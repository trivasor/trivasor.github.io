---
title: "끝에서 끝까지 진행하는 머신러닝 프로젝트 이탈 예측 방법론"
description: ""
coverImage: "/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_0.png"
date: 2024-07-01 21:02
ogImage: 
  url: /assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_0.png
tag: Tech
originalTitle: "End-to-End Machine Learning Project: Churn Prediction"
link: "https://medium.com/@ramazanolmeez/end-to-end-machine-learning-project-churn-prediction-e9c4d0322ac9"
---


![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_0.png)

이 글의 주요 목표는 엔드 투 엔드 머신러닝 프로젝트를 개발하는 것입니다. 모델이 정말 유용하려면 저장 및 배포되어야 합니다. 이 글의 범위 내에서 현실 세계 시나리오를 기반으로 한 프로젝트를 개발하는 것을 목표로 합니다.

GitHub에서 프로젝트에 접근할 수 있습니다. 또한 Kaggle에서 EDA 및 모델 개발 섹션에도 접근할 수 있습니다.

- GitHub
- EDA Kaggle
- Model Development Kaggle

<div class="content-ad"></div>

프로젝트 동안 다음 도구들을 사용할 예정입니다.

- Catboost
- Streamlit
- FastAPI
- Docker

목차
1. 프로젝트의 목적과 데이터 세트
   - 데이터 세트
2. 데이터 전처리 및 모델 개발
3. 인터페이스(Stremlit)
4. API (FastAPI)
5. 자동화 (Docker)
6. 결론

# 1. 프로젝트의 목적과 데이터 세트

<div class="content-ad"></div>

**데이터 세트**

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_2.png)

프로젝트에서 사용할 고객 데이터는 캐글(Kaggle)에서 접근할 수 있습니다. 이 데이터 세트는 7043명의 고객과 21개의 열(특징)으로 구성되어 있습니다. 고객 계정 정보, 인구 통계 정보 및 등록된 서비스를 포함하고 있습니다. 목표 변수(이탈)는 고객이 이탈했는지에 대한 정보를 제공합니다. 또한 이 링크에서 확장 및 업데이트된 데이터 세트 버전을 얻을 수도 있습니다.

<div class="content-ad"></div>

# 2. 데이터 전처리 및 모델 개발

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_3.png)

데이터 세트에는 상당수의 범주형 변수가 있습니다. 이러한 변수들을 숫자 형식으로 인코딩하여 다양한 전처리 기술을 사용하는 다른 모델들도 사용할 수 있습니다. 그러나 이번 연구에서는 범주형 변수와의 성공을 입증한 Catboost 알고리즘을 사용할 것입니다. 모델 개발에서 성능을 향상시키고 싶다면 Optuna 등의 하이퍼파라미터 최적화 도구를 사용해볼 수 있습니다.

파이썬 파일인 train_model.py를 생성하여 데이터 전처리와 모델 개발을 시작할 수 있습니다.

<div class="content-ad"></div>

