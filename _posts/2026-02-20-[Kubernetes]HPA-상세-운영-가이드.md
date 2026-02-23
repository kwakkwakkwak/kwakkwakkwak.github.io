---
title: "[Kubernetes] HPA 상세 운영 가이드"
description: "Kubernetes HPA의 동작 원리, Pod 수 계산 공식, 스케일링 타임라인, 트러블슈팅까지"
categories: Kubernetes DevOps
tags: Kubernetes HPA autoscaling
date: 2026-02-20
comments: true
---

# HPA (Horizontal Pod Autoscaler) 상세 가이드

## 현재 HPA 설정 개요

### 서비스별 설정값

| 서비스 | HPA | minReplicas | maxReplicas | CPU Target |
|--------|-----|-------------|-------------|------------|
| Frontend | O | 2 | 5 | 70% |
| Backend API | O | 2 | 5 | 70% |
| Data Ingestion | O | 2 | 5 | 70% |
| Message Processor | O | 2 | 5 | 70% |
| AI Service | O | 1 | 3 | 60% |
| Admin Frontend | X | 2 (고정) | - | - |
| Admin Backend | X | 2 (고정) | - | - |

> **참고**: Memory 메트릭은 제거됨. JVM 기반 서비스는 메모리를 일정하게 유지하므로 CPU 기반 스케일링만 사용.

### 서비스별 리소스 설정

| 서비스 | CPU Request | CPU Limit | Memory Request | Memory Limit |
|--------|-------------|-----------|----------------|--------------|
| Frontend | 10m | 100m | 32Mi | 128Mi |
| Backend API | 100m | 2000m | 768Mi | 1536Mi |
| Data Ingestion | 200m | 2000m | 512Mi | 1Gi |
| Message Processor | 100m | 2000m | 768Mi | 1536Mi |
| AI Service | 100m | 6000m | 2304Mi | 6Gi |

---

## HPA 동작 원리

### 기본 개념

HPA는 **Metrics Server**에서 수집한 메트릭을 기반으로 **자동으로 Pod 수를 조절**하여 리소스 사용률을 목표 값으로 유지한다.

```
┌─────────────────────────────────────────────────────────────┐
│  HPA Controller (15초마다 실행)                              │
│                                                              │
│  1. Metrics Server에서 현재 사용률 조회                      │
│  2. 목표 사용률(70%)과 비교                                  │
│  3. 필요한 Pod 수 계산                                       │
│  4. Rollout에 replicas 조정 요청                             │
└─────────────────────────────────────────────────────────────┘
```

### Pod 수 계산 공식

```
desiredReplicas = ceil[currentReplicas * (currentMetricValue / targetMetricValue)]
```

**예시** (Backend API):
- 현재 Pod: 2개
- 현재 CPU 사용률: 140% (각 Pod가 평균 140m 사용, Request 100m 기준)
- 목표 CPU 사용률: 70%

```
desiredReplicas = ceil[2 * (140 / 70)] = ceil[4] = 4개
```

### 체크 주기

- **메트릭 수집 주기**: 15초 (기본값)
- **스케일아웃 반응 시간**: 15초 ~ 2분
- **스케일다운 안정화 시간**: 5분 (기본값)

---

## CPU 메트릭 상세 조건

### 계산 방식
```
CPU 사용률(%) = (실제 CPU 사용량 / CPU Request) * 100
```

### 서비스별 트리거 조건

| 서비스 | CPU Request | 임계값 | 스케일아웃 트리거 |
|--------|-------------|--------|-------------------|
| Frontend | 10m | **7m** (70%) | 각 Pod가 평균 7m 이상 사용 시 |
| Backend API | 100m | **70m** (70%) | 각 Pod가 평균 70m 이상 사용 시 |
| Data Ingestion | 200m | **140m** (70%) | 각 Pod가 평균 140m 이상 사용 시 |
| Message Processor | 100m | **70m** (70%) | 각 Pod가 평균 70m 이상 사용 시 |
| AI Service | 100m | **60m** (60%) | 각 Pod가 평균 60m 이상 사용 시 |

### 실제 동작 예시 (Backend API, Pod 2개)

