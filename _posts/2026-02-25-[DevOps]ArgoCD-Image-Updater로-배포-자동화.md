다 커밋 ---
title: "[DevOps] ArgoCD Image Updater로 배포 자동화하기"
description: "Git 태그 기반 semver 전략으로 ArgoCD Image Updater를 적용하고, 타임스탬프 태그 이슈를 해결한 과정"
categories: DevOps GitOps
tags: ArgoCD Image-Updater GitOps GHCR semver
date: 2026-02-25
comments: true
---

# ArgoCD Image Updater로 배포 자동화하기

## 문제

기존 배포 방식은 이랬다.

1. GitHub Actions가 `latest` 태그로 이미지를 GHCR에 push
2. ArgoCD는 태그가 안 바뀌니 변경을 감지 못함
3. 사용자가 ReplicaSet을 수동 삭제해서 Pod를 재생성

`latest` 태그에 의존하다 보니 ArgoCD가 변경을 인지하지 못하고, 수동 삭제는 롤백이 불확실했다.

## 목표

**Git 태그 push만 하면 자동으로 배포까지 완료**되는 파이프라인을 만든다.

```
git tag v1.0.3 && git push origin v1.0.3
  → GHA가 이미지 빌드 → GHCR push
  → Image Updater가 새 semver 감지
  → ArgoCD Application 파라미터 오버라이드
  → Blue-Green 배포 시작 → K6 Smoke Test → 수동 Promote
```

## ArgoCD Image Updater란

ArgoCD Image Updater는 컨테이너 레지스트리를 주기적으로 폴링해서 새 이미지를 감지하면 ArgoCD Application의 이미지 태그를 자동으로 업데이트하는 도구다. semver, latest, digest 등 다양한 업데이트 전략을 지원한다.

## 설정

### Image Updater 어노테이션

ArgoCD Application에 어노테이션을 추가하면 된다.

```yaml
argocd-image-updater.argoproj.io/image-list: "back=ghcr.io/d-light-partner/qgx-back"
argocd-image-updater.argoproj.io/back.update-strategy: "semver"
argocd-image-updater.argoproj.io/back.allow-tags: "regexp:^v\\d+\\.\\d+\\.\\d+$"
argocd-image-updater.argoproj.io/back.helm.image-name: "image.repository"
argocd-image-updater.argoproj.io/back.helm.image-tag: "image.tag"
argocd-image-updater.argoproj.io/write-back-method: "argocd"
argocd-image-updater.argoproj.io/back.pull-secret: "secret:argocd/argocd-image-updater-ghcr#creds"
```

핵심 설정을 하나씩 보면:

- **`update-strategy: semver`** — semver 규칙으로 최신 버전을 판단한다. `v1.0.3` > `v1.0.2`
- **`allow-tags: regexp:^v\d+\.\d+\.\d+$`** — `v*` 형태의 semver 태그만 추적한다. 이게 왜 중요한지는 트러블슈팅에서 설명한다
- **`write-back-method: argocd`** — Application 리소스에 직접 파라미터를 오버라이드한다. GitOps 레포를 수정하지 않아서 불필요한 커밋이 생기지 않는다

### 인증 (GHCR)

GHCR은 private 레지스트리이므로 인증이 필요하다.

| 항목 | 값 |
|------|-----|
| 방식 | Classic PAT (`read:packages`) |
| Secret | `argocd-image-updater-ghcr` (namespace: argocd, key: creds) |
| 형식 | `username:token` |

> **주의**: Fine-grained PAT은 GitHub Packages 권한을 지원하지 않는다. 반드시 **Classic PAT**을 사용해야 한다.

## 배포 방법

설정이 끝나면 배포는 태그 하나로 끝난다.

```bash
# 1. develop에 코드 머지 후 태그 생성
git tag v1.0.3
git push origin v1.0.3

# 2. GHA가 자동 빌드 (build-prod.yml 트리거)
# 3. Image Updater가 2분 주기로 GHCR 폴링 → 새 semver 감지 → 자동 배포
```

Image Updater가 새 태그를 감지하면 ArgoCD Application의 `image.tag` 파라미터를 오버라이드하고, ArgoCD가 변경을 감지해서 Blue-Green 배포를 시작한다.

