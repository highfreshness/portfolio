# 🚘 중고차 가격 예측  프로젝트 [[링크]](https://github.com/highfreshness/portfolio/tree/master/%EC%9C%A0%EB%9F%BD%20%EC%A4%91%EA%B3%A0%EC%B0%A8%20%EA%B0%80%EA%B2%A9%EC%98%88%EC%B8%A1)



## 📌 프로젝트 주제

* 유럽 중고차 데이터를 이용한 중고차 가격 예측 모델 제작

---

## 📌 프로젝트 기간 

* 2021년 10월 18일 ~ 10월 22일 까지(1주)

---

## 📌프로젝트 참여 인원

* 전처리 / 시각화 -  2명

* 모델 분석 / 최적화 - 2명 (글쓴이)

---

## 📌사용된 개발 환경 / 라이브러리

* 개발 환경

  * Window10 21H1(OS 빌드 19043.1237)

  * Python 3.7.3

  * Jupyter-Notebook

  * Google Colab

    

* 라이브러리

  * 전처리 / 시각화

    * Matplotlib 3.1.1

    * Lux

      * lux-api 0.4.0
      * lux-widget 0.1.7

    * seaborn 0.9.0

      

  * 모델 분석 / HPO

    * Pandas 1.2.5

    * Pycaret 2.1.2

    * Scikit-learn 0.23

    * Scipy 1.5.3

    * Catboost 1.0.0

    * Tensorflow 1.13.1

      

---

## 📌 프로젝트 설명

* 전처리 / 시각화

  * BMW (**10781행**) 와 전체 데이터(BMW + Audi + Benz + Ford + Hyundai + Toyota)(**64131행**) 대상

  * 정답인 Price 열과 다른 행들의 관계를 그래프, 히트맵 시각화로 확인

  * 결측치 대체

  * 열의 데이터 형태, 실제 기준에 따른 범주화 진행

    

* 모델 / 분석

  * 데이터를 Pycaret 을 통해 모델별 성능 비교
  * Pycaret을 이용한 최고 성능 모델 HPO(Hyper-Parameter Optimization) 및 예측 성능 확인
  * Pycaret의 HPO 와 GridSearch HPO 예측 성능 확인
  * Pycaret에서 HPO를 한 모델과 하지 않은 모델의 예측 성능 비교
  * Pycaret 모델 성능 비교를 통해 구한 Top3 모델의 Blending 모델 예측 성능 비교
  * 같은 조건 내 행의 수가 늘어날 경우 예측 성능 비교

---

## 📌 핵심 코드

* 전처리 / 시각화

```python
# DataFrame의 결측치, 이상치 제거 및 대체

# engineSize 0(=결측치) i3모델에만 발생하고 있어 실제 엔진사이즈 0.6으로 변경
bmw.loc[(bmw['model']=='i3'), ['engineSize']] = 0.6 
bmw['tax'] = bmw['tax'].replace(to_replace=0, value=bmw['tax'].median())

bmw['model'] = bmw[['model']].apply(lambda x: x.str.strip())
bmw.loc[(bmw['fuelType']=='Hybrid') & (bmw['model']=='i3'), ['mpg']] = 111
bmw.loc[(bmw['fuelType']=='Electric') & (bmw['model']=='i3'), ['mpg']] = 118
bmw.loc[(bmw['fuelType']=='Other') & (bmw['model']=='i3'), ['mpg']] = 118
bmw['engineSize'] = bmw[['engineSize']].replace(0,bmw['engineSize'].mode()[0])
bmw['tax'] = bmw[['tax']].replace(0,bmw[['tax']].median())

# tax 범주화
bmw['tax_cut'] = pd.cut(bmw.tax,bins=[0,20,30,130,155,170,210,250,275,315,340,600],
                       labels=['A','B','C','D','E','F','G','H','I','J','K'])

# 엔진 크기 범주화
bmw['engineSize'] =  bmw['engineSize']//1

# 전체 데이터 대상 Heatmap
num_vars = ['price','mileage', 'tax', 'mpg', 'engineSize']
mask = np.triu(used_car_all[num_vars].corr(),1)
sns.heatmap(used_car_all[num_vars].corr(), mask=mask, annot=True)
plt.show()
```



