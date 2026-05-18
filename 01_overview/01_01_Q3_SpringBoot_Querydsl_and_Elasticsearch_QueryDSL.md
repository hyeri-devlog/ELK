# SpringBoot에서 사용하는 QueryDSL과 Elasticsearch에서 사용하는 Query DSL의 차이
둘은 이름이 비슷해서 헷갈리기 쉽다.  
하지만 완전 다른 것이다.

---

## 1️⃣ SpringBoot에서 말하는 QueryDSL
SpringBoot에서 흔히 말하는 `QueryDSL`은 보통 Java 라이브러리인 Querydsl을 말한다.  
주로 JPA와 함께 사용한다.  

**SQL 또는 JPQL을 Java 코드로 타입 안전하게 작성하기 위한 라이브러리**이다.

```java
QProduct product = QProduct.product;

List<Product> result = queryFactory
        .selectFrom(product)
        .where(product.name.eq("노트북"))
        .fetch();
```

즉, **Java 코드로 SQL/JPQL을 안전하게 작성하는 도구**이다.

--

## 2️⃣ Elasticsearch의 Query DSL
Elasticsearch의 `Query DSL`은 JSON 기반 검색 문법이다.  
**Elasticsearch에 검색 조건을 전달하기 위한 JSON 질의 언어**이다.

예를 들어 상품명에서 `노트북`을 검색하려면 이렇게 작성한다.

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

가격이 100만 원 이하인 상품을 검색하려면 이렇게 작성할 수 있다.

```json
GET /products/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 1000000
      }
    }
  }
}
```

여러 조건을 조합할 수도 있다.

```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "노트북"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "price": {
              "lte": 1000000
            }
          }
        }
      ]
    }
  }
}
```

즉, Elasticsearch Query DSL의 핵심은 **Elasticsearch에 검색 요청을 보내기 위한 JSON 문법**이라는 것이다.

---

## 📌 둘의 차이

|구분|SpringBoot Querydsl|Elasticsearch Query DSL|
|:--:|:--|:--|
|정체|Java 라이브러리|Elasticsearch 검색 문법|
|주 사용처|JPA, SQL, JPQL|Elasticsearch|
|작성 방식|Java 코드|JSON|
|목적|DB 쿼리를 타입 안전하게 작성|검색 조건을 Elasticsearch에 전달|
|대상|RDB|Elasticsearch|
|예시|`product.name.eq("노트북")`|`{ "match": { "name": "노트북" } }`|

한 문장으로 정리하면 이렇다.

> [!IMPORTANT]
> - **SpringBoot Querydsl**은 RDB 조회 쿼리를 Java 코드로 작성하는 도구
> - **Elasticsearch Query DSL**은 Elasticsearch 검색 요청을 JSON으로 작성하는 문법