## 트러블슈팅

### 타임스탬프 태그가 최신 버전으로 인식되는 문제

**증상**: `v1.0.2` 태그를 push했는데 Image Updater가 감지하지 못함

**원인**: 이전 배포에서 사용하던 타임스탬프 태그(`20251114-173059`)가 semver로 파싱되어 `20251114.0.0`으로 해석됐다. `v1.0.2`보다 "최신"이라고 판단해서 업데이트할 필요가 없다고 본 것이다.

**해결**: `allow-tags` 어노테이션 추가

```yaml
argocd-image-updater.argoproj.io/back.allow-tags: "regexp:^v\\d+\\.\\d+\\.\\d+$"
```

이 정규식으로 `v`로 시작하는 semver 태그만 필터링하면 타임스탬프 태그는 무시된다. **`allow-tags`는 선택이 아니라 필수다.** 레지스트리에 다양한 형태의 태그가 섞여 있으면 반드시 필터링해야 한다.

### 구버전 이미지가 계속 배포되는 문제

**증상**: `allow-tags` 적용 후에도 이전 이미지(`20251114-173059`)가 실행됨

**원인**: ArgoCD Application에 이전 오버라이드가 남아있었다. Image Updater의 `write-back-method: argocd`는 Application 리소스에 파라미터를 오버라이드하는 방식인데, 이전에 설정된 오버라이드가 정리되지 않은 것이다.

**해결**: Application의 파라미터 오버라이드를 클리어하고 새 버전을 배포

## 변경된 파일

| 레포 | 파일 | 변경 |
|------|------|------|
| qgx-gitOps | `charts/argocd-image-updater/Chart.yaml` | Helm 차트 래퍼 |
| qgx-gitOps | `charts/argocd-image-updater/values.yaml` | GHCR 레지스트리 설정 |
| qgx-gitOps | `argo-apps/clusters/prod/tools/argocd-image-updater.yaml` | ArgoCD App |
| qgx-gitOps | `argo-apps/clusters/prod/qgx/qgx-back.yaml` | Image Updater 어노테이션 |
| qgx-gitOps | `charts/qgx-back/values.yaml` | tag를 semver로, pullPolicy: IfNotPresent |
| qgx-back | `.github/workflows/build-prod.yml` | 태그 기반 운영 빌드 워크플로우 |

## 운영 명령어

```bash
# Image Updater 로그 확인
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-image-updater -f

# 실행 중인 이미지 태그 확인
kubectl get pods -n qgx -l app.kubernetes.io/name=qgx-back \
  -o jsonpath='{.items[*].spec.containers[*].image}'
```

## PAT 만료 관리

Classic PAT은 만료 기한이 있으므로 주기적으로 갱신해야 한다.

```bash
kubectl delete secret argocd-image-updater-ghcr -n argocd
kubectl create secret generic argocd-image-updater-ghcr \
  --namespace argocd \
  --from-literal=creds='<username>:<new-token>'
```

## 다른 서비스 확장 시

현재 qgx-back에만 적용되어 있다. 다른 서비스에 확장하려면:

1. 해당 서비스 레포에 `build-prod.yml` 워크플로우 추가
2. ArgoCD Application에 Image Updater 어노테이션 추가 (**`allow-tags` 필수**)
3. `values.yaml` 이미지 태그를 semver로 변경
4. 초기 태그(`v1.0.0`) push

## 정리

| 항목 | 이전 | 현재 |
|------|------|------|
| 이미지 태그 | `latest` (고정) | semver (`v1.0.x`) |
| 변경 감지 | ArgoCD가 감지 못함 | Image Updater가 2분 주기로 폴링 |
| 배포 트리거 | ReplicaSet 수동 삭제 | `git tag` + `git push` |
| 롤백 | 불확실 | `kubectl argo rollouts undo` |

`latest` 태그 의존에서 벗어나 semver 기반 자동 배포로 전환했다. 핵심은 `allow-tags`로 태그를 필터링하는 것이다. 레지스트리에 다양한 형태의 태그가 공존하는 환경에서는 이 설정 없이 semver 전략을 쓰면 예상치 못한 버전이 "최신"으로 인식될 수 있다.
