---
title: "[DevOps] Argo Rollouts Blue-Green 배포 전략"
description: "Argo Rollouts 기반 Blue-Green 배포와 K6 Smoke Test 자동화"
categories: DevOps Kubernetes
tags: Argo-Rollouts Blue-Green K6 Kubernetes
date: 2026-02-20
comments: true
---

# 배포 전략 (Blue-Green)

## 개요

모든 애플리케이션 서비스에 Argo Rollouts Blue-Green 전략 적용. 새 버전 배포 시 Preview 환경에서 자동 테스트 후 수동 승격.

## 배포 흐름

```
1. 초기 상태
   Active (v1) ◀── ALB/Users

2. 새 버전 배포
   Active (v1) ◀── ALB/Users
   Preview(v2) ◀── K6 Smoke Test (자동)

3. 테스트 통과 → 수동 Promote
   Active (v2) ◀── ALB/Users
   Old (v1)    → 30초 후 Scale Down
```

## 주요 설정

- `autoPromotionEnabled: false` — 테스트 통과 후에도 수동 승격 필요
- `scaleDownDelaySeconds: 30` — 이전 버전 30초 후 제거
- `prePromotionAnalysis` — K6 Smoke Test 자동 실행

## K6 Smoke Test

배포 시 Preview 서비스를 대상으로 자동 실행되는 테스트:

1. Health Check (최대 60초 대기)
2. 로그인 테스트 (JWT 토큰 발급)
3. 인증된 API 호출 테스트

- VU 1명, 1회 실행, 전체 통과 필수 (`checks rate==1.0`)
- 실패 시 자동 롤백, Preview Scale Down
- 서비스별 테스트 코드는 ConfigMap으로 관리

## 운영 명령어

```bash
# 배포 상태 확인
kubectl argo rollouts get rollout <service> -n <namespace> -w

# 수동 승격
kubectl argo rollouts promote <service> -n <namespace>

# 긴급 롤백
kubectl argo rollouts undo <service> -n <namespace>
```

## Service 구성

Blue-Green 배포를 위해 각 서비스마다 2개의 Service 리소스를 생성:

1. **service-active.yaml**: NodePort — 로드밸런서가 바라보는 실제 트래픽 서비스
2. **service-preview.yaml**: ClusterIP — 내부 테스트용 서비스 (K6 테스트 대상)

## Rollout + HPA 호환

Argo Rollouts는 내부적으로 ReplicaSet을 관리하므로 HPA와 자연스럽게 호환된다.

```yaml
# HPA의 scaleTargetRef
scaleTargetRef:
  apiVersion: argoproj.io/v1alpha1
  kind: Rollout
  name: my-service
```

- HPA가 Rollout의 replicas를 조절
- Blue-Green 배포 중에도 HPA가 독립적으로 동작
- Preview Pod도 별도로 스케일링 가능