```python
################################################## 라이브러리 가져오기 ##################################################

import numpy as np
import pandas as pd
import os

from sklearn import metrics
from sklearn.model_selection import train_test_split, StratifiedShuffleSplit
from sklearn.metrics import (
    accuracy_score, classification_report, recall_score, confusion_matrix,
    roc_auc_score, precision_score, f1_score, roc_curve, auc
)
from sklearn.preprocessing import OrdinalEncoder

from catboost import CatBoostClassifier, Pool

################################################## 데이터 불러오기 및 편집하기 ##################################################

data_path = "../data/WA_Fn-UseC_-Telco-Customer-Churn.csv"
df = pd.read_csv(data_path)

# TotalCharges를 숫자형으로 변환하고 NaN 값 채우기
df['TotalCharges'] = pd.to_numeric(df['TotalCharges'], errors='coerce')
df['TotalCharges'].fillna(df['tenure'] * df['MonthlyCharges'], inplace=True)

# SeniorCitizen을 객체형으로 변환
df['SeniorCitizen'] = df['SeniorCitizen'].astype(object)

# 'No phone service' 및 'No internet service'를 특정 열에서 'No'로 교체
df['MultipleLines'] = df['MultipleLines'].replace('No phone service', 'No')
columns_to_replace = ['OnlineSecurity', 'OnlineBackup', 'DeviceProtection', 'TechSupport', 'StreamingTV', 'StreamingMovies']
for column in columns_to_replace:
    df[column] = df[column].replace('No internet service', 'No')

# 'Churn' 범주형 변수를 숫자형으로 변환
df['Churn'] = df['Churn'].replace({'No': 0, 'Yes': 1})

################################################## StratifiedShuffleSplit ##################################################

# StratifiedShuffleSplit 객체 생성
strat_split = StratifiedShuffleSplit(n_splits=1, test_size=0.2, random_state=64)

train_index, test_index = next(strat_split.split(df, df["Churn"]))

# 학습 및 테스트 세트 생성
strat_train_set = df.loc[train_index]
strat_test_set = df.loc[test_index]

X_train = strat_train_set.drop("Churn", axis=1)
y_train = strat_train_set["Churn"].copy()

X_test = strat_test_set.drop("Churn", axis=1)
y_test = strat_test_set["Churn"].copy()

################################################## CATBOOST ##################################################

# 범주형 열 식별
categorical_columns = df.select_dtypes(include=['object']).columns.tolist()

# CatBoostClassifier 초기화 및 학습
cat_model = CatBoostClassifier(verbose=False, random_state=0, scale_pos_weight=3)
cat_model.fit(X_train, y_train, cat_features=categorical_columns, eval_set=(X_test, y_test))

# 테스트 세트에서 예측
y_pred = cat_model.predict(X_test)

# 평가 지표 계산
accuracy, recall, roc_auc, precision = [round(metric(y_test, y_pred), 4) for metric in [accuracy_score, recall_score, roc_auc_score, precision_score]]

# 결과 저장용 DataFrame 생성
model_names = ['CatBoost_Model']
result = pd.DataFrame({'Accuracy': accuracy, 'Recall': recall, 'Roc_Auc': roc_auc, 'Precision': precision}, index=model_names)

# 결과 출력
print(result)

# 모델을 'model' 디렉토리에 저장
model_dir = "../model"
if not os.path.exists(model_dir):
    os.makedirs(model_dir)

model_path = os.path.join(model_dir, "catboost_model.cbm")
cat_model.save_model(model_path)
```

**단계 1: 라이브러리 가져오기**
데이터 처리, 모델 학습 및 평가에 필요한 필수 라이브러리를 가져오는 것으로 시작합니다. 이에는 데이터 조작을 위한 NumPy 및 Pandas, 머신러닝 기능을 제공하는 scikit-learn, 그리고 예측 모델 구축을 위한 CatBoost 등이 포함됩니다.

**단계 2: 데이터 불러오기 및 편집하기**
다음으로 Pandas를 사용하여 지정된 경로에서 데이터셋을 로드합니다. 데이터 일관성을 보장하고 모델링에 대비하기 위해 초기 데이터 편집 단계를 수행합니다. 이는 특정 열을 숫자 데이터 유형으로 변환하고 누락된 값 처리, 범주형 변수 인코딩을 포함합니다.

**단계 3: Stratified Splitting**
EDA(탐색적 데이터 분석) 연구에서 데이터셋의 불균형성을 관찰했습니다. 신뢰할 수 있는 모델 평가를 위해 학습 및 테스트 세트에서 클래스의 비율을 유지하기 위해 scikit-learn의 StratifiedShuffleSplit을 사용합니다. 이 방법은 학습 및 테스트 세트의 클래스 비율을 보존하여 신뢰할 수 있는 모델 평가에 중요합니다.

<div class="content-ad"></div>

### 단계 4: CatBoost를 사용한 모델 훈련
CatBoost를 사용하여 예측 모델을 훈련합니다. 우리는 적절한 매개변수를 가진 CatBoostClassifier를 초기화합니다. 여기에는 자세성(verbosity)과 랜덤 상태(random state)가 포함됩니다. 우리는 모델을 훈련 데이터에 맞추며, 범주형 변수를 올바르게 처리하기 위해 범주형 기능을 지정합니다. 모델 훈련 단계가 자세히 다루어지지 않았음을 강조하고, 여러분의 작업에서 이 단계를 다시 검토하는 것이 좋을 수도 있습니다.

