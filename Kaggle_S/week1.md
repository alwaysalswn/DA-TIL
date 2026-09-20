# 🐋 Kaggle_S 1주차 - 캐글 필사 스터디

## 📓 Urban Analytics: Weather, Traffic & Grid ML

#### 전체 흐름
개별 도메인(날씨/교통/전력/대기질/교통수단) 탐색(2~6) → 이 도메인들을 하나의 master 데이터로 통합(7) → 통합 데이터로 도메인 간 교차분석(8~10) → 지금까지 발견한 인사이트 종합(11) → 그 인사이트를 실제 예측모델 2개(전력수요 회귀, 교통사고 분류)로 구현(12~13) → 도시 운영자 대상 실무 제안(14)으로 마무리.

즉 "EDA에서 끝나지 않고, EDA로 발견한 패턴을 실제 예측모델로 검증하는 구조"라는 게 핵심 흐름.

#### 종합 요약
스마트시티의 9개 도메인 센서 데이터(날씨/교통/전력/대기질/교통수단/긴급출동/도시이벤트/구역정보)를 개별 탐색한 뒤 하나의 master 데이터로 통합하고, 교차분석과 가설검정(t-test, 카이제곱)을 거쳐 최종적으로 전력수요 예측(회귀)과 교통사고 위험 예측(분류) 두 개의 머신러닝 모델까지 구축하는 종합 파이프라인.

#### 🌟 종합 인상깊은 점
결측치를 sensor_status와 대조해서 "무작위가 아니라 센서 다운타임 때문"이라고 원인까지 검증한 점, 여러 도메인 데이터를 딕셔너리 컴프리헨션으로 간결하게 처리한 점, 그래프로 "차이 있어 보인다"에서 끝내지 않고 t-검정·카이제곱검정으로 통계적 유의성까지 확인한 점, 사고예측 모델에서 precision보다 recall을 일부러 높게 유지해 공공안전 문제의 특성을 모델 설계에 반영한 판단이 인상적이었다.

#### 🔧 종합 개선 방향
여러 통계적 가설검정(t-test, 카이제곱)을 하면서 다중비교 보정이 없었고, 두 예측모델 모두 단일 모델(LightGBM)만 써서 비교 baseline이 없었으며, 분류모델은 임계값(0.5)을 그대로 써서 최적화하지 않았다. 통계적 엄밀성(보정, 효과크기)과 모델 검증(baseline 비교, 임계값 튜닝) 두 축을 보완하면 신뢰도가 더 높아질 것.

#### 📊 도출 인사이트
1. 전력수요는 계절성이 뚜렷하고 기온과 강하게 연동돼(냉방/난방 수요), 온도 기반 회귀모델(R²=0.996, MAE=7.78MWh)로 매우 정확하게 예측 가능하다.
2. 러시아워 혼잡도(평균 59.32)는 비러시아워(21.13)보다 통계적으로 유의하게 높다(p<0.001) — 반면 평일/주말 전력수요 차이는 유의하지 않다(p=0.88). 즉 시간대는 교통에, 요일은 전력수요엔 큰 영향이 없다.
3. 폭우 발생 시 사고 비율이 뚜렷하게 높아지는 유의한 연관성이 확인된다(카이제곱 검정, p<0.001) — 기상 조건이 교통사고 위험의 핵심 변수다.
4. 날씨·교통 관련 긴급출동은 그렇지 않은 경우보다 대응시간이 눈에 띄게 길다(날씨 관련 33.67분 vs 21.15분) — 기상/교통 상황이 응급대응의 병목이 됨을 시사한다.
5. 상업·산업지구가 주거지구보다 전력수요와 도로 혼잡도를 훨씬 많이 유발하며, 결측치조차 무작위가 아니라 센서 다운(폭풍/정전) 시점에 집중돼 있어 인프라 취약 시점과 데이터 품질 저하가 겹친다.

---

### 0. Setup

```python
import warnings
warnings.filterwarnings('ignore')

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

from sklearn.model_selection import train_test_split
from sklearn.metrics import (mean_absolute_error, mean_squared_error, r2_score, classification_report, confusion_matrix, roc_auc_score, roc_curve)
import lightgbm as lgb

sns.set_style('whitegrid')
plt.rcParams['figure.figsize'] = (10, 5)
plt.rcParams['axes.titlesize'] = 13
plt.rcParams['axes.titleweight'] = 'bold'
pd.set_option('display.max_columns', 50)

RANDOM_STATE = 42
print("Libraries loaded successfully.")

# TIL: 기본 EDA 라이브러리 외에 scipy.stats, sklearn(train_test_split, MAE/MSE/R², classification_report/confusion_matrix/roc_auc_score), lightgbm까지 미리 임포트 — 회귀+분류 모델을 둘 다 준비.
# plt.rcParams로 그래프 스타일 통일, RANDOM_STATE 상수로 재현성 확보.
```

