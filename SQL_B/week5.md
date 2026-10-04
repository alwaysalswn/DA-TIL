# 📘 SQL_BASIC 5주차 정규 과제

SQL_BASIC 정규 과제는 매주 정해진 분량의 `초보자를 위한 BigQuery(SQL) 입문` 강의를 듣고, 핵심 개념을 정리한 뒤 간단한 SQL 문제를 직접 풀어보는 방식으로 진행합니다.

이번 주는 날짜/시간 데이터와 조건문을 학습합니다. 특히 `CASE WHEN`은 SQL 문제 풀이와 데이터 분석에서 자주 사용되므로, 직접 분류 기준을 만들고 결과를 확인하는 연습을 해주세요.

완성된 과제는 Github에 업로드하고, 링크를 스프레드시트 'SQL' 시트에 입력해 제출해주세요.

**👀 수행 인증란은 필수입니다.**

---

## 📚 SQL_BASIC_5th_TIL

### 섹션 5. 데이터 탐색 - 변환

### 4-4. 날짜 및 시간 데이터 이해하기

### 4-6. 조건문(CASE WHEN, IF)

---

## ✨ 선택 강의

- 4-5. 시간 데이터 연습문제: 날짜/시간 함수를 더 연습하고 싶을 때 선택 수강
- 4-7. 조건문 연습문제: CASE WHEN과 IF를 더 연습하고 싶을 때 선택 수강

---

## 🏁 전체 강의 수강 계획

| 주차 | 필수 강의 범위 | 선택 강의 | 완료 여부 |
| --- | --- | --- | --- |
| 1주차 | 1-1 ~ 2-1 | 1 | ✅ |
| 2주차 | 2-2 ~ 2-3 | 2-4 | ✅ |
| 3주차 | 2-5, 2-7 ~ 2-8 | 2-6 | ✅ |
| 4주차 | 3-2 ~ 4-3 | 3-4 | ✅ |
| 5주차 | 4-4 ~ 4-6 | 4-5, 4-7 | ✅ |
| 6주차 | 5-2 ~ 5-5 | 5-6 | 🍽️ |
| 7주차 | 필수 강의 없음 | 6-2, 6-3, 6-4, 6-5 | 🍽️ |

---

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념 정리

아래 키워드 중 중요하다고 생각한 개념을 2개 이상 골라 짧게 정리해주세요. 3개보다 더 많이 정리하고 싶다면 자유롭게 항목을 추가해도 좋습니다.

이번 주 키워드:
- DATE
- DATETIME
- TIMESTAMP
- EXTRACT
- DATETIME_TRUNC
- FORMAT_DATETIME
- CASE WHEN
- IF

## 01.

```
개념 이름: CASE WHEN
개념 설명: 조건에 따라 다른 값을 표시하고 싶을 때 쓰는 조건문. 여러 조건이 있을 때 유용하고, WHEN을 위에서부터 차례로 검사해서 둘 다 해당하면 앞선 조건을 따르기 때문에 조건 순서에 주의해야 함 (attack >= 50을 100보다 먼저 쓰면 Very Strong이 안 나옴). 어디에도 안 맞는 경우는 ELSE로 처리
예시 쿼리:
SELECT
  eng_name,
  attack,
  CASE
    WHEN attack >= 100 THEN 'Very Strong'
    WHEN attack >= 50 THEN 'Strong'
    ELSE 'Weak'
  END AS attack_level
FROM basic.pokemon
```

## 02.

```
개념 이름: EXTRACT
개념 설명: DATETIME에서 특정 부분(YEAR, MONTH, DAY, HOUR, MINUTE 등)만 뽑아내는 함수. EXTRACT(part FROM datetime) 형태로 쓰고, 요일은 DAYOFWEEK로 뽑으면 일요일이 1, 토요일이 7인 값이 나옴
예시 쿼리:
SELECT
  EXTRACT(YEAR FROM DATETIME "2024-01-02 14:00:00") AS year,
  EXTRACT(MONTH FROM DATETIME "2024-01-02 14:00:00") AS month,
  EXTRACT(DAYOFWEEK FROM DATETIME "2024-01-23 14:00:00") AS day_of_week
# 결과: 2024, 1, 3 (화요일)
```

## (선택) 03.

```
개념 이름: TIMESTAMP
개념 설명: 특정 시점에 도장을 찍은 값으로, UTC부터 경과한 시간을 나타내고 타임존 정보가 있음 (예: 2023-12-31 14:00:00 UTC). 많은 회사 테이블에서 시간이 TIMESTAMP로 저장되어 있음
헷갈린 점: DATETIME이랑 헷갈렸음. TIMESTAMP는 결과에 UTC가 붙고 한국 시간보다 9시간 느리고, DATETIME은 타임존 정보가 없고 T가 붙어서 나오며 한국 Zone을 쓰면 한국 시간과 같음. 그래서 한국 시간으로 보려면 DATETIME(CURRENT_TIMESTAMP(), 'Asia/Seoul')처럼 변환해야 함
```

---

# 2️⃣ 수행 인증란

![수행 인증](images/스크린샷%202026-10-04%20오후%2010.24.06.png)
---

# 3️⃣ 확인 문제

프로그래머스는 로그인이 필요하므로, 로그인 후 문제 풀이를 진행해주세요.

