# 🏠 **서울시 아파트 가격 예측** 

---



## 📌 프로젝트 주제

- 서울시 공공데이터를 활용해 아파트 가격 예측 모델을 생성하고 유저가 입력하는 아파트 정보를 통해 현재(2021년) 아파트 가격 예측 웹 서비스 구현


## 📌 프로젝트 기간

- 2021년 11월 02일 ~ 11월 12일까지(9일)


## 📌프로젝트 참여 인원

- 데이터 EDA, 전처리, 예측 모델 구현 - 3명
- Django 웹 서비스 구현 - 3명 
- 맡은 역할 : 데이터 EDA, 전처리, 예측 모델 구현


## 📌사용된 개발 환경 / 라이브러리

- 개발 환경
  - Window10
  - Python 3.7.3
  - Jupyter-Notebook
  - Google Colab
  - VS Code
- 라이브러리
  - 전처리 / 시각화
    - Lux
      - lux-api 0.4.0
      - lux-widget 0.1.7
  - 모델 분석 / HPO
    - Pandas 1.2.5
    - Scikit-learn 1.0.1
    - Catboost 1.0.3
    - LightGBM 3.3.1
    - XGBoost 1.5.0
    - Vecstack 0.4.0
    - Joblib 1.1.0
  - 웹 구현
    - Django 3.2.9


## 📌 프로젝트 설명

- 데이터 소개

  - 2016년 ~ 2020년 서울특별시 부동산 실거래가 정보 (서울시 열린 데이터 광장)

  - 2021년 서울특별시 부동산 실거래가 정보 (국토교통부 실거래가 공개 시스템)

  - 아파트 매매가격지수 (KOSIS 국가통계포털)

    

- 전처리 과정

  - 불필요한 Feature 삭제

    - 예측과 관련 없는 Feature (실거래가 아이디, 업무 구분, 물건번호 등) 삭제
    - 다른 Feature로 대체 가능한 Feature (지번 코드, 시군구 코드, … 관리 구분코드, 건물 주용도 코드) 기존 명칭 대체
    - 대지권 면적 Feature 삭제 (아파트는 대기권 면적이 없음)
    
  - 2021년도 실거래 데이터 매칭 작업

    - 기존 Feature를 2016~2021 실거래 데이터에 맞게 가공, 단위 변경 진행 (시군구, 거래금액 등)
  - Feature 구성이 기존과 다르거나 매칭이 힘든 부분은 삭제 (번지, 본번, 거래 유형, … 도로명, 중개사 소재지 등)
  
  - 2016~2020년 2021년 실거래 데이터 병합

    - 전처리 완료된 데이터 병합 처리

      

- 모델 / 분석

  - 전처리 완료된 데이터를 Google AutoML을 사용해 기본적인 성능 지표 확인
  - Scikit-learn 내 회귀 모델 중 일부를 선택해 기본 Hyper-Parameter로 모델 학습하며 성능지표 확인 및 테스트
  - 테스트 시 높은 R2 Score를 기록한 모델을 5가지 선별해 GridSearch로 HPO 진행
    - 선별기준 
      1. R2 Score 
      2. 예측 기반 알고리즘이 가급적 중복되지 않을 것 (Stacking 기법 사용을 위해)
  - 서울시 구 별 아파트 가격 차이를 줄이기 위해 자치구별 데이터를 구분해 모델 학습 진행
  - Hyper-Parameter Optimization 까지 마친 회귀 모델 5개를 Stacking 기법을 적용해 최종 모델 학습 후 완성된 모델을 WEB 서비스에 탑재


## 📌 핵심 코드

- 전처리

