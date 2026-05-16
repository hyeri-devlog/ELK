# Elasticsearch 개요
## 1. 학습 목표 📌
이번 문서에서는 `Elasticsearch`를 처음 배우기 전에 반드시 알아야 하는 기본 개념을 정리한다.

> [!IMPORTANT]
> - Elasticsearch가 무엇인지 이해한다
> - Elasticsearch를 왜 배우는지 이해한다
> - Elasticsearch와 RDB의 차이를 감각적으로 이해한다
> - Elasticsearch가 Apache Lucene 위에서 동작한다는 점을 이해한다
> - Apache Lucene의 주요 특징을 이해한다

---

## 2. Elasticsearch란? 
**`Elasticsearch`는 대량의 데이터를 빠르게 저장, 검색, 분석하기 위한 분산 검색 엔진**이다.

Elasticsearch 공식 문서에서는 Elasticsearch를 검색과 분석을 위한 분산 엔진으로 설명하며,  
구조화 데이터, 비구조화 데이터, 벡터 데이터 등을 저장하고 빠르게 검색할 수 있다고 설명한다.  

=> 쉽게 말하자면, Elasticsearch은 '검색을 잘하기 위해 만들어진 데이터 저장소'이다.  

예를 들어 쇼핑몰에서 사용자가 `검정색 반팔 티셔츠`를 입력했다고 해보자.  
일반적인 DB에서도 LIKE 등을 활용하여 검색은 할 수 있다.  

하지만 데이터가 많아지고, 검색 조건이 복잡해지면 문제가 생긴다.  
예를 들면 사용자는 아래와 같이 검색할 수 있다.

```
검정 반팔
검은색 티
black t-shirt
반팔티
여름 티셔츠
```

이런 검색어들을 단순한 `LIKE` 검색만으로 자연스럽게 처리하기는 어렵다.
Elasticsearch는 이런 문제를 해결하기 위해 사용한다.

---

## 3. Elasticsearch를 왜 배워야 할까?
SpringBoot, RDB만 사용해도 웹 서비스는 만들 수 있다.
회원가입, 로그인, 게시글, 주문, 결제, 배송 관리 같은 기능은 대부분 RDB로 충분히 구현할 수 있다.
하지만 다음과 같은 요구사항이 생기면 RDB만으로는 한계가 빨리 온다.

|**요구사항**|**RDB만 사용할 때의 어려움**|
|:---|:---|
|상품명 검색|`LIKE '%검색어%'`는 데이터가 많아질수록 느려질 수 있음|
|게시글 본문 검색|긴 텍스트 안에서 단어를 찾기 어려움|
|오타 허용 검색|`스프링부트`와 `스프링 부트`를 비슷하게 처리하기 어려움|
|자동완성|입력 중인 단어를 빠르게 추천하기 어려움|
|연관도 높은 결과 정렬|단순 최신순, 가격순이 아니라 '검색어와 얼마나 관련 있는지' 계산이 필요함|
|로그 분석|수많은 서버 로그에서 에러, 사용자 행동, 장애 원인을 빠르게 찾아야 함|
|AI 검색|키워드 검색뿐 아니라 의미 기반 검색, 벡터 검색이 필요함|

즉, Elasticsearch는 데이터를 저장하는 것보다 **데이터를 빠르고 똑똑하게 찾는 것**이 중요해질 때 사용합니다.

---

## 4. Elasticsearch를 RDB와 비교해서 이해하기
Elasticsearch도 데이터를 저장하지만, 전통적인 RDB와 목적이 다르다.

|**구분**|**RDB**|**Elasticsearch**|
|:---:|:---|:---|
|대표 예시|MySQL, MariaDB, PostgreSQL|Elasticsearch|
|주 목적|정확한 데이터 저장, 관계 관리, 트랜잭션 처리|빠른 검색, 분석, 로그 탐색|
|데이터 구조|테이블, 행, 컬럼|인덱스, 도큐먼트, 필드|
|질의 방식|SQL|Query DSL|
|강점|정합성, 트랜잭션, JOIN, 제약조건|전문 검색, 빠른 검색, 분산 처리, 집계, 로그 분석|
|예시|주문 생성, 결제 저장, 회원 정보 관리|상품 검색, 게시글 검색, 로그 검색, AI 검색|

