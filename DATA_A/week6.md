# 데이터분석 6주차 정규과제

📌데이터분석 정규과제는 매주 정해진 분량의 『*혼자 공부하는 데이터 분석 with 파이썬*』 을 읽고 학습하는 것입니다. 이번 주는 아래의 **DataAnalysis_6th_TIL**에 나열된 분량을 읽고 공부하시면 됩니다.

아래의 문제를 풀어보며 학습 내용을 점검하세요. 문제를 해결하는 과정에서 개념을 스스로 정리하고, 필요한 경우 제시된 강의를 참고하여 보완하는 것이 좋습니다.

<!-- 강의 링크는 아래와 같습니다.
https://www.youtube.com/watch?v=XD65UhBMOiI&list=PLVsNizTWUw7FGzSRCkQrPEEe-ljVXgS7k&index=12
https://www.youtube.com/watch?v=NTQ5NXelOfw&list=PLVsNizTWUw7FGzSRCkQrPEEe-ljVXgS7k&index=13
-->


## DataAnalysis_6th_TIL

### 6장 복잡한 데이터 표현하기
#### 01. 객체지향 API로 그래프 꾸미기
#### 02. 맷플롯립의 고급 기능 배우기


## Study Schedule

| 주차  | 공부 범위     | 완료 여부 |
| ----- | ------------- | --------- |
| 1주차 | p.24~81    | ✅         |
| 2주차 | p.84~151   | ✅         |
| 3주차 | p.154~219  | ✅         |
| 4주차 | p.222~279 | ✅         |
| 5주차 | p.282~325 | ✅         |
| 6주차 | p.328~379 | ✅         |
| 7주차 | p.382~430 | 🍽️         |

<br>

<!-- 여기까진 그대로 둬 주세요-->


# 1️⃣ 개념 정리 

## 01. 객체지향 API로 그래프 꾸미기

- pyplot 방식 vs 객체지향 API: pyplot 방식은 `plt.plot()`, `plt.title()`처럼 함수들이 하나의 피겨 상태를 공유하고, 객체지향 API는 `fig, ax = plt.subplots()`로 피겨와 Axes를 직접 만든 뒤 `ax.plot()`, `ax.set_title()`처럼 객체의 메서드를 씀. 간단한 그래프는 pyplot, 복잡하거나 서브플롯이 여러 개면 객체지향이 좋음
- 한글 출력: 맷플롯립 기본 폰트가 한글을 지원하지 않아 글자가 깨짐. 코랩에서는 `fonts-nanum`을 설치하고 `plt.rc('font', family='NanumBarunGothic')`로 폰트를 바꿔줌
  - `rc()`는 첫 매개변수에 그룹(`'font'`), 그다음 하위 속성(`family`, `size`)을 키워드로 넘기면 여러 설정을 한 번에 지정할 수 있음. `rcParams['font.family']`로 바꿔도 같은 효과
- 산점도 데이터 준비: `value_counts()[:30]`으로 발행 도서가 많은 상위 30개 출판사를 뽑고, `isin()`으로 그 출판사에 해당하는 행의 불리언 인덱스를 만듦. 불리언의 `sum()`은 True 개수라 몇 행인지 셀 수 있고, 너무 많으면 `sample(1000, random_state=42)`로 무작위 추출 (random_state는 같은 결과가 나오도록 고정)
- 마커 꾸미기: `s`(마커 크기), `alpha`(투명도), `edgecolors`(테두리 색), `linewidths`(테두리 두께), `c`(색). `s`와 `c`에 데이터 길이와 같은 배열을 주면 값마다 크기/색이 달라져서 대출건수 같은 정보를 점에 담을 수 있음
- 컬러맵과 컬러 막대: `cmap`으로 값에 따른 색을 정함 (기본 viridis, jet은 파랑→노랑→빨강). `fig.colorbar(sc)`처럼 `scatter()`가 반환한 객체를 넘기면 색이 어떤 값인지 보여주는 막대가 붙고, 서브플롯이면 `ax=`로 위치를 지정
  - 마커 크기로 데이터를 왜곡할 수 있어서 크기를 어떻게 정했는지 같이 알려주는 게 좋음

## 02. 맷플롯립의 고급 기능 배우기

- 선 그래프 여러 개: `plot()`을 여러 번 호출하면 되고 색은 10개를 돌아가며 자동 지정됨. `label=`을 주고 `legend()`를 호출하면 범례가 생기고 `loc='upper left'`처럼 위치를 정함 (기본값 `'best'`)
- `set_xlim()`/`set_ylim()`으로 축 범위를 지정. pyplot에서는 `xlim()`/`ylim()`, 둘을 한 번에 하려면 `axis([xmin, xmax, ymin, ymax])`
- 선이 많이 겹치면 스택 영역 그래프(`stackplot(x, y)`): 선 그래프를 위로 쌓은 형태이고 y는 2차원 배열(행=출판사, 열=연도)이어야 함. 이 모양은 `pivot_table()`로 만듦
- `pivot_table()`: `index`가 행, `columns`가 열, `values`가 집계할 열, `aggfunc`가 집계 방식 (기본 평균). `values` 없이 쓰면 열이 다단(MultiIndex)이라 `get_level_values(1)`로 연도만 꺼내야 하고, 결측값은 `fillna(0)`이나 `fill_value=0`으로 채워줌
  - `groupby()`는 집계 기준이 전부 행 인덱스로 가는데 `pivot_table()`은 행과 열 인덱스로 나뉨
- 막대 그래프 여러 개: `bar()`를 연달아 부르면 앞의 막대를 덮어씀. `width=0.4`로 줄이고 두 번째는 x에 0.2를 더해서 나란히 놓음. 쌓으려면 `bottom=` 매개변수를 쓰거나 `cumsum()`으로 미리 누적해놓고 큰 막대부터(`reversed`) 그림
- 원 그래프 `pie()`: 비율은 자동 계산되고 3시 방향에서 반시계로 그려짐. 크기 비교가 어려워서 `autopct='%.1f%%'`로 비율을 표시하고 `startangle=90`으로 시작 위치, `explode=[0.1]+[0]*9`로 조각 하나를 떼어냄 (explode 길이는 data 길이와 같아야 함)
- 서브플롯 2차원 배치: `plt.subplots(2, 2)`로 만들면 `axes[0, 0]`처럼 행, 열 번호로 접근. 컬러 막대는 `ax=axes[0, 0]`으로 지정하고 `savefig()`로 저장
- 판다스로도 가능: `plot.area()`, `plot.bar(stacked=True)`는 `cumsum()` 없이 스택 그래프를 그림. 세밀한 제어는 맷플롯립이 나음


# 2️⃣ 수행 인증

![수행인증](images/스크린샷%202026-11-01%20오후%206.49.41.png)
![수행인증](images/스크린샷%202026-11-01%20오후%206.49.49.png)
![수행인증](images/스크린샷%202026-11-01%20오후%206.50.18.png)
![수행인증](images/스크린샷%202026-11-01%20오후%206.50.27.png)
![수행인증](images/스크린샷%202026-11-01%20오후%206.50.38.png)


<br>
<br>

# 3️⃣ 확인 문제

## 문제 1.

> **🧚Q. 이번 주차에는 확인문제 대신 그래프 그리기 실습을 진행합니다.
4주차에서 사용했던 캐글 데이터셋을 활용하여, 다양한 요소를 포함한 복잡한 그래프를 직접 작성해주세요.**

```
https://colab.research.google.com/drive/1IKrbjQmaoNUnfZaMnZAmEVfAR3W0WAUl?usp=sharing
```



### 🎉 수고하셨습니다.