* 모델 / 분석

```python
# Catboost 모델 생성 + Hyper parameter 튜닝
catboost = create_model('catboost')
tuned_catboost_pycaret = tune_model(catboost, n_iter = 100, optimize = 'RMSE')


# GridSearch 
model_catboostReg = CatBoostRegressor()
param_grid_catboost = {
                        'learning_rate': [0.01, 0.05, 0.1],
                        'iterations': [100, 300, 500],
					  #In most cases, the optimal depth ranges from 4 to 10. Values in the range from 6 to 10 are recommended.
    				   'depth': [6, 8, 10], 
                        'l2_leaf_reg': [1, 3, 5, 7, 10]
                       }

grid_search_result = model_catboostReg.grid_search(param_grid_catboost, 
                                       X=x_train_transformed, 
                                       y=y_train, 
                                       plot=True)


# 연속형 변수 열 입력
numeric_features = ['year','mileage','tax','mpg']
numeric_transformer = StandardScaler() # cf) RobustScaler

# 범주형 변수 열 입력
categorical_features = ['model','transmission','fuelType','engineSize']
categorical_transformer = OneHotEncoder(categories='auto', handle_unknown='ignore') # 범주형 데이터가 x_train, x_test에 고르게 들어가지 않는 경우를 pass 하기 위해 handel_unknown param ='ignore' 로 설정

preprocessor = ColumnTransformer(
    transformers=[ # List of (name, transformer, column(s))
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])
preprocessor_pipe = Pipeline(steps=[('preprocessor', preprocessor)])
preprocessor_pipe.fit(x_train, x_test) # StandardScaler, One-Hot Encoder 적용


# Tuned Model Ensemble
blender = blend_models(estimator_list = [tuned_cat,tuned_rf,tuned_et], optimize = 'RMSE')
```



---

## 📌 트러블슈팅

* Pycaret 모델 비교 에러

  * Pycaret 내 model_compare  사용 시 Huber Regressor 차례에서 오류 발생
    * model_compare 시 Huber Regressor 제외 옵션 선택 후 진행
    * Scipy 라이브러리 버전 수정(1.5.3)

---

## 📌 후속 과제

* 딥러닝 Hyper-Parameter Optimization 진행

* Tensor Board 활용

* 전체 데이터에 포함되지 않은 회사 데이터 추가

* 전체 데이터 대상 EDA(Exploratory Data Analysis)

  

---

## 📌 회고 / 느낀 점

* 전처리나 HPO를 통한 예측성능 향상에는 한계가 있으며, 데이터의 양이 가장 큰 영향을 준다.

* Pycaret 내부 기능 중 일부는 특정 모델에서 동작하지 않는다.
  * Catboost 처럼 Pycaret 내부 기능 중 일부를 사용하지 못하는 경우 직접 자료나 사용법 확인 후 안되는 기능에 대해서는 별도 확인이 필요하다.
  
* Blending 처럼 앙상블 기법을 이용한 경우 모델 기반이 비슷할수록 예측 성능 향상을 기대하기 어렵다.

* 처음 데이터 확인을 했을 때 데이터 상에서 '왜 이렇게 입력이 되어있지?' 라는 궁금증이 들 데이터들이 많았는데 검색을 해볼 수록 도메인 지식이 쌓였고

  왜 그런 데이터들이 있는지 어느 정도 납득할 수 있는 부분들이 늘어났다. 어떤 프로젝트라도 해당하는 도메인 지식이 어느 정도 확보되어야 프로젝트의 진행이나 EDA에 좋은 영향을 끼친 다는 것을 느꼈다.