```python
apart_2021 = apart_2021[apart_2021['해제사유발생일'].isnull()] # 계약 해제사유발생 건 삭제
apart_2021.drop(['번지','본번','부번','계약일','도로명','해제사유발생일','거래유형','중개사소재지'],axis=1, inplace=True) # 불필요 열 삭제

apart_2021['자치구명'] = apart_2021['시군구'].str.split(" ").str[1] # 자치구명 추출
apart_2021['법정동명'] = apart_2021['시군구'].str.split(" ").str[2] # 법정동명 추출
apart_2021.drop(['시군구'], axis='columns', inplace=True) # 시군구 열 삭제

apart_2021['계약년월'] = apart_2021['계약년월'] = 2021 # 계약년월 전체 2021년으로 대체

apart_2021['거래금액(만원)'] = apart_2021['거래금액(만원)'].str.replace(",", '') # 거래금액 열 내 ',' 제거
apart_2021['거래금액(만원)'] = apart_2021['거래금액(만원)'].astype(int) # 거래금액 열 타입 변경(object > int)
apart_2021['거래금액(만원)'] = apart_2021['거래금액(만원)'].apply(lambda x : x * 10000) # 거래금액 열 단위(만원 > 원) 변경

apart_2021 = apart_2021.rename(columns={'단지명':'건물명', '전용면적(㎡)':'건물면적', '거래금액(만원)':'물건금액', '층':'층정보', '계약년월':'신고년도'}) # 열 이름 변경
apart_2021 = apart_2021[['자치구명','법정동명','신고년도','건물면적','층정보','물건금액','건축년도','건물명']]

floor_drop_index = apart_all[apart_all['층정보'] == 0].index # 층정보 결측치 삭제
apart_all.drop(floor_drop_index, inplace=True)
const_year_index = apart_all[apart_all['건축년도'] == 0].index # 건축년도 결측치 삭제
apart_all.drop(const_year_index, inplace=True)

apart_all['경과년도'] = apart_all['건축년도'].apply(lambda x : int(datetime.datetime.now().year) - x) # 현재년도 - 건축년도
apart_all['경과년도'] = apart_all[['경과년도']].replace(int(datetime.datetime.now().year),0) # 0 값으로 생긴 현재년도 값 0으로 대체

brands = ['래미안','힐스테이트','자이','더샵','#','푸르지오','힐스테이트','롯데캐슬','아크로','아이파크','IPARK','에스케이뷰','SKVIEW'] # 시공사 브랜드 Top 10
brand = '|'.join(brands) # contains 기능을 활용해 한번에 찾아낼 수 있도록 List > Str 형태로 변형하며 브랜드 사이에 |(or) 입력
apart_all['브랜드점수'] = apart_all['건물명'].str.contains(brand) # 건물명에 Top10 브랜드가 있는 경우 True 아니면 False
apart_all['브랜드점수'] = apart_all['브랜드점수'].apply(lambda x : 1 if x == True else 0)

apart_all['법정동명'] = apart_all['법정동명'].apply(lambda x : x[:-2] if x[-1] in '가' else x) # 법정동명 값 중 OO동 뒤에 'X가' 같은 구획이 붙은 경우 삭제

# 기존 아파트 실거래 데이터에서 신고년도/구 가 일치하는 매매가격지수 데이터를 새로운 열로 추가
price_index_list = []

for idx, row in apart_all.iterrows():
        price_index_list.append(apart_priceindex[str(row['신고년도'])][row['자치구명']])
        
apart_all['매매지수'] = price_index_list
```

- 회귀 모델 성능 테스트 & GridSearch HPO

```python
models = {'KNeighbors':KNeighborsRegressor(),'Randomforest':RandomForestRegressor(),'ExtraTree':ExtraTreeRegressor(),'MLP':MLPRegressor(), 'SGD':SGDRegressor(),
'SVM':SVR(),'CatBoost':CatBoostRegressor(),'LightGBM':LGBMRegressor(),'XGBoost':XGBRegressor()}

model_score = {} # 모델 성능 저장용 dict

# 모델간 점수 비교
for name, attr in models.items():
    model = attr
    print(f'{name} model tranning ...')
    model.fit(x_train_transformed, y_train)
    predict_y= model.predict(x_test_transformed)
    model_score_list = []
    model_score_list.append(f'{name} RMSE : {(mean_squared_error(y_test, predict_y)**0.5):.1f}')
    model_score_list.append(f'{name} MAE : {(mean_absolute_error(y_test, predict_y)**0.5):.1f}')
    model_score_list.append(f'{name} R2 score : {(r2_score(y_test, predict_y)*100):.2f} %')
    model_score[f'{name}'] = model_score_list
    model_score_list = []
    
# 모델간 점수 비교 결과 출력
for name, score in model_score.items():
    print(f'{name}')
    print(f'{score}')
    print()
    
# MLP GridSearch
model1 = MLPRegressor()

param_grid_mlp={'hidden_layer_sizes': [(32,64),(128,64),(128,256)],
            'batch_size':  [50,100,200],
            'learning_rate_init': [0.01,0.05],
            'max_iter': [100,300,400]
            }

gs1 = GridSearchCV(model1, param_grid_mlp, scoring='neg_mean_squared_error', n_jobs=-1, cv=10, verbose=False)

gs1.fit(x_train_transformed, y_train)

gs1_test_score = mean_squared_error(y_train, gs1.predict(x_train_transformed))
print(f'Best RMSE {(-gs1.best_score_)**0.5} params {gs1.best_params_}')
print()
```

