---
title: "[Kubernetes] HPA와 Argo Rollout 전환 사례"
description: "5개 서비스에 HPA 전면 활성화 및 Argo Rollouts Blue-Green 배포 통일 작업 리뷰"
categories: Kubernetes DevOps
tags: Kubernetes HPA Argo-Rollouts Blue-Green
date: 2026-02-20
comments: true
---

# HPA & Rollout 전환 작업 상세 리뷰

## 작업 개요

### 목적
1. **클라우드 인증 준비**: "신속한 탄력성(Rapid Elasticity)" 증빙
2. **HPA 전면 활성화**: 모든 서비스에 자동 스케일링 적용
3. **배포 방식 통일**: Rollout 기반 Blue-Green 무중단 배포로 통일

### 적용 대상 서비스 (5개)
- Frontend (React/Nginx)
- Backend API (Spring Boot)
- Data Ingestion (Netty)
- Message Processor (Kafka Consumer)
- AI Service (Python)

---

## HPA 설정 활성화 및 전체 서비스 최적화

### Values.yaml 변경
```yaml
# 변경 전
autoscaling:
  enabled: false
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 70

# 변경 후
autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80  # Memory 메트릭 추가
```

### HPA 템플릿 개선

기존에는 CPU 메트릭만 하드코딩되어 있었지만, 조건부 렌더링 방식으로 개선했다.

```yaml
# 변경 전
metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: {% raw %}{{ .Values.autoscaling.targetCPUUtilizationPercentage | default 70 }}{% endraw %}

# 변경 후
metrics:
  {% raw %}{{- if .Values.autoscaling.targetCPUUtilizationPercentage }}{% endraw %}
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: {% raw %}{{ .Values.autoscaling.targetCPUUtilizationPercentage }}{% endraw %}
  {% raw %}{{- end }}{% endraw %}
  {% raw %}{{- if .Values.autoscaling.targetMemoryUtilizationPercentage }}{% endraw %}
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: {% raw %}{{ .Values.autoscaling.targetMemoryUtilizationPercentage }}{% endraw %}
  {% raw %}{{- end }}{% endraw %}
```

**개선 포인트**:
- 조건부 렌더링: 값이 설정된 메트릭만 생성
- 이중 메트릭 모니터링: CPU 또는 Memory 중 하나라도 임계값 초과 시 스케일아웃
- 하드코딩 제거: default 값 제거, 명시적 설정만 사용

---

## HPA scaleTargetRef 수정

### 문제 발견
```
deployments.apps "my-frontend" not found
```

### 원인
Frontend는 **Rollout**을 사용하는데 HPA가 **Deployment**를 참조하고 있었다.

### 해결
```yaml
# 변경 전
scaleTargetRef:
  apiVersion: apps/v1
  kind: Deployment
  name: {% raw %}{{ include "my-frontend.fullname" . }}{% endraw %}

# 변경 후
scaleTargetRef:
  apiVersion: argoproj.io/v1alpha1
  kind: Rollout
  name: {% raw %}{{ .Values.service.name }}{% endraw %}
```

### 교훈
- 초기 HPA 설정 시 배포 방식(Deployment vs Rollout)을 제대로 확인해야 한다
- 런타임 오류로 발견되므로 사전 검증이 중요

---

## AI Service Rollout 전환

### 변경 내용
- 삭제: `deployment.yaml`, `service.yaml`
- 추가: `rollout.yaml`, `service-active.yaml`, `service-preview.yaml`
- 추가: `analysis-template.yaml`, `test-configmap.yaml`
- 추가: `tests/main.js`

### Rollout 설정
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
spec:
  replicas: 2
  strategy:
    blueGreen:
      activeService: my-ai
      previewService: my-ai-preview
      autoPromotionEnabled: false           # 수동 승인
      scaleDownDelaySeconds: 30
      prePromotionAnalysis:                 # K6 자동 테스트
        templates:
          - templateName: "my-ai-k6-smoke-test"
