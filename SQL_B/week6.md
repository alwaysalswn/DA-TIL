# 📘 SQL_BASIC 6주차 정규 과제

SQL_BASIC 정규 과제는 매주 정해진 분량의 `초보자를 위한 BigQuery(SQL) 입문` 강의를 듣고, 핵심 개념을 정리한 뒤 간단한 SQL 문제를 직접 풀어보는 방식으로 진행합니다.

이번 주는 SQL 입문 과정의 핵심 고비인 `JOIN`을 학습합니다.

JOIN은 여러 테이블을 연결해서 분석 질문에 답하기 위해 반드시 필요한 개념입니다. 이번 주 과제에서는 정답 코드뿐 아니라 **어떤 테이블을 어떤 키로 연결했는지**를 꼭 기록해주세요.

완성된 과제는 Github에 업로드하고, 링크를 스프레드시트 'SQL' 시트에 입력해 제출해주세요.

**👀 수행 인증란은 필수입니다.**

---

## 📚 SQL_BASIC_6th_TIL

### 섹션 6. 다량의 자료를 연결 : JOIN

### 5-2. JOIN 이해하기

### 5-3. 다양한 JOIN 방법

### 5-4. JOIN 쿼리 작성하기

### 5-5. JOIN을 처음 공부할 때 헷갈리는 부분

---

## ✨ 선택 강의

- 5-6. JOIN 연습문제: JOIN 문제를 더 연습하고 싶을 때 선택 수강

---

## 🏁 전체 강의 수강 계획

| 주차 | 필수 강의 범위 | 선택 강의 | 완료 여부 |
| --- | --- | --- | --- |
| 1주차 | 1-1 ~ 2-1 | 1 | ✅ |
| 2주차 | 2-2 ~ 2-3 | 2-4 | ✅ |
| 3주차 | 2-5, 2-7 ~ 2-8 | 2-6 | ✅ |
| 4주차 | 3-2 ~ 4-3 | 3-4 | ✅ |
| 5주차 | 4-4 ~ 4-6 | 4-5, 4-7 | ✅ |
| 6주차 | 5-2 ~ 5-5 | 5-6 | ✅ |
| 7주차 | 필수 강의 없음 | 6-2, 6-3, 6-4, 6-5 | 🍽️ |

---

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념 정리

아래 키워드 중 중요하다고 생각한 개념을 2개 이상 골라 짧게 정리해주세요. 3개보다 더 많이 정리하고 싶다면 자유롭게 항목을 추가해도 좋습니다.

이번 주 키워드:
- JOIN
- INNER JOIN
- LEFT JOIN
- JOIN key
- alias
- NULL
- 다중 JOIN

## 01.

```
개념 이름: INNER JOIN
개념 설명: 두 테이블의 공통 요소, 즉 Key가 양쪽에 모두 있는 행만 연결하는 JOIN. 집합으로 보면 교집합이라 한쪽에만 있는 행은 결과에서 빠짐 (Table A의 1, 2, 3과 Table B의 1, 2, 4를 JOIN하면 1, 2만 남음). ON으로 Key를 꼭 적어줘야 함 (CROSS JOIN만 ON이 필요 없음)
예시 쿼리:
SELECT
  tp.*,
  t.*
FROM basic.trainer_pokemon AS tp
INNER JOIN basic.trainer AS t
ON tp.trainer_id = t.id
# 포획 기록(trainer_pokemon)의 trainer_id와 같은 id를 가진 트레이너가 있는 행만 연결
```

## 02.

```
개념 이름: LEFT JOIN
개념 설명: 왼쪽 테이블을 기준으로 연결하는 JOIN. 기준 테이블의 행은 모두 남기고, 오른쪽 테이블에 짝이 없으면 NULL로 채워짐 (Table A의 3 '다'는 B에 없어서 NULL). 처음에는 LEFT JOIN만 써도 충분하고, 기준이 되는 테이블을 왼쪽에 두고 오른쪽에 데이터를 계속 붙여나가는 방식. JOIN 쿼리는 테이블 확인 → 기준 테이블 정의 → JOIN Key 찾기 → 결과 예상 → 쿼리 작성/검증 순서로 작성
예시 쿼리:
SELECT
  tp.*,
  t.*
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
# AS tp처럼 별칭(alias)을 주면 테이블 이름이 길어도 tp.trainer_id처럼 짧게 쓸 수 있음
```

## (선택) 03.

```
개념 이름: NULL
개념 설명: 값이 없음, 알 수 없음을 뜻함. JOIN에서는 연결할 값이 없는 경우에 나타남 (LEFT JOIN에서 오른쪽 테이블에 짝이 없을 때)
헷갈린 점: 0이나 공백이랑 같은 건 줄 알았는데 값이 아예 없는 것이라 다름. 그래서 NULL인지 확인할 때는 = 대신 IS NULL을 씀 (문제 3에서 사용)
```

---

# 2️⃣ 수행 인증란

![수행 인증](images/스크린샷%202026-11-01%20오후%207.28.01.png)

---

# 3️⃣ 확인 문제

프로그래머스는 로그인이 필요하므로, 로그인 후 문제 풀이를 진행해주세요.

## 🧩 문제 1

