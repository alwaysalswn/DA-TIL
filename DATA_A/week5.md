# 데이터분석 5주차 정규과제

📌데이터분석 정규과제는 매주 정해진 분량의 『*혼자 공부하는 데이터 분석 with 파이썬*』 을 읽고 학습하는 것입니다. 이번 주는 아래의 **DataAnalysis_5th_TIL**에 나열된 분량을 읽고 공부하시면 됩니다.

아래의 문제를 풀어보며 학습 내용을 점검하세요. 문제를 해결하는 과정에서 개념을 스스로 정리하고, 필요한 경우 제시된 강의를 참고하여 보완하는 것이 좋습니다.

<!-- 강의 링크는 아래와 같습니다.
https://www.youtube.com/watch?v=ho0LZ6GWhtc&list=PLVsNizTWUw7FGzSRCkQrPEEe-ljVXgS7k&index=10
https://www.youtube.com/watch?v=deYY4xHsI0o&list=PLVsNizTWUw7FGzSRCkQrPEEe-ljVXgS7k&index=11
-->


## DataAnalysis_5th_TIL

### 5장 데이터 시각화하기
#### 01. 맷플롯립 기본 요소 알아보기

- 피겨(Figure): 그래프 구성 요소를 모두 담는 최상위 객체. `figure()`로 명시적으로 만들면 옵션을 조절할 수 있음
- `figsize=(너비, 높이)`: 그래프 크기를 인치 단위 튜플로 지정 (기본 (6, 4)). 기본 DPI가 72라서 픽셀 크기로 정하고 싶으면 `figsize=(900/72, 600/72)`처럼 DPI로 나눠서 넣음
- `dpi` 매개변수: DPI를 늘리면 그래프와 안의 구성 요소가 같이 커짐 (figsize는 캔버스 크기, DPI는 돋보기)
- rcParams: 그래프 기본값을 관리하는 객체. 바꾸면 이후 모든 그래프에 적용됨 (`plt.rcParams['scatter.marker'] = '*'`). 한 그래프만 바꾸려면 `marker` 매개변수를 사용
- 서브플롯: 피겨 안의 그래프 영역(Axes). `fig, axs = plt.subplots(행, 열)`로 만들고 `axs[0].scatter()`처럼 그림
  - 서브플롯에서는 `set_title()`, `set_xlabel()`, `set_ylabel()`, `set_yscale()`처럼 `set_`이 붙은 메서드를 사용

## 02. 선 그래프와 막대 그래프 그리기

- 데이터 준비: `value_counts()`는 값 기준 내림차순이라 연도별로 볼 때는 `sort_index()`로 인덱스 순 정렬. 잘못된 연도는 불리언 인덱싱으로 걸러냄
- 선 그래프 `plot(x, y)`: `title()`, `xlabel()`, `ylabel()`로 제목과 축 이름 지정. `linestyle`(실선 `'-'`, 점선 `':'`, 쇄선 `'-.'`, 파선 `'--'`), `color`, `marker`로 모양을 바꾸고 `'*-g'`처럼 문자열 하나로도 쓸 수 있음
- `xticks()`로 눈금을 지정하고 `annotate(텍스트, (x, y))`로 그래프에 값을 표시 (`xytext`, `textcoords='offset points'`로 위치 조절)
- 막대 그래프 `bar(x, y)`: `width`로 두께, `color`로 색 지정. 텍스트 정렬은 `ha='center'`
- 가로 막대 `barh()`: 두께는 `height`, 텍스트 정렬은 `va`. x/y 이름과 annotate 좌표를 바꿔서 써야 함
- `savefig()`: 그래프를 이미지로 저장. `show()` 전에 호출해야 함


# 2️⃣ 수행 인증

![수행인증](images/스크린샷%202026-10-04%20오후%2010.13.05.png)
![수행인증](images/스크린샷%202026-10-04%20오후%2010.13.19.png)
![수행인증](images/스크린샷%202026-10-04%20오후%2010.13.33.png)



<br>
<br>

# 3️⃣ 확인 문제

## 문제 1.

> **🧚Q. 다음 데이터를 이용하여 matplotlib으로 선그래프를 그리는 코드를 작성해주세요.**
- x = [1, 2, 3, 4, 5]
- y = [2, 4, 6, 8, 10]
> 조건은 아래와 같습니다.
```
1️⃣ 제목은 "Linear Trend"로 설정해주세요.
2️⃣ x축 이름은 "X values"로 설정해주세요.
3️⃣ y축 이름은 "Y values"로 설정해주세요.
4️⃣ 마커(marker)를 포함하여 선그래프를 그려주세요.
```

```python
import matplotlib.pyplot as plt

x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]

plt.plot(x, y, marker='o')
plt.title('Linear Trend')
plt.xlabel('X values')
plt.ylabel('Y values')
plt.show()
```



### 🎉 수고하셨습니다.