* Stacking 기법 적용

```python
# 구별 모델링을 위한 한글/영문 구 이름 Dictionary 생성
gu = {'노원구':'nowon', '송파구':'songpa', '강서구':'gangseo', '강남구':'gangnam', '강동구':'gangdong', '구로구':'guro', '성북구':'seongbuk', '양천구':'yangcheon', '도봉구':'dobong', '서초구':'seocho',
        '영등포구':'yeongdeungpo', '성동구':'sungdong', '마포구':'mapo', '동작구':'dongjak', '동대문구':'dongdaemoon', '은평구':'eunpyeong', '중랑구':'jungnang', '서대문구':'seodaemoon', '관악구':'gwanak', '용산구':'yongsan',
        '강북구':'gangbuk', '광진구':'gwangjin', '금천구':'geumcheon', '중구':'junggu', '종로구':'jongno'}

model_score = {} # 모델 성능지표 저장용 dict 생성

# 1. 모델 생성 
# 2. [model, pipeline, stack] pkl 파일 저장
# 3. 성능 평가 점수 저장

for kor, eng in gu.items():
    globals()[f'df_{eng}'] = apart_sep[apart_sep['자치구명']==kor] # df_nowon, df_gangnam ...
    
    x = globals()[f'df_{eng}'].drop(['물건금액'],axis=1)
    y = globals()[f'df_{eng}']['물건금액']
    
    globals()[f'df_{eng}'].to_csv(f'{kor}.csv', encoding='cp949', index=False) # 구별 csv 저장
    
    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.3, random_state=123) # train, test 분리
    
    # 연속형 변수 열 입력
    numeric_features = ['매매지수','신고년도','건물면적','층정보','건축년도','경과년도']
    numeric_transformer = StandardScaler()

    # 범주형 변수 열 입력
    categorical_features = ['자치구명','법정동명','건물명','브랜드점수']
    categorical_transformer = OneHotEncoder(categories='auto', handle_unknown='ignore') # 범주형 데이터가 x_train, x_test에 고르게 들어가지 않는 경우 pass 하기 위해 handel_unknown param ='ignore' 로 설정

    preprocessor = ColumnTransformer(
        transformers=[ # List of (name, transformer, column(s))
            ('num', numeric_transformer, numeric_features),
            ('cat', categorical_transformer, categorical_features)])
    
    preprocessor_pipe = Pipeline(steps=[('preprocessor', preprocessor)]) # 파이프라인 단계 입력
    
    preprocessor_pipe.fit(x_train, x_test) # 변수 변환
    
    # 변환된 변수로 transform
    x_train_transformed = preprocessor_pipe.transform(x_train)
    x_test_transformed = preprocessor_pipe.transform(x_test)
    
    # stacking 1 level model 지정
    estimators = [ 
    ('LightGBM', LGBMRegressor(learning_rate=0.05, n_estimators=300, num_leaves=100)),
    ('XGBoost', XGBRegressor(n_estimators=250)),
    ('RandomForest', RandomForestRegressor(n_estimators=200))]
    # ('MLP', MLPRegressor(batch_size=50, hidden_layer_sizes=(128,256), learning_rate_init=0.05, max_iter=400))]
    
    # Initialize StackingTransformer
    stack = StackingTransformer(estimators, 
                                regression = True, 
                                metric = mean_squared_error, 
                                n_folds = 10, stratified = True, shuffle = True, 
                                random_state = 123, verbose = 0)
    
    # Fit
    stack = stack.fit(x_train_transformed, y_train)
    
    # Get your stacked features
    S_train = stack.transform(x_train_transformed)
    S_test = stack.transform(x_test_transformed)
    
    # Use 2nd level estimator with stacked features
    model = CatBoostRegressor(depth=10, l2_leaf_reg = 1, iterations=500, learning_rate = 0.05) 
    model = model.fit(S_train, y_train) 

    y_pred = model.predict(S_test) 
    
    # 모델 성능평가 점수를 구별로 list에 모아둔 뒤 dict로 저장 
    model_score_temp = []
    model_score_temp.append(f'{kor}')
    model_score_temp.append((f'RMSE : {(mean_squared_error(y_test, y_pred)**0.5):.1f}'))
    model_score_temp.append((f'MAE : {(mean_absolute_error(y_test, y_pred)**0.5):.1f}'))
    model_score_temp.append((f'R2 score : {(r2_score(y_test, y_pred)*100):.2f} %'))
    model_score[kor] = model_score_temp
    model_score_temp = [] # 성능평가 점수 초기화
    
    # 구별 pkl 파일 저장 [Model, Pipeline(feature scaling), stacking]
    joblib.dump(model, f'model_apart_{eng}.pkl', compress=True) # model save
    joblib.dump(preprocessor_pipe, f'pipeline_{eng}.pkl', compress=True) # feature scaling pipeline save
    joblib.dump(stack, f'stack_{eng}.pkl', compress=True) # stacking transform save
```