문제 링크: [조건에 맞는 도서와 저자 리스트 출력하기](https://school.programmers.co.kr/learn/courses/30/lessons/144854)

풀이 과정:

```
- 사용한 테이블: BOOK(도서), AUTHOR(저자)
- JOIN key: AUTHOR_ID (BOOK의 AUTHOR_ID = AUTHOR의 AUTHOR_ID)
- 날짜를 출력한 형식: DATE_FORMAT(PUBLISHED_DATE, '%Y-%m-%d')로 'YYYY-MM-DD' 형태로 출력. 카테고리가 '경제'인 도서만 거르고 출판일 오름차순으로 정렬
```

```sql
SELECT B.BOOK_ID, A.AUTHOR_NAME,
       DATE_FORMAT(B.PUBLISHED_DATE, '%Y-%m-%d') AS PUBLISHED_DATE
FROM BOOK AS B
INNER JOIN AUTHOR AS A
ON B.AUTHOR_ID = A.AUTHOR_ID
WHERE B.CATEGORY = '경제'
ORDER BY B.PUBLISHED_DATE;
```

![수행 인증](images/스크린샷%202026-11-01%20오후%207.23.11.png)

## 🧩 문제 2

문제 링크: [상품 별 오프라인 매출 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131533)

풀이 과정:

```
- 연결한 테이블: PRODUCT(상품 가격), OFFLINE_SALE(오프라인 판매량)
- JOIN key: PRODUCT_ID
- 집계한 값: 판매량 * 가격을 SUM으로 더해서 SALES로 출력. 상품 코드(PRODUCT_CODE)별로 GROUP BY하고 매출 내림차순, 같으면 상품 코드 오름차순으로 정렬
```

```sql
SELECT P.PRODUCT_CODE, SUM(P.PRICE * O.SALES_AMOUNT) AS SALES
FROM PRODUCT AS P
INNER JOIN OFFLINE_SALE AS O
ON P.PRODUCT_ID = O.PRODUCT_ID
GROUP BY P.PRODUCT_CODE
ORDER BY SALES DESC, P.PRODUCT_CODE;
```

![수행 인증](images/스크린샷%202026-11-01%20오후%207.23.31.png)

## 🧩 문제 3

문제 링크: [없어진 기록 찾기](https://school.programmers.co.kr/learn/courses/30/lessons/59042)

풀이 과정:

```
- 기준이 되는 왼쪽 테이블: ANIMAL_OUTS (입양 기록은 있으니 이걸 기준으로 보호소 입소 기록인 ANIMAL_INS를 붙임)
- LEFT JOIN이 필요한 이유: INNER JOIN을 쓰면 입소 기록이 없는 행이 결과에서 사라져서 찾을 수가 없음. LEFT JOIN으로 ANIMAL_OUTS 행을 전부 남겨야 짝이 없는 행을 찾을 수 있음
- NULL이 발생하는 경우: ANIMAL_INS에 같은 ANIMAL_ID가 없으면 ANIMAL_INS 쪽 컬럼이 NULL이 되고, WHERE I.ANIMAL_ID IS NULL로 걸러냄
```

```sql
SELECT O.ANIMAL_ID, O.NAME
FROM ANIMAL_OUTS AS O
LEFT JOIN ANIMAL_INS AS I
ON O.ANIMAL_ID = I.ANIMAL_ID
WHERE I.ANIMAL_ID IS NULL
ORDER BY O.ANIMAL_ID;
```

![수행 인증](images/스크린샷%202026-11-01%20오후%207.23.48.png)

## 🧩 문제 4

문제 링크: [있었는데요 없었습니다](https://school.programmers.co.kr/learn/courses/30/lessons/59043)

풀이 과정:

```
- 사용한 테이블: ANIMAL_INS(보호 시작), ANIMAL_OUTS(입양)
- JOIN key: ANIMAL_ID
- 비교한 날짜 컬럼: DATETIME. 입양일(ANIMAL_OUTS)이 보호 시작일(ANIMAL_INS)보다 빠른 경우를 찾으려고 I.DATETIME > O.DATETIME 조건 사용
- 정렬 기준: 보호 시작일(I.DATETIME)이 빠른 순
```

```sql
SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_INS AS I
INNER JOIN ANIMAL_OUTS AS O
ON I.ANIMAL_ID = O.ANIMAL_ID
WHERE I.DATETIME > O.DATETIME
ORDER BY I.DATETIME;
```

![수행 인증](images/스크린샷%202026-11-01%20오후%207.24.05.png)

---

# 4️⃣ 참고 자료

JOIN 개념이 헷갈린다면 아래 자료를 함께 참고해도 좋습니다.

1. https://data-marketing-bk.tistory.com/entry/SQL-JOIN-%ED%95%9C-%EB%B0%A9%EC%97%90-%EC%A0%95%EB%A6%AC-%EA%B0%9C%EB%85%90%EB%B6%80%ED%84%B0-%EC%BD%94%EB%93%9C%EA%B9%8C%EC%A7%80-%EC%9D%B4%EA%B2%83%EB%A7%8C-%EB%B3%B4%EC%9E%90
2. https://velog.io/@wijoonwu/JOIN

---

# 5️⃣ 이번 주 회고

```
1. INNER JOIN과 LEFT JOIN의 차이를 어떻게 이해했는지: INNER JOIN은 양쪽 테이블에 Key가 모두 있는 행만 남는 교집합이고, LEFT JOIN은 왼쪽 기준 테이블은 전부 남기고 오른쪽에 짝이 없으면 NULL로 채워짐. 그래서 없는 데이터를 찾을 때는 LEFT JOIN 후 IS NULL로 거르면 됨
2. JOIN 문제를 풀 때 가장 먼저 확인해야 할 것: 테이블에 어떤 컬럼이 있는지 보고 기준 테이블과 JOIN Key를 정하는 것. 강의에서 말한 것처럼 결과를 먼저 예상해보고 쿼리를 쓰면 덜 헷갈림
3. JOIN 결과가 예상과 달랐던 경험이 있다면: 문제 3처럼 INNER JOIN을 쓰면 짝이 없는 행이 통째로 사라져서 row 수가 줄어듦. JOIN 전후 row 수를 비교해보는 습관이 필요하다고 느낌
```

수고하셨습니다!