Elasticsearch의 검색 문법은 SQL이 아니라 JSON 기반의 Query DSL을 사용한다.  
Elasticsearch 공식 문서에서도 Query DSL을 Elasticsearch에서 복잡한 검색, 필터링, 집계를 수행할 수 있는 JSON 스타일의 질의 언어라고 설명한다.  

예를 들어 RDB에서는 이렇게 검색한다.

```sql
SELECT *
FROM product
WHERE name LIKE '%노트북%';
```

Elasticsearch에서는 보통 아래와 같이 검색한다.

```json
GET /products/_search
{
  "query": {
    "match": {
      "name": "노트북"
    }
  }
}
```

처음 보면 SQL에 비해 낯설지만, Elasticsearch는 검색 조건이 복잡해질수록 Query DSL의 장점이 커진다.

---

## 5. Elasticsearch는 언제 쓰는가?
|**사용 사례**|**설명**|
|:---:|:---|
|쇼핑몰 상품 검색|상품명, 브랜드명, 카테고리, 설명 검색|
|게시판 검색|제목, 내용, 작성자 검색|
|자동완성|검색창에 일부 단어를 입력하면 추천어 표시|
|로그 분석|서버 로그, 에러 로그, 접속 로그 검색|
|모니터링|서버 상태, API 응답 시간, 장애 원인 분석|
|보안 분석|비정상 로그인, 공격 패턴 감지|
|AI 검색|벡터 검색, 의미 기반 검색, RAG 검색|

예를 들어 쇼핑몰에서는 상품 데이터의 원본은 PostgreSQL에 저장하고, 검색용 데이터는 Elasticsearch에 따로 넣는 구조를 많이 사용한다.

```
PostgreSQL
-> 주문, 결제, 상품 원본 데이터 저장

Elasticsearch
-> 상품 검색용 데이터 저장
```

즉, Elasticsearch가 RDB를 완전히 대체한다기보다는 보통 다음처럼 같이 사용한다.

```
RDB = 원본 저장소
Elasticsearch = 검색 전용 저장소
```

---

## 6. Elasticsearch의 핵심 개념

<img width="560" height="456" alt="image" src="https://github.com/user-attachments/assets/d1182582-ed33-4cf6-969b-89f8027612c3" />

|**용어**|**RDB와 매칭**|
|:---:|:---|
|Index|Table|
|Document|Row|
|Field|Column|
|Mapping|Table schema|
|Query DSL|SQL|
|Cluster|여러 Elasticsearch 서버 묶음|
|Node|Elasticsearch 서버 한 대|
|Shard|인덱스를 나눈 조각|
|Replica|Shard의 복제본|

<img width="512" height="417" alt="image" src="https://github.com/user-attachments/assets/2d42991f-68df-4132-980c-778c8a3cdedf" />

예를 들어 `products`라는 상품 인덱스가 있다고 하면 다음과 같이 볼 수 있다.

```
products 인덱스
 ├── document 1: { "name": "검정 반팔 티셔츠", "price": 19000 }
 ├── document 2: { "name": "흰색 셔츠", "price": 29000 }
 └── document 3: { "name": "여름 반팔 니트", "price": 39000 }
```

RDB 관점으로 보면 대략 이런 느낌이다.

```
product 테이블
 ├── row 1
 ├── row 2
 └── row 3
```

다만 완전히 같은 것은 아니다.
Elasticsearch의 Document는 JSON 형태이고, 검색을 위해 내부적으로 분석 과정을 거쳐 저장된다.

---

## 7. Elasticsearch가 검색이 빠른 이유
Elasticsearch가 검색을 빠르게 할 수 있는 핵심 이유는 **역색인** 때문이다.  
Elastic 공식 문서에서는 역색인을 **`각 토큰이 어떤 문서에 포함되어 있는지 매핑하는 데이터 구조`**라고 설명한다.  

