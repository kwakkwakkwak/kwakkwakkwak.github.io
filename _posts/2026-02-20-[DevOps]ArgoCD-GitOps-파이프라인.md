---
title: "[DevOps] ArgoCD GitOps 파이프라인 구축"
description: "App of Apps 패턴, Multiple Sources, Helm 차트 기반 GitOps 파이프라인 아키텍처"
categories: DevOps GitOps
tags: ArgoCD GitOps Helm Kubernetes
date: 2026-02-20
comments: true
---

# GitOps 파이프라인

## 레포지토리 구조

```
gitops-repo/                             configs-repo/
├── argo-apps/                           ├── backend/
│   ├── clusters/prod/                   │   ├── config.yaml      (ConfigMap)
│   │   ├── app/      (8개 서비스)       │   └── secrets.yaml     (SealedSecret)
│   │   ├── monitoring/ (5개 컴포넌트)   ├── ingestion/
│   │   └── tools/    (2개)              ├── processor/
│   └── projects/                        ├── ai-service/
├── charts/                              ├── admin-backend/
│   ├── <service>*/    (서비스별 Helm)   ├── frontend/
│   ├── kafka/                           ├── admin-frontend/
│   └── monitoring/                      ├── manage-env.sh        (자동화)
│       ├── prometheus/                  └── seal-all.sh          (일괄 암호화)
│       ├── thanos/
│       ├── loki/
│       ├── alloy/
│       └── grafana/
```

## ArgoCD 구성

- **App of Apps 패턴**: `root-app`이 `argo-apps/` 전체를 재귀적으로 관리
- **프로젝트 분리**: `app` (애플리케이션), `monitoring` (모니터링), `tools` (도구)
- **Multiple Sources**: 차트(gitops-repo) + 설정값(configs-repo)을 분리하여 관심사 분리

```
ArgoCD Application
├── Source 1: gitops-repo (차트 + 템플릿)
│   └── helm.valueFiles:
│       ├── values.yaml
│       ├── $configs/<service>/config.yaml
│       └── $configs/<service>/secrets.yaml
└── Source 2: configs-repo (ref: configs)
    └── 환경변수 값만 관리
```

## 배포 워크플로우

```
1. 개발자가 앱 코드 Push
2. GitHub Actions → Docker 빌드 → GHCR Push
3. gitops-repo values.yaml에서 image.tag 업데이트 → Push
4. ArgoCD가 변경 감지 → Helm 렌더링
5. Argo Rollouts Blue-Green 배포 시작
   → Preview(Green) 생성 → K6 Smoke Test 자동 실행
   → 테스트 통과 시 수동 Promote → Active 전환
6. HPA가 트래픽에 따라 자동 스케일링
```

## Helm 차트 구성 (서비스별 공통)

각 서비스 차트는 동일한 템플릿 패턴:

```
charts/<service>/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── rollout.yaml           # Argo Rollouts (Blue-Green)
│   ├── hpa.yaml               # HorizontalPodAutoscaler
│   ├── service-active.yaml    # Active Service (NodePort)
│   ├── service-preview.yaml   # Preview Service (ClusterIP)
│   ├── configmap.yaml         # ConfigMap (envFrom)
│   ├── sealedsecret.yaml      # SealedSecret (envFrom)
│   ├── analysis-template.yaml # K6 Smoke Test
│   └── test-configmap.yaml    # K6 테스트 스크립트
└── tests/                     # K6 테스트 코드
```

### 핵심 설계 포인트

1. **관심사 분리**: 인프라 차트(gitops-repo)와 설정값(configs-repo)을 별도 레포로 관리
2. **envFrom 패턴**: ConfigMap/Secret의 모든 키를 환경변수로 자동 주입
3. **checksum 어노테이션**: 설정 변경 시 Pod 자동 롤아웃
4. **Blue-Green 표준화**: 모든 서비스가 동일한 Rollout + K6 테스트 패턴 사용