**시나리오 1: 스케일아웃 발생**
```
Pod 1: 80m 사용 → 80 / 100 = 80% 사용률
Pod 2: 90m 사용 → 90 / 100 = 90% 사용률
평균 사용률: (80 + 90) / 2 = 85%

85% > 70% → 스케일아웃 트리거!
필요 Pod 수: ceil[2 * (85 / 70)] = ceil[2.43] = 3개
```

**시나리오 2: 스케일아웃 발생 안함**
```
Pod 1: 60m 사용 → 60 / 100 = 60% 사용률
Pod 2: 70m 사용 → 70 / 100 = 70% 사용률
평균 사용률: (60 + 70) / 2 = 65%

65% < 70% → 현재 상태 유지 (스케일아웃 없음)
```

---

## 리소스 최적화 원칙

| 원칙 | 설명 |
|------|------|
| **CPU Request는 낮게** | 스케줄링 유연성 확보, HPA 민감도 향상 |
| **CPU Limit은 충분히** | 버스트 트래픽 대응 |
| **Memory는 JVM 특성 반영** | Heap + Metaspace + Native 고려 |
| **Memory HPA 타겟 제거** | JVM은 메모리를 일정하게 유지하므로 비효율적 |

---

## 스케일 동작 타임라인

### 스케일아웃 (Scale Out)

```
시간축: ─────────────────────────────────────────────────►
        t0    t1    t2    t3    t4

t0 (0초): CPU 70% 초과 시작
          └─ HPA: 메트릭 수집 대기 (15초 주기)

t1 (15초): HPA가 임계값 초과 감지
           └─ 스케일아웃 결정
           └─ Rollout에 replicas 변경 요청

t2 (30초): 새 Pod 생성 시작
           └─ Container 이미지 Pull

t3 (1~2분): 새 Pod Ready 상태
            └─ Liveness/Readiness Probe 통과
            └─ Service에 Endpoint 추가

t4 (3분): 메트릭 안정화
          └─ 사용률이 70% 아래로 떨어짐
```

**총 소요 시간**: 약 **1~2분**

### 스케일다운 (Scale Down)

```
시간축: ─────────────────────────────────────────────────►
        t0    t1           t5    t6

t0 (0분): 부하 감소, CPU 70% 아래

t1~t5 (0~5분): 안정화 대기 기간 (300초)

t5 (5분): 스케일다운 결정 → Pod 종료

t6 (5분 30초): Pod 종료 완료
```

**총 소요 시간**: 최소 **5분 이상**

---

## HPA 모니터링 방법

### 1. 실시간 HPA 상태 확인

```bash
kubectl get hpa -n <namespace> -w
```

**출력 예시**:
```
NAME            REFERENCE              TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
backend         Rollout/backend        45%/70%   2         5         2          7d
frontend        Rollout/frontend       23%/70%   2         5         2          7d
ingestion       Rollout/ingestion      62%/70%   2         5         2          7d
processor       Rollout/processor      55%/70%   2         5         2          7d
ai-service      Rollout/ai-service     50%/60%   1         3         1          7d
```

### 2. HPA 상세 정보

```bash
kubectl describe hpa <service-name> -n <namespace>
```

### 3. HPA 이벤트 로그

```bash
kubectl get events -n <namespace> --sort-by='.lastTimestamp' | grep HorizontalPodAutoscaler
```

---

## 트러블슈팅

### 문제 1: HPA가 메트릭을 못 가져옴

**증상**: `TARGETS: <unknown>/70%`

**해결**:
```bash
kubectl get deployment metrics-server -n kube-system
kubectl rollout restart deployment/metrics-server -n kube-system
```

### 문제 2: HPA가 스케일하지 않음

**원인 1**: Resources requests 미설정
**원인 2**: scaleTargetRef 잘못 설정
**확인**: `kubectl describe hpa -n <namespace>`

### 문제 3: 너무 자주 스케일아웃

**원인**: CPU Request 값이 너무 낮음
**해결**: CPU Request 값 증가

---

## 참고 자료

- [Kubernetes HPA 공식 문서](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Argo Rollouts HPA 통합](https://argoproj.github.io/argo-rollouts/features/hpa-support/)