### 단계 5: 모델 평가
모델이 훈련된 후, 정확도, 재현율, ROC AUC 점수, 정밀도 등과 같은 다양한 메트릭을 사용하여 성능을 평가합니다. 이러한 메트릭은 우리 모델이 고객 이탈을 얼마나 잘 예측하는지에 대한 통찰력을 제공합니다.

데이터를 분석하고 머신러닝 모델을 구축한 후, 이 모델의 사용성을 높이기 위해 대화형 인터페이스가 필요합니다. 이 작업에는 Streamlit이 좋은 대안입니다.

<div class="content-ad"></div>

# 3. 인터페이스 (Streamlit)

![2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_4.png](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_4.png)

Streamlit은 머신러닝과 데이터 과학 팀을 위한 오픈 소스 응용 프로그램 프레임워크입니다. 프로젝트의 프로토타입 단계에서 자주 사용되며 빠른 인터페이스를 제공합니다. 사용하기 쉽고 강력한 커뮤니티를 가지고 있습니다.

빠른 인터페이스를 만들기 위해 streamlit-app.py라는 파이썬 파일을 생성하여 프로젝트를 시작했습니다. 다음 코드는 Streamlit을 사용하여 상호작용 인터페이스를 만들고 CatBoost 모델을 사용하여 고객의 이탈 확률을 평가할 수 있게 해줍니다. 또한 SHAP 값을 사용하여 모델의 결정을 이해하고 시각화할 수 있습니다.

<div class="content-ad"></div>

프로젝트에서 SHAP에 대해 간략히 언급하는 것이 좋겠네요.

![링크](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_5.png)

SHAP (SHapley Additive exPlanations)은 게임 이론적 접근 방식으로 개발된 설명 가능한 인공지능 방법 중 하나입니다. 모델에 대한 설명을 가능하게 하여 블랙박스의 성격을 줄입니다. 이를 통해 모델에서 사용된 각 속성이 모델 결과에 어느 정도 영향을 미치는지 알려줍니다.

이제 streamlit-app.py 파일을 계속 만들어보도록 하죠.

<div class="content-ad"></div>


# Step 1: Setting Up
먼저 필요한 라이브러리를 가져옵니다. 이 중에 Streamlit, 데이터 조작을 위한 Pandas, 훈련된 모델을 불러오기 위한 CatBoost, 모델 해석을 시각화하기 위한 SHAP 등이 있습니다.

# Step 2: Data Loading and Model Loading
앱은 전처리된 데이터와 훈련된 CatBoost 모델을 불러옵니다. 이를 통해 필요한 데이터와 모델이 추론에 준비되어 있는지 확인합니다.

# Step 3: User Interface Components
앱은 다양한 옵션을 갖춘 사용자 인터페이스를 제공합니다:


<div class="content-ad"></div>

- 기능 중요도: SHAP 값에 기반한 기능 중요도 요약을 사용자가 볼 수 있게 합니다.
- 사용자별 SHAP: 특정 고객을 선택하고 해당 고객의 SHAP 값 시각화를 할 수 있어 모델의 예측 근거를 이해하는 데 도움이 됩니다.
- CHURN 확률 계산: 사용자가 가상의 고객 데이터를 입력하고 훈련된 모델을 사용해 이탈 확률을 계산할 수 있게 합니다.

**단계 4: 사용자 데이터 로딩과 준비**
사용자가 "CHURN 확률 계산" 옵션을 선택하면 앱이 해당 고객의 성별, 재직기간, 계약 유형 등과 같은 다양한 고객 속성을 입력하도록 요구합니다. 확인 버튼을 클릭하면 이 입력 데이터를 데이터프레임으로 형식화하고 모델에 공급하여 해당 고객의 이탈 확률을 예측합니다.

**단계 5: 결과 표시**
마지막으로 앱이 사용자에게 결과를 제시합니다. "CHURN 확률 계산" 옵션의 경우, 이탈 확률과 함께 형식화된 방식으로 입력 데이터를 표시합니다. "기능 중요도"나 "사용자별 SHAP"과 같은 다른 옵션의 경우, 모델의 행동을 해석하는 데 도움이 되는 시각화를 생성합니다.

터미널에서 streamlit-app.py 파일이 있는 폴더에서 이 코드를 실행할 수 있습니다.

