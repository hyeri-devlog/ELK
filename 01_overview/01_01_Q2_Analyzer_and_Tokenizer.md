# Analyzer와 Tokenizer의 차이점

## 1️⃣ Tokenizer란?
**Tokenizer는 문장을 토큰으로 쪼개는 역할**을 한다.  

예를 들어, `검정 반팔 티셔츠`라는 검색어가 있다면,  
Tokenizer는 이 문장을 `검정`, `반팔`, `티셔츠`로 나눈다.  

영어라면 보통 공백 기준으로 나누기 쉽다.  
(예 - `black short sleeve t-shirt` 검색 시 `black`, `short`, `sleeve`, `t`, `shirt`)  

즉, Tokenizer의 핵심 역할은 이것이다.

```
문장 -> 단어 조각들
```

---

## 2️⃣ Analyzer란?
**Analyzer는 텍스트를 검색 가능한 토큰으로 만드는 전체 처리 과정**이다.  
Analyzer 안에는 Tokenzier만 있는 것이 아니다.  
보통 다음 요소들이 함께 들어간다.

```
Analyzer
 ├── Character Filter
 ├── Tokenizer
 └── Token Filter
```

각 역할은 아래와 같다.

- **Character Filter**: 토큰화 전에 문자 일부를 정리
- **Tokenizer**: 문장을 토큰으로 쪼갬
- **Token Filter**: 쪼개진 토큰을 후처리

예를 들어 다음 문장이 있다고 하자.

```
I LOVE SpringBoot!!!
```

Analyzer는 대략 이런 처리를 할 수 있다.

```
**원문**
I LOVE SpringBoot!!!

**1단계 Character Filter**
I LOVE SpringBoot

**2단계 Tokenizer**
I
LOVE
SpringBoot

**3단계 Token Filter**
i
love
springboot
```

최종적으로 검색 엔진에는 이런 토큰이 저장된다.

```
i
love
springboot
```

---

## 📌Analyzer와 Tokenizer 차이
|구분|Analyzer|Tokenizer|
|:--:|:--|:--|
|역할|텍스트 분석 전체 과정|텍스트를 토큰으로 쪼개는 역할|
|범위|넓음|좁음|
|포함 관계|Tokenizer를 포함함|Analyzer의 일부|
|예시|소문자 변환, 특수문자 제거, 토큰화, 불용어 제거|공백 기준으로 단어 나누기|
|비유|세탁 공정 전체|세탁물 분류|

핵심은 `Analyzer 안에 Tokenizer가 들어있다`는 것이다.  

도식으로 보면 이렇다.

```
Analyzer
 ├── Character Filter
 │     예: HTML 태그 제거, 특수문자 정리
 │
 ├── Tokenizer
 │     예: 문장을 단어로 쪼개기
 │
 └── Token Filter
       예: 소문자 변환, 불용어 제거, 동의어 처리
```

---

## 한국어 검색에서 왜 중요할까❓
한국어는 영어보다 검색 처리가 어렵다.  
영어는 공백 기준으로 단어를 나누기 쉬운 반면, 한국어는 조사, 어미, 합성어가 있다.

```
검정운동화를 샀어요
```

위 문장은 아래처럼 나눌 수 있다.

|1|2|3|
|:--|:--|:--|
|검정/운동화/샀어요|검정운동화/샀어요|검정/운동화/사다|

검색 품질은 이 분석 방식에 따라 크게 달라진다.  
그래서 Elasticsearch에서 한국어 검색을 구현할 때는 Analyzer 설정이 매우 중요하다.
