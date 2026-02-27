---
title: "[Kubernetes] ConfigMap + SealedSecrets로 설정 관리하기"
description: "Kubernetes에서 ConfigMap과 Sealed Secrets를 활용한 환경변수 관리 아키텍처와 실전 가이드"
categories: Kubernetes DevOps
tags: Kubernetes ConfigMap SealedSecrets Helm ArgoCD
date: 2026-02-25
comments: true
---

# ConfigMap + Sealed Secrets 설정 관리

## 문제

모든 서비스의 시크릿(DB 비밀번호, API 키 등)이 Spring Boot `application.yml`에 **평문으로 하드코딩**되어 있었다.

## 목표

- 비민감 설정 → ConfigMap (Git에 평문 OK)
- 민감 설정 → SealedSecret (Git에 암호화 저장, 클러스터 내 복호화)
- **개발자(앱 설정)와 DevOps(인프라 설정) 완전 분리** → 별도 Git 레포

## 아키텍처

```
[gitops 레포] (DevOps 영역)              [configs 레포] (설정 영역)
├── charts/<service>/                     ├── <service>/
│   ├── values.yaml (배포 설정)           │   ├── config.yaml (비민감 설정)
│   └── templates/                        │   └── secrets.yaml (암호화 시크릿)
│       ├── rollout.yaml                  ├── manage-env.sh   (자동화)
│       ├── configmap.yaml                └── seal-all.sh     (일괄 암호화)
│       └── sealedsecret.yaml
├── argo-apps/
│   └── clusters/prod/
│       └── <service>.yaml
│           (ArgoCD Multiple Sources)
```

### 데이터 흐름

```
configs 레포의 secrets.yaml (암호화 값)
    ↓ ArgoCD Multiple Sources로 values 병합
gitops 레포의 sealedsecret.yaml 템플릿 → SealedSecret 리소스 생성
    ↓ Sealed Secrets 컨트롤러가 복호화
일반 Secret 자동 생성
    ↓ rollout.yaml의 envFrom → secretRef
Pod 시작 시 환경변수로 주입 (SPRING_DATASOURCE_URL=jdbc:mysql://...)
```

## 핵심 설계

- **envFrom 방식**: 템플릿 수정 없이 키-값 추가만으로 환경변수 확장
- **checksum 어노테이션**: 설정 변경 시 Pod 자동 롤아웃
- **레포 분리**: 인프라(gitops)와 설정(configs)의 관심사 분리
- **Sealed Secrets**: Git에 암호화 저장, 클러스터 내에서만 복호화

---

## 로컬 개발 vs 배포 환경 차이

```
[로컬 개발]
application-local.yml에 DB 주소, 비밀번호 직접 작성
→ IDE에서 바로 실행

[클라우드 배포]
application-prod.yml에서 환경변수 참조 (${SPRING_DATASOURCE_URL} 등)
→ K8s가 ConfigMap/Secret에서 환경변수 주입 → Pod 시작
```

---

## 설정 변경 시나리오별 가이드

### 1. 비민감 설정 변경

**예시**: Backend의 HikariCP max pool size를 5 → 10으로 변경

```yaml
# configs-repo/<service>/config.yaml
config:
  SPRING_PROFILES_ACTIVE: "prod"
  TZ: "Asia/Seoul"
  MY_HIKARI_MAXIMUMPOOLSIZE: "10"   # 5 → 10 변경
```

```bash
git add <service>/config.yaml
git commit -m "feat: backend HikariCP max pool size 10으로 증가"
git push
# ArgoCD가 자동 감지 → ConfigMap 업데이트 → Pod 재시작
```

**끝!** 로컬 코드 변경 없음.

### 2. 민감 설정 변경 (DB 비밀번호 등)

```bash
# Bastion에서 새 비밀번호를 kubeseal로 암호화
echo -n 'NewPassword123!' | kubeseal --raw \
  --from-file=/dev/stdin \
  --namespace <namespace> \
  --name <service>-secret \
  --cert sealed-secrets-pub-cert.pem

# 출력: AgCEW/aoOTzri8aC... (암호화된 값)
```