<div class="content-ad"></div>

```js
streamlit run streamlit-app.py
```

![image](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_6.png)

로컬호스트에서 브라우저로 열리는 창에서 애플리케이션 인터페이스를 확인할 수 있습니다. 이 인터페이스에는 SHAP 요약, 특정 사용자의 SHAP 데이터 및 사용자가 입력한 새로운 값에 기반한 고객 이탈 확률 계산과 같은 다양한 옵션이 있습니다.

특징 중요도: 우리는 모델의 의사 결정과정에서 어떤 기능이 중요한지 전반적으로 파악해 볼 수 있습니다.

<div class="content-ad"></div>


![image1](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_7.png)

사용자 기반 SHAP: 특정 고객의 결과 모델이 선택된 기준을 알고 싶을 수 있습니다. 테스트 세트에서 고객ID가 6100-QQQQHEB인 고객을 살펴보겠습니다.

![image2](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_8.png)

데이터 세트에 따르면 고객ID가 6100-QQQHEB인 고객은 이탈한 것으로 나타납니다. 또한 훈련된 교육 세트에 따르면 이 고객이 이탈할 것이라고 모델이 말합니다. 그러나 이 정보만으로는 충분하지 않습니다. 어떤 특성을 기반으로 고객이 이탈할 것이라고 말하는지 확인하고 싶을 수 있습니다.


<div class="content-ad"></div>

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_9.png)

사진 속 SHAP 차트를 보면 6100-QQQQHEB 고객의 이탈(Churn) 확률이 80% 이상인 것을 보여줍니다. 해당 모델은 이 결정을 내린 이유는 사용자의 InternetService 기능이 광섬유(Fiber Optic)이고, Contract 기능이 월간(Month-to-month)이며, PaymentMethod 기능이 전자 수표(Electronic check)인 것 때문입니다. 다른 기능들도 이 결정을 확인하지만 미미한 영향을 미칩니다.

또 다른 SHAP 그래프에서도 비슷한 추론을 볼 수 있습니다.

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_10.png)

<div class="content-ad"></div>

## 이탈 확률 계산하기

저희가 만든 모델과 Streamlit 인터페이스를 통해 사용자들에게 새로운 고객 값을 제공하고 이 값을 기준으로 이탈 확률을 계산할 수 있습니다.

![이미지1](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_11.png)

![이미지2](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_12.png)

입력된 값에 따르면, 새로 생성된 고객의 이탈 확률은 71.33% 입니다.

<div class="content-ad"></div>

인터랙티브 인터페이스 외에도, 모델을 신속하고 신뢰할 수 있게 API로 변환하는 것이 매우 중요합니다. API를 이용하면 모델을 더 넓은 사용자들에게 제공하고, 다른 애플리케이션과 통합할 수 있으며 사용이 쉬워집니다. FastAPI는 이러한 모든 과정을 잘 수행하는 좋은 예시입니다.

# 4. API (FastAPI)

![FastAPI](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_13.png)

FastAPI는 빠른 웹 애플리케이션과 API를 개발하기 위한 파이썬 웹 프레임워크입니다. 성능, 속도, 보안에 특히 중점을 두며, 비동기 프로그래밍 지원과 Python 타입 지원 덕분에 API 개발에 매우 유용합니다.

<div class="content-ad"></div>

프로젝트 범위 내에서 FastAPI 어플리케이션을 실행하는 데 uvicorn을 사용했어요. Uvicorn은 Python 웹 서버를 위한 서버 어플리케이션이에요. FastAPI처럼 빠르게 설치할 수 있어요. 그리고 사용하기도 아주 쉬워요. Uvicorn 대신 Gunicorn, Hypercorn, Daphne과 같은 대안들을 사용할 수도 있어요. 어떤 서버를 사용할지는 프로젝트의 요구사항과 성능 기대치에 따라 다르겠죠.

프로젝트에 간단한 API를 제공하기 위해, 프로젝트 폴더에 fast-api.py라는 Python 파일을 생성했어요. 아래 코드는 uvicorn과 FastAPI를 사용하여 빠른 API를 생성하는 데 도움을 줘요.

API 계층은 다음과 같이 구성되어요:

```js
import uvicorn
import pandas as pd
from fastapi import FastAPI
from catboost import CatBoostClassifier

# 모델 경로
MODEL_PATH = "../model/cat_model.cbm" 

# 훈련된 모델을 불러오는 함수
def load_model():
    model = CatBoostClassifier()
    model.load_model(MODEL_PATH)
    return model

# 데이터프레임 형식의 데이터에서 이탈 확률을 예측하는 함수
def get_churn_probability(data, model):
    # 수신한 데이터를 데이터프레임으로 변환
    dataframe = pd.DataFrame.from_dict(data, orient='index').T
    # 예측 수행
    churn_probability = model.predict_proba(dataframe)[0][1]
    return churn_probability

# 모델 불러오기
model = load_model()

# FastAPI 어플리케이션 생성
app = FastAPI(title="이탈 예측 API", version="1.0")

@app.get('/')
def index():
    return {'message': '이탈 예측 API'}

# API 엔드포인트 정의
@app.post('/predict/')
def predict_churn(data: dict):
    # 예측 수행
    churn_probability = get_churn_probability(data, model)
    # 예측 반환
    return {'이탈 확률': churn_probability}

# 어플리케이션 실행
if __name__ == '__main__':
    uvicorn.run("fast-api:app", host='127.0.0.1', port=5000)
```

<div class="content-ad"></div>

### Step 1: 설정하기
FastAPI 스크립트는 FastAPI 자체와 데이터 조작을 위한 Pandas, 그리고 훈련된 모델을 로드하기 위한 CatBoost를 포함한 필수 라이브러리를 가져오는 것으로 시작합니다.

### Step 2: 모델 로드
스크립트는 미리 훈련된 CatBoost 모델을 지정된 경로에서 로드하기 위해 load_model() 함수를 정의합니다.

### Step 3: 이탈 확률 예측
입력 데이터의 데이터프레임 형식에서 이탈 확률을 예측하기 위해 get_churn_probability() 함수가 정의됩니다. 이 함수는 들어오는 데이터 사전을 데이터프레임으로 변환한 다음 로드된 모델을 사용하여 이탈 확률을 예측합니다.

### Step 4: FastAPI 애플리케이션 만들기
FastAPI 애플리케이션은 "이탈 예측 API"라는 제목과 버전 "1.0"으로 생성됩니다.

<div class="content-ad"></div>

Step 5: API 엔드포인트 정의
- / 엔드포인트는 CHURN Prediction API의 가용성을 나타내는 간단한 메시지를 제공하기 위해 정의됩니다.
- /predict/ 엔드포인트는 JSON 형식의 데이터를 포함하는 POST 요청을 받아들이도록 정의됩니다. 이는 predict_churn() 함수를 호출하며, 이 함수는 제공된 데이터에 대한 이탠 소비를 예측하기 위해 로드된 모델을 사용합니다.

Step 6: 애플리케이션 실행
마지막으로, uvicorn.run() 함수를 사용하여 애플리케이션을 실행합니다. 이 함수는 스크립트 파일(fast-api)과 FastAPI 인스턴스(app)를 지정하고, 호스트 및 포트 구성을 함께 제공합니다.

다음과 같이 thefast-api.py 파일이 포함된 폴더 내에서 API를 시작할 수 있습니다:

```js
python fast-api.py
```

<div class="content-ad"></div>

![링크](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_14.png)

호스트로 이동하여 UVicorn에 제공된 API를 볼 수 있습니다.

![링크](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_15.png)

그러나 만든 API에 액세스하려면 확장자 끝에 /docs 또는 /redoc를 추가해야 합니다 (http://127.0.0.1:5000/docs).

<div class="content-ad"></div>

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_16.png)

API를 통해 생성된고객의 이탈 확률을 대화식으로 확인할 수 있습니다.

