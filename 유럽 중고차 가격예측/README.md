# ๐ ์ค๊ณ ์ฐจ ๊ฐ๊ฒฉ ์์ธก ๋ชจ๋ธ๋ง ํ๋ก์ ํธ



## ๐ ํ๋ก์ ํธ ์ฃผ์ 

* ์ ๋ฝ ์ค๊ณ ์ฐจ ๋ฐ์ดํฐ๋ฅผ ์ด์ฉํ ์ค๊ณ ์ฐจ ๊ฐ๊ฒฉ ์์ธก ๋ชจ๋ธ ์ ์

---

## ๐ ํ๋ก์ ํธ ๊ธฐ๊ฐ 

* 2021๋ 10์ 18์ผ ~ 10์ 22์ผ ๊น์ง(1์ฃผ)

---

## ๐ํ๋ก์ ํธ ์ฐธ์ฌ ์ธ์

* ์ ์ฒ๋ฆฌ / ์๊ฐํ -  2๋ช

* ๋ชจ๋ธ ๋ถ์ / ์ต์ ํ - 2๋ช (๊ธ์ด์ด)

---

## ๐์ฌ์ฉ๋ ๊ฐ๋ฐ ํ๊ฒฝ / ๋ผ์ด๋ธ๋ฌ๋ฆฌ

* ๊ฐ๋ฐ ํ๊ฒฝ

  * Window10 21H1(OS ๋น๋ 19043.1237)

  * Python 3.7.3

  * Jupyter-Notebook

  * Google Colab

    

* ๋ผ์ด๋ธ๋ฌ๋ฆฌ

  * ์ ์ฒ๋ฆฌ / ์๊ฐํ

    * Matplotlib 3.1.1

    * Lux

      * lux-api 0.4.0
      * lux-widget 0.1.7

    * seaborn 0.9.0

      

  * ๋ชจ๋ธ ๋ถ์ / HPO

    * Pandas 1.2.5

    * Pycaret 2.1.2

    * Scikit-learn 0.23

    * Scipy 1.5.3

    * Catboost 1.0.0

    * Tensorflow 1.13.1

      

---

## ๐ ํ๋ก์ ํธ ์ค๋ช

* ์ ์ฒ๋ฆฌ / ์๊ฐํ

  * BMW (**10781ํ**) ์ ์ ์ฒด ๋ฐ์ดํฐ(BMW + Audi + Benz + Ford + Hyundai + Toyota)(**64131ํ**) ๋์

  * ์ ๋ต์ธ Price ์ด๊ณผ ๋ค๋ฅธ ํ๋ค์ ๊ด๊ณ๋ฅผ ๊ทธ๋ํ, ํํธ๋งต ์๊ฐํ๋ก ํ์ธ

  * ๊ฒฐ์ธก์น ๋์ฒด

  * ์ด์ ๋ฐ์ดํฐ ํํ, ์ค์  ๊ธฐ์ค์ ๋ฐ๋ฅธ ๋ฒ์ฃผํ ์งํ

    

* ๋ชจ๋ธ / ๋ถ์

  * ๋ฐ์ดํฐ๋ฅผ Pycaret ์ ํตํด ๋ชจ๋ธ๋ณ ์ฑ๋ฅ ๋น๊ต
  * Pycaret์ ์ด์ฉํ ์ต๊ณ  ์ฑ๋ฅ ๋ชจ๋ธ HPO(Hyper-Parameter Optimization) ๋ฐ ์์ธก ์ฑ๋ฅ ํ์ธ
  * Pycaret์ HPO ์ GridSearch HPO ์์ธก ์ฑ๋ฅ ํ์ธ
  * Pycaret์์ HPO๋ฅผ ํ ๋ชจ๋ธ๊ณผ ํ์ง ์์ ๋ชจ๋ธ์ ์์ธก ์ฑ๋ฅ ๋น๊ต
  * Pycaret ๋ชจ๋ธ ์ฑ๋ฅ ๋น๊ต๋ฅผ ํตํด ๊ตฌํ Top3 ๋ชจ๋ธ์ Blending ๋ชจ๋ธ ์์ธก ์ฑ๋ฅ ๋น๊ต
  * ๊ฐ์ ์กฐ๊ฑด ๋ด ํ์ ์๊ฐ ๋์ด๋  ๊ฒฝ์ฐ ์์ธก ์ฑ๋ฅ ๋น๊ต