```yaml
# configs-repo/<service>/secrets.yaml
sealedSecrets:
  SPRING_DATASOURCE_PASSWORD: AgCEW/aoOTzri8aC...  # 새 암호화 값으로 교체
```

```bash
git commit -m "fix: backend DB 비밀번호 변경"
git push
# ArgoCD → SealedSecret 업데이트 → Secret 재생성 → Pod 재시작
```

### 3. 새 환경변수 추가

#### Step 1: Spring Boot에서 환경변수 참조하도록 수정
```yaml
# application-prod.yml (앱 레포)
my-service:
  api-key: ${MY_SERVICE_API_KEY}
```

#### Step 2: 민감한 값이면 → secrets.yaml에 추가
```bash
# Bastion에서 암호화
echo -n 'actual-api-key-value' | kubeseal --raw \
  --from-file=/dev/stdin \
  --namespace <namespace> \
  --name <service>-secret \
  --cert sealed-secrets-pub-cert.pem
```

```yaml
# configs-repo/<service>/secrets.yaml
sealedSecrets:
  MY_SERVICE_API_KEY: AgBx...  # 암호화 값 추가
```

#### Step 2 (대안): 민감하지 않은 값이면 → config.yaml에 추가
```yaml
# configs-repo/<service>/config.yaml
config:
  MY_SERVICE_URL: "https://api.example.com"  # 평문으로 추가
```

---

## 자동화 스크립트 (manage-env.sh)

Bastion에서 실행하는 파이프라인 스크립트:

```bash
# 일반 설정 추가/수정 (ConfigMap)
./manage-env.sh config <service> <key> <value>

# 민감 정보 추가/수정 (SealedSecret — 자동 암호화)
./manage-env.sh secret <service> <key> <value>

# 환경변수 삭제
./manage-env.sh delete <service> <key>
```

**동작**: 값 암호화(secret만) → YAML 자동 업데이트 → diff 확인 → git commit & push → ArgoCD sync

## 환경변수 이름 규칙 (Relaxed Binding)

Spring Boot는 **Relaxed Binding**이라는 규칙으로 `application.yml`의 프로퍼티를 환경변수에 매핑한다. ConfigMap/Secret에 환경변수를 등록할 때 이 규칙을 따라야 Spring이 값을 인식한다.

### 변환 규칙

1. `.`(점) → `_`(언더스코어)로 치환
2. `-`(하이픈) → 제거
3. 전부 **대문자**로 변환

### 변환 예시

| application.yml 경로 | 환경변수 이름 | 적용 규칙 |
|----------------------|--------------|-----------|
| `server.port` | `SERVER_PORT` | `.` → `_`, 대문자 |
| `spring.datasource.url` | `SPRING_DATASOURCE_URL` | `.` → `_`, 대문자 |
| `spring.datasource.username` | `SPRING_DATASOURCE_USERNAME` | `.` → `_`, 대문자 |
| `jwt.token.access-secret` | `JWT_TOKEN_ACCESSSECRET` | `.` → `_`, `-` 제거, 대문자 |
| `spring.jpa.show-sql` | `SPRING_JPA_SHOWSQL` | `.` → `_`, `-` 제거, 대문자 |
| `my.hikari.maximum-pool-size` | `MY_HIKARI_MAXIMUMPOOLSIZE` | `.` → `_`, `-` 제거, 대문자 |
| `my.tenant.routes.COMPANY.jdbc-url` | `MY_TENANT_ROUTES_COMPANY_JDBCURL` | `.` → `_`, `-` 제거, 대문자 |

### 실전 적용

```yaml
# application-prod.yml (앱 레포)
spring:
  datasource:
    url: ${SPRING_DATASOURCE_URL}          # ConfigMap/Secret에서 주입
    username: ${SPRING_DATASOURCE_USERNAME}
    password: ${SPRING_DATASOURCE_PASSWORD}
  jpa:
    show-sql: ${SPRING_JPA_SHOWSQL:false}  # 기본값 지정 가능
```

```yaml
# configs-repo/<service>/config.yaml
config:
  SPRING_JPA_SHOWSQL: "false"
```