POST -` /predict/아래에 있는 Try it out을 클릭하여 생성된 고객 데이터를 입력할 수 있습니다.

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_17.png)

<div class="content-ad"></div>

Execute를 누르면 고객의 이탈 확률을 계산합니다. API가 성공적인 응답을 반환하면 아래와 같은 결과를 확인할 수 있습니다.

![이탈 확률 계산 결과](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_18.png)

고객의 이탈 확률은 62.8504%로 확인되었습니다. 동일한 고객 데이터를 사용하여 Streamlit을 통해 이탈 확률을 얻을 수도 있습니다.

![Streamlit을 통한 이탈 확률 확인](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_19.png)

<div class="content-ad"></div>

지금까지 스트림릿 인터페이스와 API를 성공적으로 구축했으니, 이제 모델을 제품 환경으로 이동하는 시간이에요. 이 과정에서는 모든 플랫폼과 장치에서 모델을 편리하게 사용할 수 있도록 필요한 작업을 수행할 거에요.

## 5. 자동화 (도커)

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_20.png)

도커는 컨테이너화 기술로 불립니다. 컨테이너는 독립적이고 격리된 환경에서 애플리케이션과 그 의존성을 실행하는 데 사용됩니다. 도커 컨테이너에는 모든 애플리케이션 코드, 라이브러리 및 구성 파일이 포함되어 있어요.

<div class="content-ad"></div>

이제부터는 시스템에서 작동하는 모델 파일을 자동화해야 합니다. 왜 그런 해결책이 필요한 걸까요? 시스템에서 제대로 작동하는 모델이 호환성 문제 등으로 다른 시스템에서 작동하지 않을 수 있습니다. 모델을 표준화하고 구축한 시스템 구성 요소에서 제대로 작동하도록 해야 합니다. 도커는 누가 어떤 패키지를 설치했는지, 어느 단계에서 에러가 발생했는지 같은 문제를 피하는 좋은 해결책을 제공합니다. 이외에도 이식성, 의존성 관리, 격리, 그리고 쉬운 배포를 제공합니다.

첫 번째 단계에서 데이터 전처리 단계와 모델이 개발된 train_model.py 파일을 작성했습니다. 이 파일과 작업하는 대신, 예측 섹션이 추가된 predict.py 파일을 작성할 것입니다.

train_model.py 스크립트에서 생성된 모델을 여기에서 사용할 수 있도록 저장해야 합니다. 아래 스크립트에서 파일 경로를 편집하여 모델을 저장한 파일 경로에 맞추세요.

predict.py 파일:

<div class="content-ad"></div>

```python
import pandas as pd
from catboost import CatBoostClassifier

# 훈련된 모델 불러오기
MODEL_PATH = "model/cat_model.cbm"
model = CatBoostClassifier()
model.load_model(MODEL_PATH)

def predict_churn(user_input):
    try:
        # 예측을 위한 데이터 준비
        user_data = pd.DataFrame([user_input])
        
        # 예측하기
        prediction = model.predict_proba(user_data)[:, 1][0]
        
        return {"이탈 확률": float(prediction)}
    except Exception as e:
        return {"error": str(e)}

if __name__ == "__main__":
    # 사용자로부터 입력 받기
    customerID = "6464-UIAEA"
    print("다음 정보를 입력해주세요:")
    gender = input("성별 (남성/여성): ").strip()
    senior_citizen = int(input("고령 시민 (0/1): ").strip())
    partner = input("파트너 (예/아니오): ").strip()
    dependents = input("부양가족 (예/아니오): ").strip()
    tenure = int(input("처리 기간 (월): ").strip())
    phone_service = input("전화 서비스 (예/아니오): ").strip()
    multiple_lines = input("다중 라인 (예/아니오): ").strip()
    internet_service = input("인터넷 서비스 (DSL/광섬유/없음): ").strip()
    online_security = input("온라인 보안 (예/아니오): ").strip()
    online_backup = input("온라인 백업 (예/아니오): ").strip()
    device_protection = input("장치 보호 (예/아니오): ").strip()
    tech_support = input("기술 지원 (예/아니오): ").strip()
    streaming_tv = input("스트리밍 TV (예/아니오): ").strip()
    streaming_movies = input("스트리밍 영화 (예/아니오): ").strip()
    contract = input("계약 (월간/일년/이년): ").strip()
    paperless_billing = input("페이퍼리스 빌링 (예/아니오): ").strip()
    payment_method = input("결제 수단 (전자 수표/우편 수표/은행 이체(자동)/신용 카드(자동)): ").strip()
    monthly_charges = float(input("월간 요금 ($): ").strip())
    total_charges = float(input("총 요금 ($): ").strip())

    new_customer_data = pd.DataFrame({
                "customerID": [customerID],
                "gender": [gender],
                "SeniorCitizen": [senior_citizen],
                "Partner": [partner],
                "Dependents": [dependents],
                "tenure": [tenure],
                "PhoneService": [phone_service],
                "MultipleLines": [multiple_lines],
                "InternetService": [internet_service],
                "OnlineSecurity": [online_security],
                "OnlineBackup": [online_backup],
                "DeviceProtection": [device_protection],
                "TechSupport": [tech_support],
                "StreamingTV": [streaming_tv],
                "StreamingMovies": [streaming_movies],
                "Contract": [contract],
                "PaperlessBilling": [paperless_billing],
                "PaymentMethod": [payment_method],
                "MonthlyCharges": [monthly_charges],
                "TotalCharges": [total_charges]
            })

    # 모델을 사용하여 이탈 확률 예측
    churn_probability = model.predict_proba(new_customer_data)[:, 1]

    # 이탈 확률 형식화
    formatted_churn_probability = "{:.2}".format(churn_probability.item())

    print(f"이탈 확률: {formatted_churn_probability}")