```

### K6 Smoke Test
```javascript
export default function () {
  const TARGET_URL = __ENV.TARGET_URL || 'http://my-ai:8000';
  const res = http.get(`${TARGET_URL}/health`, { timeout: '10s' });

  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 5s': (r) => r.timings.duration < 5000,
  });
}
```

---

## 검증 결과

### HPA 설정 상태

| 서비스 | enabled | minReplicas | maxReplicas | CPU Target | Memory Target |
|--------|---------|-------------|-------------|------------|---------------|
| Frontend | true | 2 | 5 | 80% | 80% |
| Backend | true | 2 | 5 | 80% | 80% |
| Ingestion | true | 2 | 5 | 80% | 80% |
| Processor | true | 2 | 5 | 80% | 80% |
| AI Service | true | 2 | 5 | 80% | 80% |

### 배포 방식

| 서비스 | 배포 방식 | HPA scaleTargetRef | Blue-Green | K6 Test |
|--------|-----------|-------------------|------------|---------|
| Frontend | Rollout | kind: Rollout | O | O |
| Backend | Rollout | kind: Rollout | O | O |
| Ingestion | Rollout | kind: Rollout | O | O |
| Processor | Rollout | kind: Rollout | O | O |
| AI Service | Rollout | kind: Rollout | O | O |

### 리소스 설정

| 서비스 | CPU Request | Memory Request | CPU Limit | Memory Limit |
|--------|-------------|----------------|-----------|--------------|
| Frontend | 100m | 128Mi | 500m | 512Mi |
| Backend | 500m | 512Mi | 1000m | 1Gi |
| Ingestion | 500m | 512Mi | 1000m | 1Gi |
| Processor | 500m | 512Mi | 1000m | 1Gi |
| AI Service | 500m | 512Mi | 1000m | 1Gi |

**중요**: HPA가 동작하려면 `resources.requests` 필수

---

## 발견된 문제점 및 개선 사항

### Frontend 리소스 설정이 과소할 수 있음

```yaml
# 현재 설정
resources:
  requests:
    cpu: 100m      # 너무 낮음
    memory: 128Mi

# 권장
resources:
  requests:
    cpu: 200m
    memory: 256Mi
```

CPU Request가 100m인 경우, 80% 트리거 시 **80m만 사용해도 스케일아웃**되어 불필요한 스케일아웃 가능성이 있다.

---

## 예상 동작 시나리오

### HPA 스케일아웃 시나리오

1. **초기 상태**: Pod 2개 (minReplicas)
2. **부하 증가**: CPU 사용률 80% 초과 또는 Memory 사용률 80% 초과
3. **HPA 트리거**: 평균 사용률 80% 초과 감지 (15초 주기)
4. **스케일아웃**: Pod 3개로 증가 (약 30초 소요)
5. **부하 지속**: CPU/Memory 여전히 80% 초과 시 Pod 4~5개까지 증가
6. **부하 감소**: 사용률 정상화
7. **스케일다운**: **5분 대기 후** Pod 2개로 복귀

### Blue-Green 배포 시나리오

1. **이미지 태그 변경**: `values.yaml` 수정 후 Git Push
2. **ArgoCD Sync**: Rollout 리소스 업데이트
3. **Preview 생성**: 새 버전 Pod 2개 생성
4. **K6 Test 실행**: AnalysisTemplate에 의해 자동 실행
5. **Test 성공**: 수동 Promote 대기 (autoPromotionEnabled: false)
6. **Traffic Switch**: ALB가 Preview로 전환 (Blue -> Green)
7. **Old Version 제거**: 30초 후 이전 버전 Pod 삭제

---

## 잠재적 리스크 및 대응 방안

| 리스크 | 증상 | 대응 |
|--------|------|------|
| Metrics Server 미설치 | `kubectl top` 실패 | Metrics Server 설치 |
| 과도한 스케일아웃 | CPU Request가 너무 낮음 | Request 값 증가 |
| 스케일다운 지연 | 부하 감소 후 5분 대기 | 충분한 대기 시간 확보 |
| Blue-Green 배포 실패 | K6 테스트 실패 | 자동 롤백 (Preview 삭제, Active 유지) |

---

## 배운 점

1. **배포 방식 사전 확인 중요성**: Deployment vs Rollout을 명확히 파악해야 HPA 설정 가능
2. **이중 메트릭의 가치**: CPU만으로는 부족, Memory도 함께 모니터링해야 안정적
3. **조건부 렌더링**: Helm 템플릿에서 유연성 확보 중요
4. **Blue-Green + HPA 호환**: Rollout은 ReplicaSet을 관리하므로 HPA와 잘 작동
