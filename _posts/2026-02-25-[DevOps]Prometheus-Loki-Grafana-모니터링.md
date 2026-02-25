---
title: "[DevOps] Prometheus + Loki + Grafana 모니터링 스택 구축"
description: "Prometheus, Thanos, Loki, Alloy, Grafana로 구성한 Kubernetes 모니터링 파이프라인"
categories: DevOps Monitoring
tags: Prometheus Loki Grafana Thanos Kubernetes
date: 2026-02-25
comments: true
---

# Kubernetes 모니터링 스택

## 스택 구성

```
Alloy (DaemonSet) ──로그──▶ Loki ──▶ NCP Object Storage (S3)
       │                             │
       │                             ▼
   Pod 로그 파싱              Grafana (대시보드)
   커스텀 메트릭 생성                ▲
       │                             │
       ▼                             │
  Prometheus ──메트릭──▶ Thanos ─────┘
                        (장기 보관)
```

## 컴포넌트

| 컴포넌트 | 역할 | 스토리지 |
|----------|------|----------|
| Prometheus | 메트릭 수집 (qgx 네임스페이스 Pod 스크래핑) | NKS Block 50Gi, 30일 |
| Thanos | 장기 메트릭 보관 (Query, StoreGateway, Compactor) | NCP Object Storage |
| Loki | 로그 집계 (SingleBinary, TSDB v13) | NCP Object Storage (S3) |
| Alloy | 로그 수집 + JSON 파싱 + 커스텀 Prometheus 메트릭 생성 | - |
| Grafana | 대시보드 (Loki + Prometheus + Thanos + MySQL 데이터소스) | NKS Block 10Gi |
| Memcached | Loki 캐시 (chunks 2Gi, results 512Mi) | - |

모니터링 컴포넌트는 `role: monitoring` taint가 설정된 **전용 노드**에서 실행.

## Thanos 장기 보관

Prometheus Sidecar가 Object Storage로 업로드. 보관 정책:
- Raw 데이터: 90일
- 5분 다운샘플링: 365일
- 1시간 다운샘플링: 무제한

## Alloy 로그 파이프라인

DaemonSet으로 모든 노드에서 실행. 백엔드 서비스 로그를 수집하는 파이프라인:

1. **Pod Discovery** — 대상 네임스페이스에서 서비스 필터링
2. **JSON 파싱** — traceId, level, logger, message, httpMethod, uri, statusCode, duration 추출
3. **라벨 부여** — cluster, env, level 등 Loki 라벨 생성
4. **커스텀 메트릭 생성** — 로그에서 Prometheus 메트릭 자동 추출:
   - `qgx_http_requests_total` (counter) — HTTP 요청 수
   - `qgx_http_request_duration_ms` (histogram, 10~10000ms 버킷) — 응답 시간
   - `qgx_http_errors_total` (counter) — 에러 수
5. **Loki Write** — 가공된 로그를 Loki Gateway로 전송

## Grafana 대시보드

| 대시보드 | 데이터소스 | 내용 |
|----------|-----------|------|
| QGX Backend Logs | Loki | 레벨별 로그 볼륨, 로그 스트림, 테넌트/서비스 필터 |
| QGX Pod Monitoring | Prometheus | Pod 상태, CPU/메모리/네트워크, 재시작 현황 |
| QGX Event Monitoring | Prometheus | 이벤트 성공/실패율, 타입별 분석 |
| QGX Raw Message Status | MySQL | 메시지 처리 상태, Loki 드릴다운 연동 |

## 핵심 설계 포인트

1. **로그에서 메트릭 추출**: Alloy가 JSON 로그를 파싱하여 Prometheus 메트릭을 자동 생성하므로, 애플리케이션 코드 수정 없이 HTTP 메트릭 수집 가능
2. **장기 보관 분리**: Thanos를 통해 Prometheus의 단기 보관 한계를 극복하고 Object Storage로 장기 보관
3. **전용 노드**: 모니터링 워크로드가 애플리케이션에 영향을 주지 않도록 taint/toleration으로 격리
4. **멀티 데이터소스**: Grafana에서 Loki(로그) + Prometheus(메트릭) + MySQL(비즈니스)을 통합 대시보드로 제공