---

## ๐ ํต์ฌ ์ฝ๋

* ์ ์ฒ๋ฆฌ / ์๊ฐํ

```python
# DataFrame์ ๊ฒฐ์ธก์น, ์ด์์น ์ ๊ฑฐ ๋ฐ ๋์ฒด

# engineSize 0(=๊ฒฐ์ธก์น) i3๋ชจ๋ธ์๋ง ๋ฐ์ํ๊ณ  ์์ด ์ค์  ์์ง์ฌ์ด์ฆ 0.6์ผ๋ก ๋ณ๊ฒฝ
bmw.loc[(bmw['model']=='i3'), ['engineSize']] = 0.6 
bmw['tax'] = bmw['tax'].replace(to_replace=0, value=bmw['tax'].median())

bmw['model'] = bmw[['model']].apply(lambda x: x.str.strip())
bmw.loc[(bmw['fuelType']=='Hybrid') & (bmw['model']=='i3'), ['mpg']] = 111
bmw.loc[(bmw['fuelType']=='Electric') & (bmw['model']=='i3'), ['mpg']] = 118
bmw.loc[(bmw['fuelType']=='Other') & (bmw['model']=='i3'), ['mpg']] = 118
bmw['engineSize'] = bmw[['engineSize']].replace(0,bmw['engineSize'].mode()[0])
bmw['tax'] = bmw[['tax']].replace(0,bmw[['tax']].median())

# tax ๋ฒ์ฃผํ
bmw['tax_cut'] = pd.cut(bmw.tax,bins=[0,20,30,130,155,170,210,250,275,315,340,600],
                       labels=['A','B','C','D','E','F','G','H','I','J','K'])

# ์์ง ํฌ๊ธฐ ๋ฒ์ฃผํ
bmw['engineSize'] =  bmw['engineSize']//1

# ์ ์ฒด ๋ฐ์ดํฐ ๋์ Heatmap
num_vars = ['price','mileage', 'tax', 'mpg', 'engineSize']
mask = np.triu(used_car_all[num_vars].corr(),1)
sns.heatmap(used_car_all[num_vars].corr(), mask=mask, annot=True)
plt.show()
```



* ๋ชจ๋ธ / ๋ถ์

```python
# Catboost ๋ชจ๋ธ ์์ฑ + Hyper parameter ํ๋
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


# ์ฐ์ํ ๋ณ์ ์ด ์๋ ฅ
numeric_features = ['year','mileage','tax','mpg']
numeric_transformer = StandardScaler() # cf) RobustScaler

# ๋ฒ์ฃผํ ๋ณ์ ์ด ์๋ ฅ
categorical_features = ['model','transmission','fuelType','engineSize']
categorical_transformer = OneHotEncoder(categories='auto', handle_unknown='ignore') # ๋ฒ์ฃผํ ๋ฐ์ดํฐ๊ฐ x_train, x_test์ ๊ณ ๋ฅด๊ฒ ๋ค์ด๊ฐ์ง ์๋ ๊ฒฝ์ฐ๋ฅผ pass ํ๊ธฐ ์ํด handel_unknown param ='ignore' ๋ก ์ค์ 

preprocessor = ColumnTransformer(
    transformers=[ # List of (name, transformer, column(s))
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])
preprocessor_pipe = Pipeline(steps=[('preprocessor', preprocessor)])
preprocessor_pipe.fit(x_train, x_test) # StandardScaler, One-Hot Encoder ์ ์ฉ


# Tuned Model Ensemble
blender = blend_models(estimator_list = [tuned_cat,tuned_rf,tuned_et], optimize = 'RMSE')
```



---