### 2. Data Cleaning

```python
missing_summary = pd.DataFrame({name: df.isnull().sum() for name, df in datasets.items() if name != 'districts'}, index=['total_missing_values']).T
missing_summary['pct_of_cells'] = ([datasets[n].isnull().sum().sum()/(datasets[n].shape[0]*datasets[n].shape[1])*100 for n in missing_summary.index])
missing_summary.sort_values('total_missing_values', ascending=False)

print(weather.loc[weather.sensor_status != 'Normal', 'temperature_c'].isnull().mean() * 100)
print(weather.loc[weather.sensor_status == 'Normal', 'temperature_c'].isnull().mean() * 100)

for name in ['weather', 'traffic', 'power_grid', 'air_quality', 'public_transport']:
    dup = datasets[name].duplicated(subset=['timestamp', 'district_id']).sum()

traffic = traffic.drop_duplicates(subset=['timestamp', 'district_id']).reset_index(drop=True)
air_quality = air_quality.drop_duplicates(subset=['timestamp', 'district_id']).reset_index(drop=True)

# TIL: 딕셔너리 컴프리헨션으로 6개 데이터셋의 결측치를 한 번에 집계 → sensor_status와 교차검증해서 결측 원인 진단 → duplicated(subset=[...])로 중복 키 확인 후 drop_duplicates로 제거.
# 흐름: 집계 → 원인 진단 → 중복 처리.
```

### 4. Statistical & Univariate Analysis
아워 혼잡도(p<0.001)·폭우-사고 연관성(p<0.001)은 유의함.
```
```python
print("Skewness — traffic_volume:", round(traffic['traffic_volume'].skew(), 2))

demand_days = power_grid[['timestamp','district_id','electricity_demand_mwh']].merge(
    traffic[['timestamp','district_id','weekend_flag']], on=['timestamp','district_id'], how='inner')
t_stat, p_val = stats.ttest_ind(weekday_demand, weekend_demand, equal_var=False)

t_stat2, p_val2 = stats.ttest_ind(rush, non_rush, equal_var=False)

contingency = pd.crosstab(rain_acc['heavy_rain_flag'], rain_acc['accident_flag'])
chi2, p_val3, dof, expected = stats.chi2_contingency(contingency)

# TIL: 분포/왜도(skew()) 확인 → stats.ttest_ind(독립표본 t검정, equal_var=False → Welch's t-test)로 두 그룹(평일/주말, 러시아워/비러시아워) 평균 차이 검정 → stats.chi2_contingency(카이제곱 검정)로 범주형 변수 연관성 검정.
# 결과: 평일/주말 차이는 유의하지 않음(p=0.88), 러시

### 7. Multivariate / Cross-Domain Anal

```python
corr_cols = ['temperature_c','humidity_percent','rainfall_mm','wind_speed_kmh',
             'traffic_volume','congestion_index','average_speed_kmh',
             'electricity_demand_mwh','pm25','synthetic_aqi',
             'passenger_count','population_density','commercial_activity_index']
sns.heatmap(master[corr_cols].corr(), annot=True, fmt='.2f', cmap='coolwarm', center=0)

agg = master.groupby('district_type').agg(
    avg_demand=('electricity_demand_mwh','mean'),
    avg_congestion=('congestion_index','mean'),
    avg_aqi=('synthetic_aqi','mean')
).sort_values('avg_demand')

sns.boxplot(data=master, x='hour', y='electricity_demand_mwh', showfliers=False)
sns.boxplot(data=master, x='rush_hour_flag', y='congestion_index')

# TIL: 5개 센서 테이블+district 정적 정보를 merge()로 합친 master에서 .corr()+heatmap으로 도메인 간 상관관계 확인 → groupby('district_type').agg()로 지역 유형별 평균 비교 → boxplot으로 시간대별/러시아워별 분포 비교.
```

### 12. Machine Learning — Traffic Accident Risk Prediction (Classification)