## 🧩 문제 1

문제 링크: [자동차 대여 기록에서 장기/단기 대여 구분하기](https://school.programmers.co.kr/learn/courses/30/lessons/151138)

풀이 과정:

```
- 장기/단기 대여를 나눈 기준: 대여 기간이 30일 이상이면 '장기 대여', 아니면 '단기 대여'
- 사용한 날짜 계산 방식: DATEDIFF(END_DATE, START_DATE) + 1 (시작일과 종료일을 둘 다 포함하려고 +1, 강의의 DATETIME_DIFF와 같은 역할). 2022년 9월 시작 건만 DATE_FORMAT(START_DATE, '%Y-%m') = '2022-09'로 거르고, 날짜는 DATE_FORMAT으로 'YYYY-MM-DD' 형태로 출력 (강의의 FORMAT_DATETIME과 같은 역할)
- CASE WHEN으로 만든 컬럼: RENT_TYPE
```

```sql
SELECT HISTORY_ID, CAR_ID,
       DATE_FORMAT(START_DATE, '%Y-%m-%d') AS START_DATE,
       DATE_FORMAT(END_DATE, '%Y-%m-%d') AS END_DATE,
       CASE
           WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30 THEN '장기 대여'
           ELSE '단기 대여'
       END AS RENT_TYPE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE DATE_FORMAT(START_DATE, '%Y-%m') = '2022-09'
ORDER BY HISTORY_ID DESC;
```

![수행 인증](images/스크린샷%202026-10-04%20오후%2010.21.42.png)

## 🧩 문제 2

문제 링크: [한 해에 잡은 물고기 수 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/298516)

풀이 과정:

```
- 문제에서 요구한 연도: 2021년
- 사용한 날짜 조건: YEAR(TIME) = 2021 (강의의 EXTRACT(YEAR FROM ...)와 같은 역할)
- 집계한 대상: 2021년에 잡은 물고기 수 → COUNT(*)를 FISH_COUNT로 출력
```

```sql
SELECT COUNT(*) AS FISH_COUNT
FROM FISH_INFO
WHERE YEAR(TIME) = 2021;
```

![수행 인증](images/스크린샷%202026-10-04%20오후%2010.22.12.png)

## 🧩 문제 3

문제 링크: [조건에 부합하는 중고거래 상태 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/164672)

풀이 과정:

```
- 날짜 조건: CREATED_DATE가 2022-10-05인 게시글만
- CASE WHEN으로 바꾼 값: SALE → '판매중', RESERVED → '예약중'
- ELSE에 해당하는 경우: 그 외(DONE) → '거래완료'
- 정렬 기준: BOARD_ID 내림차순
```

```sql
SELECT BOARD_ID, WRITER_ID, TITLE, PRICE,
       CASE
           WHEN STATUS = 'SALE' THEN '판매중'
           WHEN STATUS = 'RESERVED' THEN '예약중'
           ELSE '거래완료'
       END AS STATUS
FROM USED_GOODS_BOARD
WHERE CREATED_DATE = '2022-10-05'
ORDER BY BOARD_ID DESC;
```

![수행 인증](images/스크린샷%202026-10-04%20오후%2010.23.17.png)

## 🧩 문제 4

문제 링크: [자동차 평균 대여 기간 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/157342)

풀이 과정:

```
- GROUP BY 기준: CAR_ID (자동차별 평균을 구해야 해서)
- 평균을 계산한 방식: AVG(DATEDIFF(END_DATE, START_DATE) + 1)을 ROUND(..., 1)로 소수 첫째 자리까지 표시 (시작일, 종료일 포함이라 +1)
- HAVING에 사용한 조건: 평균 대여 기간이 7일 이상
- 처음 헷갈렸던 점: 평균은 GROUP BY 이후에 계산되는 값이라 WHERE가 아니라 HAVING으로 걸러야 한다는 점, +1을 빼먹기 쉬웠음
```

```sql
SELECT CAR_ID, ROUND(AVG(DATEDIFF(END_DATE, START_DATE) + 1), 1) AS AVERAGE_DURATION
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
GROUP BY CAR_ID
HAVING AVG(DATEDIFF(END_DATE, START_DATE) + 1) >= 7
ORDER BY AVERAGE_DURATION DESC, CAR_ID DESC;
```

![수행 인증](images/스크린샷%202026-10-04%20오후%2010.23.35.png)

---

# 4️⃣ 이번 주 회고

```
1. 날짜 함수 중 가장 헷갈린 함수: EXTRACT와 DATETIME_TRUNC. 강의에서 말한 것처럼 00:00:00 같은 시간 형태까지 필요한지 생각해보고 고르면 됨
2. CASE WHEN을 사용할 때 기억해야 할 문법: WHEN 조건 THEN 결과를 위에서부터 순서대로 검사하고 앞 조건이 우선이라 순서가 중요함. 마지막은 ELSE로 받고 END AS 컬럼명으로 마무리
3. 날짜/시간 데이터나 조건문을 활용해보고 싶은 분석 상황: DATETIME_TRUNC로 1시간 단위 수요를 집계해보거나, CASE WHEN으로 비슷한 카테고리를 하나로 합쳐서 분석해보고 싶음
```

수고하셨습니다!