## 📌 트러블 슈팅

- 모델 학습 시 R2 Score 값이 `-` 로 나타나는 경우가 있었는데 R2 Score 의 계산방식에서는 `-` 가 나올 수 없는 것이 정상이기 때문에 잘못된 값으로 판단하고 Hyper-Parameter 조정을 통해 제대로 된 수치 구현

- 다양한 아파트 이름 속에서 특정 브랜드 아파트 이름을 포함한 아파트만 찾아 별도의 Feature를 생성해 가산점을 부여하고 싶었지만 pandas 내에 contains 함수는 문자열 하나만 처리할 수 있기 때문에 원하는 대로 사용이 힘들어 구글링을 통해 해결

  - contains 기능은 문자열 하나만 사용 가능하기 때문에 여러 가지 조건에 해당하는 값들을 한번에 찾을 수가 없어 여러개의 데이터를 `OR(=|)` 형태의 str로 묶어 contains 에 입력 ```ex) brand = '|'.join(brands)``` 

- 서울시 전체를 대상으로 모델을 학습시킨 경우 MSE 값과 R2 Score가 예상보다 만족스럽지 않아 자료를 다시 검토해 본 결과 구별로 아파트의 가격이 천차만별로 다른 양상을 보여 결국 1개의 모델에서 서울시 내 모든 지역구 별로 모델을 학습시켜 MSE 값과 R2 Score를 향상시키는데 성공했다.


## 📌 후속 과제

- 단독주택 / 연립주택 / 오피스텔까지 포함한 서울시 부동산 가격 예측
  - 이번 프로젝트에서는 아파트만을 대상으로 가격 예측 모델 생성

- 구 별로 나누어진 모델을 동 단위까지 세분화
  - 부동산 데이터가 더 추가된다는 가정 아래 실현 가능할 것으로 추측

- 예측 가격과 함께 실제 아파트 가격 제공


## 📌 회고 / 느낀 점

- 구별로 모델을 만들며 반복적인 모델 생성 시 반복문 활용 방법과 변수 생성에 대해 많은 발전이 있었다.
- 구 별로 RMSE 값이 차이가 나는 이유처럼 데이터에 대해 다양한 시각으로 접근을 해야 문제 해결이 수월해질 수 있다.

  - 형성된 부동산의 시세에 따라 같은 R2 Score 수치를 가지고 있어도 RMSE 값이 차이 날 수 있다는 사실을 확인했다. (부동산 시세 ↑ = RMSE ↑)

- 모델을 여러 개를 사용해 성능 향상을 올려주는 Stacking 같은 기법을 활용 시 다양한 예측 기반 모델을 섞어 주는 것이 성능 향상에 도움을 줄 수 있다.
- 머신러닝/딥러닝 모델의 성능은 데이터의 양이 가장 높은 영향을 미치고 그 외 수단(HPO.. 정규화 등)에 대해서는 데이터의 양이 늘어나는 것보다 극적인 효과는 없는 것으로 보인다.