```python
clf_df = master[clf_features_num + clf_features_cat + [clf_target, 'timestamp']].dropna()
clf_df = pd.get_dummies(clf_df, columns=clf_features_cat, drop_first=True)

neg, pos = (y_train_c == 0).sum(), (y_train_c == 1).sum()
scale_pos_weight = neg / pos

clf_model = lgb.LGBMClassifier(
    n_estimators=400, learning_rate=0.05, num_leaves=31,
    scale_pos_weight=scale_pos_weight, random_state=RANDOM_STATE, verbose=-1)
clf_model.fit(X_train_c, y_train_c)

pred_proba = clf_model.predict_proba(X_test_c)[:, 1]
pred_c = (pred_proba >= 0.5).astype(int)

print(classification_report(y_test_c, pred_c, target_names=['No Accident','Accident']))
print("ROC-AUC:", round(roc_auc_score(y_test_c, pred_proba), 3))

# TIL: 사고 발생 비율(3.44%)이 심하게 불균형할 때 scale_pos_weight로 소수 클래스에 가중치를 주는 법, predict_proba로 확률을 뽑아 임계값(0.5)으로 분류, classification_report/confusion_matrix/roc_auc_score로 평가하는 흐름.
# timestamp 기준으로 train/test를 시간순으로 나누는 방식도 재확인.
```

---

## 📓 Comprehensive data exploration with Python

#### 전체 흐름
타겟 변수(SalePrice) 자체의 분포 확인 → 타겟과 다른 변수들의 관계 탐색(상관관계·산점도) → 결측치 처리 → 이상치 탐지·제거 → 정규성/등분산성 가정 확인 및 로그변환 → 범주형 변수 인코딩(더미변수) 순서.

즉 "타겟 이해 → 관계 탐색 → 데이터 정제(결측/이상치) → 통계적 가정 처리 → 모델 투입 형태로 변환"이라는, 회귀모델 학습 직전까지의 전처리 파이프라인을 보여주는 구조.

#### 종합 요약
House Prices 데이터를 가지고 회귀모델 학습 전 단계까지의 EDA 전 과정을 보여주는 정석 튜토리얼. 타겟변수 분석 → 관계탐색 → 결측치 → 이상치 → 정규성/인코딩까지 순서대로 밟아가며, 매 단계 통계적 근거를 먼저 설명하고 코드를 붙이는 서술형 스타일이 특징.

#### 🌟 종합 인상깊은 점
매 단계마다 왜 이걸 해야 하는지 통계적 근거·직관을 먼저 설명한 뒤 코드를 붙이는 서술 방식이라 결과 코드보다 판단 과정을 따라가며 배우게 된다. 상관관계 분석에서 숫자만 보지 않고 다중공선성 변수쌍을 시각적으로 잡아낸 것, 이상치를 기계적으로 자르지 않고 "진짜 이상값인지" 근거를 설명한 것, 로그변환 전에 정규성 가정이 왜 필요한지부터 설명한 순서가 인상적이었다.

#### 🔧 종합 개선 방향
판단 기준(결측치 삭제, 이상치 판정, 변환 방식 선택)이 시각적 확인과 저자의 경험적 직관에 크게 의존한다 — IQR/Cook's distance/Box-Cox/다중공선성(VIF) 같은 정량적 근거를 보완하면 훨씬 재현 가능하고 설득력 있는 분석이 될 것.

#### 📊 도출 인사이트
1. PoolQC·MiscFeature·Alley·Fence 등은 결측 비율이 80% 이상으로, 사실상 "해당 시설이 없음"을 의미하는 정보로 해석해야 하며 단순 결측 처리보다 그 자체가 유의미한 신호다.
2. SalePrice와 상관관계가 가장 높은 변수는 OverallQual(전체 품질)과 GrLivArea(지상 생활면적)로, 집값 예측의 핵심 피처가 될 가능성이 크다.
3. GarageCars-GarageArea, TotalBsmtSF-1stFlrSF처럼 서로 다른 변수인데 사실상 같은 정보를 담은 다중공선성 쌍이 존재해, 모델링 시 변수 선택에 주의가 필요하다.
4. GrLivArea가 매우 큰데도 SalePrice가 오히려 낮은 관측치 2건은 일반적인 가격 결정 패턴을 벗어난 이상치로, 제거 후 학습하는 것이 타당하다.
5. SalePrice는 원래 오른쪽으로 치우친(right-skewed) 분포였지만 로그변환 후 정규분포에 가까워져, 선형회귀처럼 정규성을 가정하는 모델에 더 적합한 형태가 된다.

