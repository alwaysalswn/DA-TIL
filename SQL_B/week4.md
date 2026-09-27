# 📘 SQL_BASIC 4주차 정규 과제

SQL_BASIC 정규 과제는 매주 정해진 분량의 `초보자를 위한 BigQuery(SQL) 입문` 강의를 듣고, 핵심 개념을 정리한 뒤 간단한 SQL 문제를 직접 풀어보는 방식으로 진행합니다.

이번 주는 SQL 쿼리를 작성하는 흐름, 쿼리 작성 템플릿, 데이터 타입 변환, 문자열 함수를 학습합니다.

완성된 과제는 Github에 업로드하고, 링크를 스프레드시트 'SQL' 시트에 입력해 제출해주세요.

**👀 수행 인증란은 필수입니다.**

---

## 📚 SQL_BASIC_4th_TIL

### 섹션 4. SQL 쿼리 잘 작성하기, 쿼리 작성 템플릿 및 오류를 잘 디버깅하기

### 3-2. SQL 쿼리를 작성하는 흐름

### 3-3. 쿼리 작성 템플릿과 생산성 도구

### 섹션 5. 데이터 탐색 - 변환

### 4-1. INTRO

### 4-2. 데이터 타입과 데이터 변환(CAST, SAFE_CAST)

### 4-3. 문자열 함수(CONCAT, SPLIT, REPLACE, TRIM, UPPER)

---

## ✨ 선택 강의

- 3-4. 오류를 디버깅하는 방법: 오류 메시지 해석과 디버깅 흐름을 더 익히고 싶을 때 선택 수강

---

## 🏁 전체 강의 수강 계획

| 주차 | 필수 강의 범위 | 선택 강의 | 완료 여부 |
| --- | --- | --- | --- |
| 1주차 | 1-1 ~ 2-1 | 1 | ✅ |
| 2주차 | 2-2 ~ 2-3 | 2-4 | ✅ |
| 3주차 | 2-5, 2-7 ~ 2-8 | 2-6 | ✅ |
| 4주차 | 3-2 ~ 4-3 | 3-4 | ✅ |
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
- 쿼리 작성 순서
- 쿼리 작성 템플릿
- 데이터 타입
- CAST
- SAFE_CAST
- CONCAT
- REPLACE
- TRIM

## 01.

```
개념 이름: 쿼리 작성 순서
개념 설명: 쿼리를 바로 짜지 않고 지표 고민 → 지표 구체화 → 유사 사례 탐색 → 쿼리 작성 → 데이터 정합성 확인 → 쿼리 가독성 정리 → 쿼리 저장 순서로 진행. 뭘 확인하고 싶은 건지 안 정하고 SELECT부터 치면 나중에 다시 갈아엎게 되는데, 순서대로 밟으면 그런 시행착오가 줄어듦
예시 쿼리:
SELECT FACTORY_ID, FACTORY_NAME, ADDRESS
FROM FOOD_FACTORY
WHERE ADDRESS LIKE '강원도%'
```

## 02.

```
개념 이름: SAFE_CAST
개념 설명: 데이터 타입을 바꾸다가 변환이 애초에 불가능한 값을 만나면 CAST는 에러를 내면서 쿼리 전체가 멈춰버리는데, SAFE_CAST는 변환에 실패해도 에러 대신 NULL을 반환해서 나머지 행은 그대로 살아있음. 실패할 수도 있는 변환에는 SAFE_CAST를 쓰는 게 안전함
예시 쿼리:
SELECT SAFE_CAST("카일스쿨" AS INT64) AS result
```

## (선택) 03.

```
개념 이름: REPLACE
개념 설명: REPLACE(문자열 원본, 찾을 단어, 바꿀 단어) 형태로 문자열 안의 특정 단어를 다른 단어로 바꿔주는 함수
헷갈린 점: 찾는 단어가 문자열 안에 여러 번 나오면 하나만 바뀌는 줄 알았는데, 나오는 곳마다 전부 다 바뀐다는 걸 알게 됨
```

---

# 2️⃣ 수행 인증란

![수행 인증](images/스크린샷%202026-09-28%20오전%201.47.32.png)

---

# 3️⃣ 확인 문제

프로그래머스는 로그인이 필요하므로, 로그인 후 문제 풀이를 진행해주세요.

## 🧩 문제 1

