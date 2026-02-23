---
title: "[DevOps] Vaultwarden으로 팀 비밀번호 관리하기"
description: "Self-hosted Vaultwarden(Bitwarden 호환)으로 팀 비밀번호를 안전하게 관리하는 방법"
categories: DevOps Security
tags: Vaultwarden Bitwarden password-manager Kubernetes
date: 2026-02-20
comments: true
---

# Vaultwarden 사용 가이드

> 팀 비밀번호 관리를 위한 Self-hosted 솔루션

## 시작하기

### 계정 생성 (초대받은 경우)

1. 초대 이메일에서 **Join Organization Now** 클릭
2. 이메일, 이름, 마스터 비밀번호 입력
3. **Create Account** 클릭

> **마스터 비밀번호**: 모든 비밀번호를 보호하는 핵심 비밀번호입니다. 분실 시 복구 불가능하므로 안전하게 보관하세요.

### 브라우저 확장 프로그램 설치

1. 브라우저 확장 스토어에서 **Bitwarden** 검색 후 설치
   - [Chrome 웹 스토어](https://chrome.google.com/webstore)
   - [Firefox Add-ons](https://addons.mozilla.org)
2. 확장 프로그램 아이콘 클릭 → **Self-hosted** 선택
3. Server URL에 자체 호스팅 URL 입력 (예: `https://vault.example.com`)
4. 이메일, 마스터 비밀번호로 로그인

### 모바일 앱 설치

1. App Store / Play Store에서 **Bitwarden** 설치
2. 앱 실행 → **Self-hosting** 또는 **자체 호스팅** 선택
3. Server URL 입력
4. 로그인

---

## 비밀번호 저장하기

### 웹에서 직접 추가

1. Vaultwarden 웹 UI 로그인
2. **+ New** → **Item** 클릭
3. 정보 입력:
   - **Name**: 서비스 이름 (예: ArgoCD)
   - **Username**: 사용자명
   - **Password**: 비밀번호
   - **URI**: 웹사이트 주소
4. **Owner** 선택:
   - 본인 계정: 개인 보관
   - 조직 이름: 팀 공유
5. **Collection** 선택 (조직 저장 시)
6. **Save** 클릭

### 브라우저 확장으로 자동 저장

1. 웹사이트에서 로그인
2. Bitwarden 팝업 알림 → **Save** 클릭
3. 필요시 조직/컬렉션 지정

---

## 비밀번호 사용하기

### 자동 완성 (브라우저)

1. 로그인 페이지 방문
2. Bitwarden 확장 아이콘 클릭
3. 해당 사이트 항목 클릭 → 자동 입력

또는:
- 단축키 `Ctrl+Shift+L` (Windows) / `Cmd+Shift+L` (Mac)

### 수동 복사

1. Bitwarden 확장/웹 열기
2. 항목 찾기
3. 복사 아이콘 클릭 (사용자명/비밀번호)

---

## 조직 (팀 공유)

### 조직 구조

```
조직 (Organization)
└── 컬렉션 (Collection) - 폴더 개념
    └── 항목 (Item) - 개별 비밀번호
```

### 컬렉션 예시

| 컬렉션 | 용도 |
|--------|------|
| 인프라 | ArgoCD, Grafana, 클라우드 콘솔 |
| 개발도구 | GitHub, GitLab, Jira |
| 데이터베이스 | MySQL, Redis, MongoDB |
| 공용계정 | 공유 이메일, SNS |

### 권한 유형

| 역할 | 권한 |
|------|------|
| Owner | 전체 관리 (조직 삭제 가능) |
| Admin | 멤버/컬렉션 관리 |
| Manager | 컬렉션 내 항목 관리 |
| User | 읽기/쓰기 |

---

## 멤버 관리 (관리자용)

### 멤버 초대

1. 조직 페이지 → **Members** 탭
2. **Invite Member** 클릭
3. 이메일 입력, 역할/컬렉션 선택
4. **Save** 클릭 → 초대 메일 발송

### 멤버 승인

1. 초대받은 사용자가 가입 완료
2. **Members** 탭에서 "Needs confirmation" 확인
3. 해당 멤버 옆 메뉴 → **Confirm** 클릭

### 컬렉션 생성

1. 조직 페이지 → **Vault** 탭
2. **+ New** → **Collection** 클릭
3. 이름 입력, 접근 권한 설정
4. **Save** 클릭

---

## 보안 권장사항

### 마스터 비밀번호

- 최소 12자 이상
- 영문 대/소문자, 숫자, 특수문자 조합
- 다른 곳에서 사용하지 않는 고유한 비밀번호
- 절대 공유하지 않기

### 2단계 인증 (2FA)

1. 웹에서 **Settings** → **Security** → **Two-step login**
2. **Authenticator App** 선택
3. Google Authenticator, Authy 등으로 QR 코드 스캔
4. 인증 코드 입력 → 활성화

### 세션 관리

- 공용 PC에서는 반드시 로그아웃
- **Settings** → **Sessions**에서 활성 세션 확인/종료

---

## 자주 묻는 질문

### Q: 마스터 비밀번호를 잊었어요
A: 복구 불가능합니다. 계정을 새로 만들어야 합니다. 마스터 비밀번호는 안전한 곳에 별도 보관하세요.

### Q: 오프라인에서도 사용 가능한가요?
A: 네. 브라우저 확장/앱이 로컬에 암호화된 복사본을 저장합니다.

### Q: 비밀번호가 유출될 위험은 없나요?
A: 모든 데이터는 마스터 비밀번호로 암호화되어 저장됩니다. 서버 관리자도 내용을 볼 수 없습니다.

### Q: 여러 기기에서 동기화되나요?
A: 네. 로그인한 모든 기기에서 자동 동기화됩니다.

## Kubernetes에서 Vaultwarden 운영

Vaultwarden은 Kubernetes에서 `tools` 네임스페이스에 LoadBalancer 타입 Service로 배포하여 외부에서 직접 접근 가능하도록 구성했다. 가벼운 Rust 구현체라 리소스 소비가 적고, 공식 Bitwarden 클라이언트와 완벽 호환된다.