일반적인 RDB 검색을 단순화하면 이런 느낌이다.

```
문서 1: 검정 반팔 티셔츠
문서 2: 흰색 셔츠
문서 3: 여름 반팔 니트
```

사용자가 `반팔`을 검색하면 각 문서를 하나씩 확인해야 한다.

```
문서 1에 반팔이 있나? 있음
문서 2에 반팔이 있나? 없음
문서 3에 반팔이 있나? 있음
```

반면 Elasticsearch는 데이터를 저장할 때 미리 이런 구조를 만들어 둔다.

```
검정 → 문서 1
반팔 → 문서 1, 문서 3
티셔츠 → 문서 1
흰색 → 문서 2
셔츠 → 문서 2
여름 → 문서 3
니트 → 문서 3
```

그래서 `반팔`을 검색하면 바로 알 수 있다.

```
반팔 -> 문서 1, 문서 3
```

이 구조가 바로 **역색인, Inverted Index**이다.  
책 뒤에 있는 '찾아보기'와 비슷하다.  
책에서 `Docker`라는 단어가 어디 나오는지 찾고 싶을 때 책 전체를 처음부터 끝까지 읽는다.  
책 뒤의 찾아보기에서 `Docker`를 찾으면 된다.

```
Docker -> 12쪽, 45쪽, 103쪽
```

Elasticsearch도 비슷하다.

```
검색어 -> 해당 검색어가 들어 있는 문서 목록
```

---

## 8. Apache Lucene이란?
Elasticsearch를 이해하려면 Apache Lucene도 알아야 한다.  
**`Apache Lucene`는 Java로 작성된 고성능 검색 라이브러리**이다.  

Apache Lucene 공식 문서에서는 Lucene Core를 강력한 색인과 검색 기능, spellchecking,
hit highlighting, 분석과 토큰화 기능을 제공하는 Java 라이브러리라고 설명한다.  

더 쉽게 말하자면,  
Lucene은 검색 엔진을 만들기 위한 핵심 부품(예 - 자동차 엔진)이고,  
Elasticsearch는 그 Lucene을 사용해서 분산 검색 엔진으로 만든 제품(예 - 자동차)이다.  

Lucene 자체는 매우 강력하지만, 라이브러리이다.  
즉, 개발자가 직접 Java 코드로 Lucene을 사용해서 검색 시스템을 만들어야 한다.  
반면, Elasticsearch는 Lucene을 내부에 사용하면서도 다음 기능을 제공한다.

- HTTP API
- JSON 기반 요청/응답
- 클러스터 구성
- 분산 저장
- 샤딩
- 복제
- 장애 복구
- Kibana 연동
- 보안 기능
- 운영 관리 기능

그래서 실무에서는 Lucene을 직접 쓰기보다는 Elasticsearch를 사용하는 경우가 많다.

---

## 9. Elasticsearch와 Apache Lucene의 관계


---

## 10. Apache Lucene의 주요 특징

### 1) 고성능 전문 검색


### 2) 색인 기능


### 3) 분석과 토큰화


### 4) 역색인 구조


### 5) 관련도 점수 계산


### 6) 하이라이팅


### 7) 맞춤법 교정과 추천어


### 8) 벡터 검색 지원


---

## 11. Elasticsearch 이해하기


---

## 12. 정리

---

## 출처
[Elastic 공식 문서] Elasticsearch 소개
https://www.elastic.co/elasticsearch

[Elastic 공식 문서] Query DSL
https://www.elastic.co/docs/explore-analyze/query-filter/languages/querydsl

[Elastic 공식 문서] Full-text search와 Inverted Index 설명
https://www.elastic.co/docs/solutions/search/full-text/how-full-text-works

[Apache Lucene 공식 사이트] Apache Lucene
https://lucene.apache.org/

[Apache Lucene 공식 문서] Lucene Core
https://lucene.apache.org/core/
