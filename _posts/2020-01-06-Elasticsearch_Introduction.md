---
title: Elasticsearch
excerpt : Elasticsearch Introduction
date: 2020-01-06 21:30:00
header:
  image: # /assets/images/elk-stack-elkb-diagram.svg
  caption: ""
tags:
  - elasticsearch
---
# Elasticsearch

## Elasticsearch의 기본 동작

### 인덱스 생성 및 삭제, 조회

#### 인덱스를 생성하는 세가지 방법

1. 인덱스의 settings를 정의
2. 인덱스의 mappings를 정의
   - RDB의 스키마 정의와 비슷
3. 사용자 정의된 도큐멘트를 인덱싱

#### 인덱스의 settings

1. Static index settings
   - number_of_shards : primary 샤드 갯수 설정
2. Dynamic index settings
   - number_of_replicas : replica 샤드 갯수 설정
   - refresh_interval : 시스템 OS 버퍼 캐시에 있는 데이터를 어느 주기에 세그먼트로 내릴지 설정
   - index.routing.allocation.enable : 인덱스의 샤드들의 라우팅 허용 설정
3. other settings
   - Analysis, Mapping, Slowlog...

#### 인덱스의 settings를 정의하여 인덱스 생성

- PUT Method 사용

```json
PUT twitter
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3,
            "number_of_replicas" : 1
        }
    }
}
```

> flat_settings 형태로도 인덱스 생성 가능

```json
PUT twitter
{
    "settings" : {
        "index.number_of_shards" : 3,
        "index.number_of_replicas" : 1
    }
}
```

#### 인덱스 삭제

- DELETE Method 사용

```crud
DELETE twitter
```

- nginx를 앞단에 두어 특정 IP에서만 허용하기도 한다.
- CLI 사용하기

```shell
curl -XDELETE -H 'Content-Type:application/json' http://{ES_URL}:9200/{index}
```

#### 인덱스가 존재하는지 확인

```crud
HEAD twitter
```

#### 인덱스의 세팅을 확인

```crud
GET twitter/_settings
```

#### 인덱스의 매핑을 확인

```crud
GET twitter/_mappings
GET twitter/_mapping
```

#### 인덱스의 상태를 확인(사이즈, 문서 수, 실행된 명령 정보 등)

```crud
GET twitter/_stats
```

#### 인덱스의 샤드 및 세그먼트 정보

```crud
GET twitter/_segments
```

#### 인덱스의 요약 정보

```crud
GET _cat/indices?v
GET _cat/indices/twitter?v
```

#### 샤드 할당 알고리즘

shard = hash(routing) % number_of_primary_shards

#### 인덱싱

1. primary shard가 우선 writing 되어야 한다.
2. primary shard에 writing이 완료된 후 replica shard에 복제한다.

#### 사용자 정의된 도큐먼트를 ID 지정하여 인덱싱

```json
PUT twitter/_doc/1
{
    "user" : "eddie",
    "post_date" : "2020-01-06T22:02:12",
    "message" : "elasticsearch"
}
```

- 7.x버전 부터는 _doc 이외의 타입 이름은 권고되지 않음
- 도큐멘트 ID를 지정하지 않으면 랜덤한 ID가 생성 됨

### 문서 조회

1. 조회 요청을 Round Robin으로 처리
2. 마스터 노드가 다른 노드에 요청을 전달
3. 요청 자체도 앞단에서 balancing 권고

#### 문서 ID 통해 조회

```http method
GET twitter/_doc/1
```

#### 데이터인 _source만 조회

```http
GET twitter/_source/1
```

#### 문서 ID 통해 갱신

```http
PUT twitter/_doc/1
{
    "user" : "eddie",
    "post_date" : "2020-01-06T22:02:12",
    "message" : "es"
}
```

- ES에서 문서 업데이트시 실제 데이터가 변경되는 것이 아니라 version이 증가하며 갱신되 정보가 추가 저장

#### 문서 ID 통해 삭제

```http
DELETE twitter/_doc/1
```

#### 클러스터 상태정보

```http
GET _cluster/health
```

#### 클러스터 사용자 설정 정보

```http
GET _cluster/settings
```

## 플러그인

### 플러그인이란

- ES 기능을 커스텀 설정에 의해 더 강화하여 사용
- 전체 노드에 설치해야 하는 플러그인도 있음
- 플러그인 로딩 위해 클러스터 재시작 필요
- root 계정으로 설치

### Core Plugins

- Elasticsearch에서 공식적으로 지원하는 플러그인
- ES 버전이 올라갈 때마다 같이 버전 업데이트 지원
- ES에서 공식적으로 권고

### Community contributed

- 개인 개발자나 회사에 의해 지원되는 플러그인
