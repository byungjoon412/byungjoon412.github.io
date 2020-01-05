---
title: Elasticsearch
excerpt : Elasticsearch Introduction
date: 2020-01-05 19:20:00
header:
  image: /assets/images/elk-stack-elkb-diagram.svg
  caption: ""
tags:
  - elasticsearch
---
# Elasticsearch

<!--
https://coralogix.com/wp-content/uploads/2019/03/elastic-search.png
-->
![elasticsearch](https://www.antaresnet.com/wp-content/uploads/2018/07/Elasticsearch-Logo-Color-V-768x400.png)
일래스틱서치는 Apache Lucene 기반의 텍스트, 숫자, 위치 기반 정보, 정형 및 비정형 데이터 등 모든 유형의 데이터를 위한 분산형 오픈 소스 검색 및 분석 엔진입니다.
준 실시간(NRT, Near Real Time)으로 검색할 수 있습니다.

일래스틱서치는 단독으로 사용되기도 하지만 ELK Stack의 일부로서 사용되기도 합니다.

## ELK Stack(Elasticsearch, Logstash, Kibana, Beats)

![ELK-Stack](../assets/images/ELK-Stack.png)

- Logstash : 여러 소스에서 동시에 데이터를 수집하여 변환한 후 전송하는 서버 사이드 데이터 처리 파이프라인.
- Kibana : 차트와 그래프를 이용한 데이터 시각화 도구

## Elastic Stack

![elastic-stack](../assets/images/elastic-Stack.png)
Beats를 포함하여 elastic stack이라 한다.

- Beats : 단일 목적의 데이터 수집기 플랫폼. 서버에서 데이터를 수집하여 Logstash나 Elasticsearch에 데이터를 전송.

![elastic](../assets/images/elastic.png)

## 시스템 구성 / 용어

- 도큐먼트(Document)
  - 인덱싱될 수 있는 정보의 기본 단위
  - JSON(JavaScript Object Notation)형식으로 표현
  - RDB의 row와 비슷한 개념으로 고유한 문서 ID를 갖는다
- 인덱스(Index)
  - 비슷한 특성을 갖는 도큐먼트의 집합
  - 이름으로 구분되며 인덱싱, 검색, 업데이트, 삭제를 수행하는 동안 참조하기 위해 사용 됨
  - RDB의 데이터베이스와 비슷한 개념
  - 최초의 데이터를 클러스터에 저장할 때 인덱스 자동 생성
- 타입(Type)
  - 인덱스의 파티션
  - RDB의 테이블과 비슷한 개념
  - 하나의 인덱스에 Single Type 권고(_doc). 7.x버전부터 _doc으로 고정
- 노드(Node)
  - 클러스터를 구성하는 ES 프로세스
  - 데이터를 보관하고 클러스터 인덱싱과 검색 능력에 관여
  - 노드 간 헬스 체크, 문서를 색인하여 저장, 검색 요청에 의해 데이터 리턴
  - 노드별 nam과 uuid 사용
  - 역할에 따라 master, data, ingest, client 노드로 사용
  - master 노드
    - 클러스터 구성의 기준이 되는 노드
    - 노드들의 헬스 체크 담당
    - 모든 업데이트 사항 보고 받음
  - data 노드
    - 문서가 저장되는 노드
    - 문서 요청에 데이터를 리턴
  - all 노드
    - 마스터와 데이터 노드 구분이 필요 없을 때 사용
    - 데이터 노드 확장이 거의 필요 없을 때 비용 절감 위해 사용
  - client 노드
    - 사용자의 쿼리를 받기 위한 노드
    - 받은 쿼리를 data 노드에 전달
    - data 노드가 리턴한 문서를 취합하여 사용자에게 리턴
- 클러스터(Cluster)
  - Elasticsearch는 클러스터로 구성
  - 데이터를 갖고 있는 한개 이상의 노드 집합
  - 사용자는 클러스터 대상으로 데이터 저장, 검색 요청
  - 클러스터별 cluster_name과 cluster_uuid 사용
- 샤드(Shard)
  - 인덱스를 샤드라 불리는 여러 조각으로 나눌 수 있다
  - 데이터를 인덱스에 넣다보면 디스크 볼륨의 유한성으로 인해 더이상 데이터를 저장할 수 없게되고, 단일 노드의 유한한 자원(CPU, Memory)로 인해 검생 성능 저하 발생
  - 명령을 분배하고 병렬적으로 처리될 수 있게 해주어 퍼포먼스를 올려준다
  - 색인할 때 복제 과정이 필요해 성능 저하되고 디스크 볼륨이 두배로 필요
  - 한 번 지정한 샤드 갯수는 불변
- 레플리카(Replicas)
  - 인덱스의 샤드에 대한 한개 이상의 복사본
  - 높은 가용성을 제공한다
- 세그먼트(Segment)
  - 샤드는 세그먼트들로 구성
  - 문서가 저장되는 최소 단위
  - 문서는 System OS Buffer Cache에 저장
  - Refresh과정 통해 문서를 디스크에 저장되어 검색 가능한 상태가 됨
  - 데이터가 잘게 쪼개지면 단일 요청에 많은 segment가 응대해야하는 단점이 있음
  - 백그라운드에서 세그먼트 병합 진행