문제 링크: [특정 옵션이 포함된 자동차 리스트 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/157343)

풀이 과정:

```
- 찾으려는 문자열 조건: OPTIONS 컬럼에 '네비게이션'이라는 단어가 포함된 행
- 사용한 문자열 조건 문법: LIKE '%네비게이션%' (컬럼 값 중간 어디든 그 단어가 들어있으면 걸리도록 앞뒤에 % 다 붙임)
- 정렬 기준: CAR_ID 내림차순(DESC)
```

```sql
SELECT CAR_ID, CAR_TYPE, DAILY_FEE, OPTIONS
FROM CAR_RENTAL_COMPANY_CAR
WHERE OPTIONS LIKE '%네비게이션%'
ORDER BY CAR_ID DESC;
```

![수행 인증](images/스크린샷%202026-09-28%20오전%201.59.34.png)

## 🧩 문제 2

문제 링크: [강원도에 위치한 생산공장 목록 출력하기](https://school.programmers.co.kr/learn/courses/30/lessons/131112)

풀이 과정:

```
- 문제에서 요구한 조건: ADDRESS가 '강원도'로 시작하는 공장만 조회
- WHERE 절로 옮긴 방식: ADDRESS LIKE '강원도%' (문자열이 강원도로 "시작"하는 조건이라 %를 뒤에만 붙임)
- 정렬 기준: FACTORY_ID 오름차순
```

```sql
SELECT FACTORY_ID, FACTORY_NAME, ADDRESS
FROM FOOD_FACTORY
WHERE ADDRESS LIKE '강원도%'
ORDER BY FACTORY_ID;
```

![수행 인증](images/스크린샷%202026-09-28%20오전%201.59.44.png)

## 🧩 문제 3

문제 링크: [이름에 el이 들어가는 동물 찾기](https://school.programmers.co.kr/learn/courses/30/lessons/59047)

풀이 과정:

```
- 찾으려는 문자열 패턴: ANIMAL_TYPE이 'Dog'(개)이면서 이름(NAME)에 'EL'이라는 두 글자가 들어가는 동물
- 대소문자를 처리한 방식: UPPER(NAME)로 이름을 전부 대문자로 바꾼 뒤 LIKE '%EL%'로 검색해서 el/EL/El 상관없이 다 잡히게 함
- 정렬 기준: NAME 오름차순, ANIMAL_ID 오름차순
```

```sql
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE ANIMAL_TYPE = 'Dog'
  AND UPPER(NAME) LIKE '%EL%'
ORDER BY NAME ASC, ANIMAL_ID ASC;
```

![수행 인증](images/스크린샷%202026-09-28%20오전%202.04.36.png)

## 🧩 문제 4

문제 링크: [카테고리 별 상품 개수 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131529)

풀이 과정:

```
- 추출한 문자열 범위: PRODUCT_CODE 앞 2자리 (카테고리 코드)
- 그룹화 기준: 앞 2자리 코드로 GROUP BY 해서 COUNT(*)로 상품 개수 집계
- 정렬 기준: 카테고리 코드 오름차순
```

```sql
SELECT LEFT(PRODUCT_CODE, 2) AS CATEGORY, COUNT(*) AS PRODUCTS
FROM PRODUCT
GROUP BY LEFT(PRODUCT_CODE, 2)
ORDER BY LEFT(PRODUCT_CODE, 2);
```

![수행 인증](images/스크린샷%202026-09-28%20오전%202.04.51.png)

---

# 4️⃣ 이번 주 회고

```
1. 쿼리 작성 흐름을 잡을 때 도움이 된 방법: SELECT부터 바로 치지 않고 목표/테이블/조건을 주석으로 먼저 적어두니까 문제에서 요구하는 조건을 빼먹는 일이 줄었다
2. 타입 변환이나 문자열 처리에서 조심해야 할 점: CAST는 변환이 실패하면 쿼리 자체가 에러 나서, 실패 가능성이 있으면 SAFE_CAST로 미리 방어해야 한다. LIKE 검색도 대소문자를 구분하니까 UPPER/LOWER로 맞춰주는 습관이 필요하다
3. 앞으로 문제 풀이 때 먼저 확인할 것: 조건이 "~로 시작"인지 "~를 포함"인지부터 구분해서 LIKE 패턴에 %를 앞/뒤 중 어디에 붙일지부터 정하기
```

수고하셨습니다!