## ๐ ํธ๋ฌ๋ธ์ํ

* Pycaret ๋ชจ๋ธ ๋น๊ต ์๋ฌ

  * Pycaret ๋ด model_compare  ์ฌ์ฉ ์ Huber Regressor ์ฐจ๋ก์์ ์ค๋ฅ ๋ฐ์
    * model_compare ์ Huber Regressor ์ ์ธ ์ต์ ์ ํ ํ ์งํ
    * Scipy ๋ผ์ด๋ธ๋ฌ๋ฆฌ ๋ฒ์  ์์ (1.5.3)

---

## ๐ ํ์ ๊ณผ์ 

* ๋ฅ๋ฌ๋ Hyper-Parameter Optimization ์งํ

* Tensor Board ํ์ฉ

* ์ ์ฒด ๋ฐ์ดํฐ์ ํฌํจ๋์ง ์์ ํ์ฌ ๋ฐ์ดํฐ ์ถ๊ฐ

* ์ ์ฒด ๋ฐ์ดํฐ ๋์ EDA(Exploratory Data Analysis)

  

---

## ๐ ํ๊ณ  / ๋๋ ์ 

* ์ ์ฒ๋ฆฌ๋ HPO๋ฅผ ํตํ ์์ธก์ฑ๋ฅ ํฅ์์๋ ํ๊ณ๊ฐ ์์ผ๋ฉฐ, ๋ฐ์ดํฐ์ ์์ด ๊ฐ์ฅ ํฐ ์ํฅ์ ์ค๋ค.

* Pycaret ๋ด๋ถ ๊ธฐ๋ฅ ์ค ์ผ๋ถ๋ ํน์  ๋ชจ๋ธ์์ ๋์ํ์ง ์๋๋ค.
  * Catboost ์ฒ๋ผ Pycaret ๋ด๋ถ ๊ธฐ๋ฅ ์ค ์ผ๋ถ๋ฅผ ์ฌ์ฉํ์ง ๋ชปํ๋ ๊ฒฝ์ฐ ์ง์  ์๋ฃ๋ ์ฌ์ฉ๋ฒ ํ์ธ ํ ์๋๋ ๊ธฐ๋ฅ์ ๋ํด์๋ ๋ณ๋ ํ์ธ์ด ํ์ํ๋ค.
  
* Blending ์ฒ๋ผ ์์๋ธ ๊ธฐ๋ฒ์ ์ด์ฉํ ๊ฒฝ์ฐ ๋ชจ๋ธ ๊ธฐ๋ฐ์ด ๋น์ทํ ์๋ก ์์ธก ์ฑ๋ฅ ํฅ์์ ๊ธฐ๋ํ๊ธฐ ์ด๋ ต๋ค.

* ์ฒ์ ๋ฐ์ดํฐ ํ์ธ์ ํ์ ๋ ๋ฐ์ดํฐ ์์์ '์ ์ด๋ ๊ฒ ์๋ ฅ์ด ๋์ด์์ง?' ๋ผ๋ ๊ถ๊ธ์ฆ์ด ๋ค ๋ฐ์ดํฐ๋ค์ด ๋ง์๋๋ฐ ๊ฒ์์ ํด๋ณผ ์๋ก ๋๋ฉ์ธ ์ง์์ด ์์๊ณ 

  ์ ๊ทธ๋ฐ ๋ฐ์ดํฐ๋ค์ด ์๋์ง ์ด๋ ์ ๋ ๋ฉ๋ํ  ์ ์๋ ๋ถ๋ถ๋ค์ด ๋์ด๋ฌ๋ค. ์ด๋ค ํ๋ก์ ํธ๋ผ๋ ํด๋นํ๋ ๋๋ฉ์ธ ์ง์์ด ์ด๋ ์ ๋ ํ๋ณด๋์ด์ผ ํ๋ก์ ํธ์ ์งํ์ด๋ EDA์ ์ข์ ์ํฅ์ ๋ผ์น ๋ค๋ ๊ฒ์ ๋๊ผ๋ค.

