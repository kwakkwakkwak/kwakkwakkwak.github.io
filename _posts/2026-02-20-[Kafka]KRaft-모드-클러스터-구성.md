---
title: "[Kafka] KRaft 모드 클러스터 구성"
description: "ZooKeeper 없이 KRaft 모드로 Kafka 3-node Combined 클러스터 구성하기"
categories: Kafka DevOps
tags: Kafka KRaft Kubernetes StatefulSet
date: 2026-02-20
comments: true
---

# Kafka KRaft 모드 클러스터 구성

## 구성

- **모드**: KRaft (ZooKeeper 없음)
- **노드**: 3-node Combined (Controller + Broker), StatefulSet
- **이미지**: `apache/kafka:3.9.0`
- **스토리지**: 50Gi/node (Block Storage)
- **서비스**: ClusterIP (내부 통신 전용)
- **관리**: Kafka UI (`provectuslabs/kafka-ui:v0.7.2`)

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   kafka-0   │◀──▶│   kafka-1   │◀──▶│   kafka-2   │
│  Combined   │    │  Combined   │    │  Combined   │
│ Controller  │    │ Controller  │    │ Controller  │
│ + Broker    │    │ + Broker    │    │ + Broker    │
└──────┬──────┘    └──────┬──────┘    └──────┬──────┘
       └──────────────────┼──────────────────┘
                          │
                    Headless Service
                    (kafka-headless)
```

## 설정

| 항목 | 값 |
|------|---|
| Partitions | 3 |
| Replication Factor | 3 |
| Min ISR | 2 |
| Log Retention | 72시간 |
| Log Size Limit | 1GB/partition |
| Auto Create Topics | true |
| Pod Anti-Affinity | soft (노드 분산) |

## 토픽 설계

| 토픽 | 용도 |
|------|------|
| data-ingestion | 실시간 데이터 수집 (ingestion -> processor) |
| data-processed | 처리 완료 데이터 |

## KRaft 모드의 장점

1. **ZooKeeper 제거**: 운영 복잡도 감소, 별도 ZooKeeper 클러스터 불필요
2. **Combined 모드**: Controller와 Broker를 한 노드에서 실행하여 리소스 절약
3. **빠른 리더 선출**: ZooKeeper 의존 없이 Raft 프로토콜로 직접 리더 선출
4. **StatefulSet**: Pod 재시작 시에도 안정적인 네트워크 ID와 스토리지 유지