```yaml
# configs-repo/<service>/secrets.yaml
sealedSecrets:
  SPRING_DATASOURCE_URL: AgCEW/ao...      # kubeseal로 암호화된 값
  SPRING_DATASOURCE_USERNAME: AgBx/kL...
  SPRING_DATASOURCE_PASSWORD: AgDf/mN...
```

환경변수 이름만 규칙에 맞추면 Spring Boot가 자동으로 프로퍼티에 바인딩한다. `application.yml`에서 `${ENV_VAR}` 플레이스홀더를 쓰든, Relaxed Binding으로 자동 매핑하든 둘 다 동작한다.

---

## 서비스별 설정 현황

| 서비스 | ConfigMap 키 | SealedSecret 키 |
|--------|-------------|----------------|
| Backend API | 프로필, TZ, HikariCP, Prometheus URL | DB 3종, JWT 2종, 암호화 키, 멀티테넌트 DB 6종 |
| Data Ingestion | 프로필, TZ, Kafka, Processor URL | DB 3종 |
| Message Processor | 프로필, TZ, Kafka, OpenSearch 호스트 | DB 3종, OpenSearch 2종 |
| Admin Backend | 프로필, TZ, API URL, Prometheus URL | DB 3종, JWT 2종 |
| Frontend | BUILD_MODE, TZ | - |
| Admin Frontend | BUILD_MODE, TZ | - |
| AI Service | 환경, TZ, LLM, LangChain, CORS | API 키, DB URL 등 6종 |
| AI Web | BUILD_MODE, TZ, VITE_API_URL | - |

---

## 프론트엔드 런타임 환경변수 주입 (window.ENV)

React/Vite 앱은 빌드 타임에 `import.meta.env`로 환경변수를 bake-in하지만, 이미지 재빌드 없이 환경변수를 바꾸려면 런타임 주입이 필요하다.

```
ConfigMap (VITE_API_URL 등)
  → Pod 환경변수 주입 (envFrom)
  → docker-entrypoint.sh가 VITE_* 환경변수 → /usr/share/nginx/html/env.js 생성
  → index.html에서 <script src="/env.js"> 로드
  → JS 코드에서 window.ENV?.API_URL || import.meta.env.VITE_API_URL 폴백
```

이 패턴을 사용하면 하나의 Docker 이미지로 환경별(dev, staging, prod) 설정만 바꿔서 배포할 수 있다.

---

## 배포 순서 (점진적)

1. **Phase 0**: Sealed Secrets 컨트롤러 배포
2. **Phase 1**: kubeseal 암호화 + secrets.yaml 완성
3. **Phase 2-a**: Admin Frontend (ConfigMap만, 시크릿 없음, 가장 안전)
4. **Phase 2-b**: Frontend (ConfigMap만)
5. **Phase 2-c**: Admin Backend (ConfigMap + SealedSecret, 낮은 트래픽)
6. **Phase 2-d**: Backend API (핵심 서비스, 신중하게)
7. **Phase 2-e**: Ingestion + Processor
8. **Phase 2-f**: AI Service

## 검증 (각 서비스 배포 후)

```bash
# ConfigMap 생성 확인
kubectl get configmap <service>-config -n <namespace> -o yaml

# SealedSecret → Secret 복호화 확인
kubectl get secret <service>-secret -n <namespace>

# Pod 환경변수 주입 확인
kubectl exec -it <pod> -n <namespace> -- env | grep SPRING_DATASOURCE

# 서비스 정상 동작 확인
curl http://<endpoint>/actuator/health
```

## 주의사항

1. **seal-all.sh는 절대 원격에 push하지 말 것** — 평문 비밀번호가 들어있음
2. **sealed-secrets-pub-cert.pem** — 공개키이므로 push해도 무방하지만, 클러스터가 바뀌면 새로 발급 필요
3. **암호화 값은 클러스터+네임스페이스에 종속** — 다른 클러스터에서는 복호화 불가
4. **config.yaml 변경 시 Pod 자동 재시작** — rollout.yaml에 checksum 설정 있음
5. **secrets.yaml 변경 시에는 수동 재시작 필요할 수 있음** — `kubectl rollout restart`
