# 📘 SQL_BASIC 3주차 정규 과제

SQL_BASIC 정규 과제는 매주 정해진 분량의 `초보자를 위한 BigQuery(SQL) 입문` 강의를 듣고, 핵심 개념을 정리한 뒤 간단한 SQL 문제를 직접 풀어보는 방식으로 진행합니다.

이번 주는 집계 함수와 `GROUP BY`, `HAVING`을 학습합니다.

완성된 과제는 Github에 업로드하고, 링크를 스프레드시트 'SQL' 시트에 입력해 제출해주세요.

**👀 수행 인증란은 필수입니다.**

---

## 📚 SQL_BASIC_3rd_TIL

### 섹션 3. 데이터 탐색 - 조건, 추출, 요약

### 2-5. 집계(GROUP BY + HAVING + SUM/COUNT)

### 2-7. 정리

### 2-8. 새로운 집계 함수 소개(GROUP BY ALL, 2024-02-26에 나온 함수)

---

## ✨ 선택 강의

- 2-6. 연습 문제: 집계와 조건 조회를 더 연습하고 싶을 때 선택 수강

---

## 🏁 전체 강의 수강 계획

| 주차 | 필수 강의 범위 | 선택 강의 | 완료 여부 |
| --- | --- | --- | --- |
| 1주차 | 1-1 ~ 2-1 | 1 | ✅ |
| 2주차 | 2-2 ~ 2-3 | 2-4 | ✅ |
| 3주차 | 2-5, 2-7 ~ 2-8 | 2-6 | ✅ |
| 4주차 | 3-2 ~ 4-3 | 3-4 | 🍽️ |
| 5주차 | 4-4 ~ 4-6 | 4-5, 4-7 | 🍽️ |
| 6주차 | 5-2 ~ 5-5 | 5-6 | 🍽️ |
| 7주차 | 필수 강의 없음 | 6-2, 6-3, 6-4, 6-5 | 🍽️ |

---

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념 정리

아래 키워드 중 중요하다고 생각한 개념을 2개 이상 골라 짧게 정리해주세요. 3개보다 더 많이 정리하고 싶다면 자유롭게 항목을 추가해도 좋습니다.

이번 주 키워드:
- COUNT
- SUM
- AVG
- MAX
- MIN
- GROUP BY
- HAVING
- 집계 기준

## 01.

```
개념 이름: GROUP BY
개념 설명: 같은 값을 가진 행끼리 묶어서 묶음마다 집계 결과를 한 줄씩 만든다.
예시 쿼리:
SELECT generation, COUNT(id) AS cnt
FROM basic.pokemon
GROUP BY generation
```

## 02.

```
개념 이름: HAVING
개념 설명: 집계가 끝난 결과에 거는 조건으로, 집계값은 WHERE에서 못 쓰기 때문에 HAVING을 쓴다.
예시 쿼리:
SELECT type1, COUNT(id) AS cnt
FROM basic.pokemon
GROUP BY type1
HAVING cnt >= 10
```

## (선택) 03.

```
개념 이름: GROUP BY ALL
개념 설명: SELECT에서 집계 함수가 아닌 컬럼을 자동으로 전부 그룹 기준으로 잡아준다.
헷갈린 점: SELECT에 컬럼을 추가하면 그룹 기준도 같이 바뀐다는 점, 그리고 GROUP BY 뒤에 컬럼을 직접 적는 것과 결과가 같은지 헷갈렸다.
```

---

# 2️⃣ 수행 인증란

![수행 인증](images/스크린샷%202026-09-20%20오후%209.44.53.png)

---

# 3️⃣ 확인 문제

프로그래머스는 로그인이 필요하므로, 로그인 후 문제 풀이를 진행해주세요.

## 🧩 문제 1

문제 링크: [최댓값 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/59415)

풀이 과정:

```
- 문제 요구사항: ANIMAL_INS 테이블에서 가장 최근에 들어온 동물의 입소 시각(DATETIME)을 조회
- 사용한 SQL 절: SELECT, FROM
  SELECT MAX(DATETIME) AS 시간
  FROM ANIMAL_INS;
- 새로 배운 점: 가장 큰 값 하나만 필요하면 ORDER BY ... DESC LIMIT 1 대신 MAX()를 쓰는 게 의도가 더 분명하다는 것을 알게 됐다.
```

![수행 인증](images/스크린샷%202026-09-20%20오후%209.45.21.png)

## 🧩 문제 2

문제 링크: [가장 비싼 상품 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131697)

풀이 과정:

```
- 사용한 집계 함수: MAX
- 집계 대상 컬럼: PRODUCT 테이블의 PRICE

  SELECT MAX(PRICE) AS MAX_PRICE
  FROM PRODUCT;

- 결과를 검증한 방법: ORDER BY PRICE DESC LIMIT 1로도 조회해서 같은 값이 나오는지 비교했다.
```

![수행 인증](images/스크린샷%202026-09-20%20오후%209.45.56.png)

## 🧩 문제 3

문제 링크: [고양이와 개는 몇 마리 있을까](https://school.programmers.co.kr/learn/courses/30/lessons/59040)

풀이 과정:

```
- 그룹화 기준: ANIMAL_TYPE
- WHERE와 HAVING 중 사용한 절: 둘 다 쓰지 않았다. 고양이와 개를 종류별로 세기만 하면 되고, 집계 결과를 다시 거를 조건도 없었기 때문이다.

  SELECT ANIMAL_TYPE, COUNT(*) AS count
  FROM ANIMAL_INS
  GROUP BY ANIMAL_TYPE
  ORDER BY ANIMAL_TYPE;

- 처음 틀렸다면 틀린 이유: 정렬을 빼먹어서 고양이, 개 순서가 보장되지 않았다. ORDER BY ANIMAL_TYPE을 추가해야 했다.
- 새로 배운 SQL 패턴: 종류별로 세는 문제는 GROUP BY 기준 컬럼 + COUNT + 같은 컬럼으로 ORDER BY 패턴으로 푼다.
```

![수행 인증](images/스크린샷%202026-09-20%20오후%209.46.15.png)

---

# 4️⃣ 이번 주 회고

```
1. 문제를 SQL로 옮길 때 가장 어려웠던 부분: 문제에서 "몇 마리"처럼 무엇을 세는지와 "종류별"처럼 무엇으로 묶는지를 구분해서 COUNT 대상과 GROUP BY 기준을 정하는 부분이 어려웠다.
2. WHERE와 HAVING의 차이를 어떻게 이해했는지: WHERE는 그룹으로 묶기 전에 원본 행을 거르고, HAVING은 묶어서 계산한 뒤의 결과를 거른다. "Water 타입 포켓몬"은 WHERE, "포켓몬이 10마리 이상인 타입"은 HAVING이다.
3. 다음 주에 더 연습하고 싶은 문제 유형: 조건과 집계가 함께 나오는 문제, 그리고 여러 컬럼으로 그룹화하는 문제를 더 연습하고 싶다.
```

수고하셨습니다!



