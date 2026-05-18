# Elasticsearch의 주요 기능

## 전문 검색

## 역색인 기반 빠른 검색

## 분석기와 토큰화

## 관련도 점수 계산

## Query DSL

---

## 집계 Aggregation
Elasticsearch는 검색 결과를 그룹화하고 통계를 낼 수 있다.  

예를 들어 상품 데이터가 있다고 해보자.

```json
{
  "category": "의류",
  "price": 19000
}
```

다음과 같은 분석이 가능하다.

```
카테고리별 상품 개수
평균 가격
최고 가격
최저 가격
날짜별 주문 수
```

예를 들어 카테고리별 상품 수를 구하면 이런 결과를 만들 수 있다.

```
의류: 120개
전자기기: 80개
식품: 50개
```

Kibana 대시보드에서 그래프로 보여주는 데이터도 이런 집계를 기반으로 만들어지는 경우가 많다.

---

## 분산 처리
Elasticsearch는 데이터를 여러 Node에 나누어 저장할 수 있다.

```
Cluster
├── Node 1
├── Node 2
└── Node 3
```

하나의 Index는 여러 Shard로 나뉜다.

```
products Index
├── Shard 0
├── Shard 1
└── Shard 2
```

그리고 Shard는 여러 Node에 분산 배치된다.

```
Node 1 → Shard 0
Node 2 → Shard 1
Node 3 → Shard 2
```

이 구조 덕분에 Elasticsearch는 대량 데이터를 여러 서버에 나누어 저장하고 검색할 수 있다.

---

## 복제와 장애 대응
Elasticsearch는 Shard의 복제본을 만들 수 있다.

```
Primary Shard = 원본
Replica Shard = 복제본
```

예를 들어 다음과 같이 배치할 수 있다.

```
Node 1 → Primary Shard 0
Node 2 → Replica Shard 0
```

Node 1에 장애가 발생해도  Node 2에는 복제본이 남아 있다.

```
Node 1 장애
↓
Replica Shard 0이 Primary 역할을 대신 수행
```

그래서 Elasticsearch는 단일 서버보다 장애 대응에 유리하다.

---

## 거의 실시간 검색
Elasticsearch는 데이터를 넣은 뒤 비교적 짧은 시간 안에 검색 가능하게 만든다.  
이를 보통 Near Real-Time Search, 즉 거의 실시간 검색이라고 한다.  
예를 들어 상품을 등록하면 바로 다음 순간에 100% 즉시 검색되는 구조라기보다는, 아주 짧은 지연 후 검색 가능해지는 구조이다.  

```
DB에 저장 완료
↓
Elasticsearch에 색인 요청
↓
아주 짧은 지연 후 검색 가능
```

그래서 테스트 코드에서 데이터를 넣자마자 바로 검색하면 결과가 안 나오는 경우가 있을 수 있다.

---

## Ingest Pipeline

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/7dd55be0-837d-4a55-91b7-f0440dbda795" />

Elasticsearch는 데이터를 저장하기 전에 간단히 가공할 수 있다.  
이 기능을 `Ingest Pipeline`이라고 한다.  
Elastic 공식 문서에서는 Ingest Pipeline을 **색인 전에 필드 제거, 텍스트에서 값 추출, 데이터 보강** 같은 변환을 수행하는 기능이라고 설명한다.  

예를 들어 로그가 다음처럼 들어온다고 하자.

```
2026-05-18 ERROR Payment failed userId=kim
```

Ingest Pipeline에서 다음처럼 가공할 수 있다.

```json
{
  "date": "2026-05-18",
  "level": "ERROR",
  "message": "Payment failed",
  "userId": "kim"
}
```

즉, 원본 데이터를 검색하기 좋은 형태로 정리한 뒤 저장할 수 있다.

---

## Kibana 연동
Elasticsearch는 Kibana와 함께 자주 사용된다.

```
Elasticsearch = 데이터 저장, 검색, 집계
Kibana = 화면에서 조회, 분석, 시각화
```

Kibana에서는 다음을 할 수 있다.

```
인덱스 데이터 조회
검색 쿼리 테스트
로그 검색
대시보드 생성
차트 생성
클러스터 상태 확인
```

개발자 입장에서는 Elasticsearch만 쓰는 것보다 Kibana를 같이 띄워두면 훨씬 이해하기 쉽다.

---

## 벡터 검색과 의미 기반 검색

## 정리
1. 전문 검색
2. 역색인 기반 빠른 검색
3. Analyzer / Tokenizer 기반 텍스트 분석
4. 관련도 점수 계산
5. JSON 기반 Query DSL
6. Aggregation 집계
7. 분산 처리
8. Shard 복제와 장애 대응
9. 거의 실시간 검색
10. Ingest Pipeline
11. Kibana 연동
12. 벡터 검색 / 의미 기반 검색

## 출처
[Elastic 공식 문서] Elasticsearch Features
https://www.elastic.co/elasticsearch/features

[Elastic 공식 문서] Full-text search
https://www.elastic.co/docs/solutions/search/full-text

[Elastic 공식 문서] Vector search
https://www.elastic.co/docs/solutions/search/vector

[Elastic 공식 문서] Semantic search
https://www.elastic.co/docs/solutions/search/semantic-search

[Elastic 공식 문서] Ingest pipelines
https://www.elastic.co/docs/manage-data/ingest/transform-enrich/ingest-pipelines