---

### 4. Missing data

```python
total = df_train.isnull().sum().sort_values(ascending=False)
percent = (df_train.isnull().sum()/df_train.isnull().count()).sort_values(ascending=False)
missing_data = pd.concat([total, percent], axis=1, keys=['Total', 'Percent'])

df_train = df_train.drop((missing_data[missing_data['Total'] > 1]).index, 1)
df_train = df_train.drop(df_train.loc[df_train['Electrical'].isnull()].index)

# TIL: 결측치 개수(isnull().sum())와 비율(/count())을 표로 정리 → 기준(1개 초과) 넘는 컬럼은 drop(axis=1)로 통째 삭제, 1개짜리 결측(Electrical)은 해당 행만 drop().
# 흐름: 확인 → 판단 → 처리.
```

### "'SalePrice', her buddies and her interests"

```python
var = 'GrLivArea'
data = pd.concat([df_train['SalePrice'], df_train[var]], axis=1)
data.plot.scatter(x=var, y='SalePrice', ylim=(0,800000));

# TIL: sns.heatmap()으로 전체 상관관계 → nlargest()로 타겟 기준 상위 변수만 추려 확대 히트맵 → pairplot()으로 변수쌍 산점도.
# 흐름: 전체 관계 훑기 → 타겟 중심으로 좁히기 → 개별 관계 뜯어보기.
```

### "Out liars!"

```python
saleprice_scaled = StandardScaler().fit_transform(df_train['SalePrice'][:,np.newaxis])
low_range = saleprice_scaled[saleprice_scaled[:,0].argsort()][:10]
high_range = saleprice_scaled[saleprice_scaled[:,0].argsort()][-10:]

df_train = df_train.drop(df_train[df_train['Id'] == 1299].index)
df_train = df_train.drop(df_train[df_train['Id'] == 524].index)

# TIL: 표준화(z-score)로 극단값 후보 계산 → 정렬해서 값 확인 → 산점도로 시각 재검증 → Id 기준으로 직접 drop().
# 흐름: 통계적 스크리닝 → 시각적 검증 → 제거.
```

### "5. Getting hard core"와 "Last but not the least, dummy variables"

```python
df_train['SalePrice'] = np.log(df_train['SalePrice'])

df_train['HasBsmt'] = 0
df_train.loc[df_train['TotalBsmtSF']>0, 'HasBsmt'] = 1
df_train.loc[df_train['HasBsmt']==1, 'TotalBsmtSF'] = np.log(df_train['TotalBsmtSF'])

df_train = pd.get_dummies(df_train)

# TIL: stats.probplot으로 정규성 진단 → np.log()로 로그변환 → 0값 많은 변수는 이진 컬럼(HasBsmt)을 새로 만들고 나머지만 로그변환 → pd.get_dummies()로 범주형 원-핫 인코딩.
# 흐름: 진단 → 변환 → 예외처리 → 인코딩.
```

---

## 😅 오늘 자주 낸 실수 유형

처음이라 그냥 따라치기만 하면 쉬울 줄 알았는데, 생각보다 오류가 많이 났다 — 대소문자나 `!-` 같은 것들.

1. **대소문자 실수** — `Ylim`이라고 대문자로 썼다가 `ylim`으로 고침.
2. **인접 키 오타(비교연산자)** — `!=`를 `!-`로 잘못 침.
3. **괄호 누락(메서드 체이닝)** — `.sum.sum()`이라고 써서 괄호 하나를 빼먹음.
4. **문법 구조 자체를 헷갈림** — `import scipy import stats`처럼 `import`를 두 번 씀.
5. **실행 순서/세션 관리 실수** — 셀만 따로 실행해서 위쪽 import가 세션에 없어 `NameError`.

→ 다음부터: (1) 대소문자·특수기호는 원문이랑 한 글자씩 대조, (2) 괄호 개수 세어보기, (3) 새 코드 치기 전에 위쪽 셀 `Run All`로 세션 준비.

---

## 💬 스터디 논의용 메모
- 노트북1은 회귀 EDA 정석 흐름(결측치→관계탐색→이상치→정규성/더미변수), 노트북2는 여러 데이터셋을 하나로 합치는 실전 패턴(딕셔너리 컴프리헨션)+가설검정+예측모델 2개까지 배울 수 있었다.
- 각자 필사한 부분에서 겪은 에러·막힌 점 공유하기.