```     

이 파이썬 스크립트는 사전 훈련된 모델을 사용하여 사용자가 이탈을 예측할 수 있게 해줍니다. 사용자 입력을 DataFrame으로 변환하고 모델에 전달하여 예측을 수행합니다. 스크립트는 결과를 화면에 출력하며 끝납니다.

모델 배포를 위한 Dockerfile에 잘 명시된 요구 사항이 있어야 합니다. 이 단계에서 우리는 requirements.txt 파일을 생성하여 계속합니다. 이 파일을 만드는 여러 가지 방법이 있습니다. 모델 생성 시 사용한 특정 라이브러리를 수동으로 지정할 수도 있습니다. 또한 virtuaIenv, pip freeze, pipreqs 또는 piqar와 같은 가상 환경을 사용한 경우 이럴 좋은 옵션으로 사용할 수 있습니다. 그러나 pip freeze는 작업에 사용된 환경 변수가 아니라 가상 환경의 모든 패키지를 저장하므로 주의해야 합니다. pip freeze 사용법은 여기서 검토할 수 있습니다.

이 작업에서는 pip freeze를 사용하여 프로젝트 폴더에 requirements.txt 파일을 생성했습니다.

<div class="content-ad"></div>


altair==5.2.0
annotated-types==0.6.0
anyio==4.2.0
asttokens==2.4.1
attrs==23.2.0
blinker==1.7.0
cachetools==5.3.2
catboost==1.2.2
certifi==2024.2.2
charset-normalizer==3.3.2
click==8.1.7
cloudpickle==3.0.0
contourpy==1.2.0
cycler==0.12.1
decorator==5.1.1
executing==2.0.1
fastapi==0.109.2
fonttools==4.48.1
gitdb==4.0.11
GitPython==3.1.41
graphviz==0.20.1
h11==0.14.0
idna==3.6
importlib-metadata==7.0.1
ipython
jedi==0.19.1
Jinja2==3.1.3
joblib==1.3.2
jsonschema==4.21.1
jsonschema-specifications==2023.12.1
kiwisolver==1.4.5
llvmlite==0.42.0
markdown-it-py==3.0.0
MarkupSafe==2.1.5
matplotlib==3.8.3
matplotlib-inline==0.1.6
mdurl==0.1.2
numba==0.59.0
numpy==1.26.4
packaging==23.2
pandas==2.2.0
parso==0.8.3
pexpect==4.9.0
pillow==10.2.0
plotly==5.18.0
prompt-toolkit==3.0.43
protobuf==4.25.2
ptyprocess==0.7.0
pure-eval==0.2.2
pyarrow==15.0.0
pydantic==2.6.1
pydantic_core==2.16.2
pydeck==0.8.1b0
Pygments==2.17.2
pyparsing==3.1.1
python-dateutil==2.8.2
pytz==2024.1
referencing==0.33.0
requests==2.31.0
rich==13.7.0
rpds-py==0.18.0
scikit-learn==1.4.0
scipy==1.12.0
shap==0.44.1
six==1.16.0
slicer==0.0.7
smmap==5.0.1
sniffio==1.3.0
stack-data==0.6.3
starlette==0.36.3
streamlit==1.31.1
tenacity==8.2.3
threadpoolctl==3.3.0
toml==0.10.2
toolz==0.12.1
tornado==6.4
tqdm==4.66.2
traitlets==5.14.1
typing_extensions==4.9.0
tzdata==2024.1
tzlocal==5.2
urllib3==2.2.0
uvicorn==0.27.1
validators==0.22.0
watchdog==4.0.0
wcwidth==0.2.13
zipp==3.17.0


도커 파일(Dockerfile)은 Docker 컨테이너의 구조를 결정하는 구성 파일입니다. 어플리케이션의 실행 방법 및 설치해야 할 종속성 등의 명령을 포함합니다. 도커는 이 Dockerfile을 사용하여 컨테이너를 생성하고 이 컨테이너를 "이미지"라는 파일로 변환합니다. 그리고 이 "이미지"는 어떤 시스템에서든 실행될 수 있게 됩니다.

requirements.txt 파일을 작성한 후 도커 파일을 만들어보겠습니다:


# 파이썬 기반 이미지 사용
FROM python:3.9-slim

# requirements.txt 파일을 복사하고 종속성 설치
COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# 작업 디렉토리 지정
WORKDIR /app

# 어플리케이션 코드를 복사
COPY . .

# 어플리케이션 실행
CMD ["python", "src/predict.py"]


<div class="content-ad"></div>

**단계 1: 기본 이미지 선택**  
가장 기본적인 Python 환경을 제공하는 베이스 이미지를 지정하여 컨테이너가 생성됩니다 (python:3.9-slim).

**단계 2: 종속성 설치**  
호스트 머신에서 requirements.txt 파일을 복사하여 해당 파일에 나열된 종속성을 pip을 사용하여 설치합니다.

**단계 3: 작업 디렉터리 설정 및 응용프로그램 코드 복사**  
작업 디렉터리를 /app으로 설정하고, 호스트 머신의 모든 파일 및 디렉터리를 컨테이너 내의 /app 디렉터리로 복사합니다.

**단계 4: 응용프로그램 실행**  
컨테이너가 시작될 때 Python 인터프리터를 사용하여 predict.py 스크립트를 실행합니다.

<div class="content-ad"></div>

프로젝트 이 단계에서 계속하기 위해 Docker를 설치하고 필요한 파일을 생성한 후에는 Dockerfile이 있는 디렉토리로 이동하여 다음과 같이 빌드할 수 있어요.

```js
docker build -t churn-prediction-app .
```

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_21.png)

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_22.png)

<div class="content-ad"></div>

아래 명령어로 Dockerfile이 성공적으로 빌드되었는지 확인할 수 있어요:

![Screenshot](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_23.png)

빌드를 완료했다면, 터미널에서 만들어진 이미지를 실행할 수 있어요.

```js
docker run -it churn-prediction-app
```

<div class="content-ad"></div>

우리는 고객 정보를 입력하여 이타점 확률을 확인할 수 있습니다.

![이미지](/assets/img/2024-07-01-End-to-EndMachineLearningProjectChurnPrediction_24.png)

Docker를 통해 Streamlit 및 FastAPI에서 예측을 위해 생성한 고객의 이타확률을 확인할 수 있습니다. 저희가 만든 모델을 예측하고 싶은 사용자는 Dockerfile을 빌드하고 실행함으로써 이 편의성에 접근할 수 있습니다.

# 6. 결론

<div class="content-ad"></div>

프로젝트 개발 과정 중 통신 회사가 제공한 고객 데이터를 활용해 CatBoost를 사용한 머신 러닝 모델이 개발되었습니다. 모델을 대화형으로 확인하기 위해 Streamlit을 활용한 인터페이스를 설계했고, 빠르게 접근하고 모델을 통합하기 위해 FastAPI로 API를 생성했습니다. 마지막으로, 프로젝트를 보다 확장 가능하고 배포 가능하게 만들기 위해 Docker를 사용해 최적화를 달성했습니다.

읽어주셔서 감사합니다. 궁금하신 점이나 제안이 있으시면 언제든지 공유해주세요!

LinkedIn: [Ramazan Olmez의 LinkedIn 프로필](https://www.linkedin.com/in/ramazanolmez/)  
이메일: ramazanolmeez@gmail.com