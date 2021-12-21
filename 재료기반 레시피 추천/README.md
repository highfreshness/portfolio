# 🍴 냉장고 속 남은 재료로 만드는 만개의레시피
---


## 📌 프로젝트 주제

* 만개의레시피 사이트 내 레시피 자료를 기반으로 유저의 재료를 입력 받아 유사도가 높은 레시피 추천

  

## 📌 프로젝트기간 

* 2021년 10월 1일 ~ 10월 8일까지(1주)

  

## 📌프로젝트 참여 인원

* 전처리 -  3명
* 유사도 분석 - 2명 
  * 유사도 분석 파트 담당

  

## 📌사용된 개발 환경 / 라이브러리

* 개발 환경

  * Window10 21H1(OS 빌드 19043.1237)
  * Python 3.7.3
  * Jupyter-Notebook
  * MySQL 5.6.50-log MySQL Community Server

* 라이브러리

  * 데이터 수집 및 전처리

    * requests ( HTTP 호출 및 응답 ) - 2.21.0
    * BeautifulSoup ( HTML 구분 문석 ) - 4.7.1
    * tqdm ( 데이터 수집 진행률 프로세스바 ) - 4.31.1

  * 유사도 분석

    * pandas(DataFrame) - 1.2.5

    * scikit-learn - 1.0

      * TfidfVectorizer(TF-IDF 벡터화)

      * cosine_similarity(코사인 유사도 분석)

        

## 📌 코드 설명

* 데이터 수집

  * '만개의레시피' 사이트에서 분류 선택 후 한 페이지 내 있는 레시피 별 URL 추출

  * 추출한 레시피 URL에 하나씩 들어가 제목/재료/소스 등을 SQL DB에 INSERT

    

*  데이터 수집 및 유사도분석

  * 수집된 데이터를 받아 결측치 확인 후 수정(재료 결측치 - 행 삭제, 소스 결측치 - ' - ' 대체)

  * 유저에게 레시피 분류와 재료를 입력 받아 저장

  * 레시피 분류를 통해 전처리 데이터 필터링

  * 전체 데이터를 TF-IDF로 벡터화 시킨 후 입력받은 재료와 전체 데이터의 유사도 확인

  * 확인된 유사도를 기준으로 추천 레시피 제목/재료/소스/URl 출력(유사도 상위 5개까지)

    

## 📌 핵심 코드

* 데이터 수집

  * ```python
    def fn_insert_sql(params):
    
        #----------------------------------------
        # Database(Mysql) Connect Info
        #----------------------------------------
        
        # mysql 연결을 위한 정보를 저장하고 있는 mysql Connect 객체 생성한다.
        # host : ip위치(localhost : 현재 pc)
        # port : mysql port 번호
        # db   : 데이터베이스 이름
        # user : 데이터베이스 접근 login Id
        # pw   : 데이터베이스 접근 login Id의 password
        # charset : character Set
        # autocommit = True : Insert, Updaete, Delete 등이 자동 Commit 된다.
        # pymysql.cursors.DictCursor : 결과값을 Dict(Key : Value)로 Return한다.
        
        mysql_conn = pymysql.connect(
            host='localhost', 
            port=21500,
            db='mydb', user='root', password='',
            charset='utf8', autocommit=True,
            cursorclass=pymysql.cursors.DictCursor
        )
        
        #return DataFrame
        rtn = pd.DataFrame;
        try:
            #DataBase Sql (Structured Query Language)
            cursor = mysql_conn.cursor()
            
            ins_sql = '''
                INSERT INTO recipe (
                    page_num,
                    seq,
                    cooking_type, 
                    title, 
                    ingredient, 
                    sorce,
                    uri
                )
                VALUES (
                    %s,
                    %s,
                    %s,
                    %s,
                    %s,
                    %s,
                    %s
                )
            '''
            
            # 이 때에 corsor에서 database와 연결 및 Query를 보내고, returen 값을 받는다.
            # 실제 이때 연결된다 볼 수 있다.
            cursor.execute(ins_sql, (
                params.get('page_num'),
                params.get('sequence'),
                params.get('cooking_type'),
                params.get('title'),
                params.get('ingredient'),
                params.get('source'),
                params.get('uri')
            ))
            rtn = cursor.fetchall()
    #    except:
    
        finally:
            #Database 연결 해제
            mysql_conn.close()
        
        return rtn;
    ```
    
    
    
  * ```python
    # Recipe 상세 페이지의 스크래핑 및 데이터 저장 Function
    def fn_get_recipe(recipe_url):
        
        ## Request Url & Params
        target_url['recipe'] = recipe_url                                 # Ex : /recipe/6912734
        recipe_url = target_url.get('domain') + target_url.get('recipe')  # Ex : https://www.10000recipe.com + /recipe/6912734
        
        
        # 데이터 저장을 위한 dict
        data_params = {
            'page_num'     : page_params.get('page'),         # 페이지 번호 
            'sequence'     : page_params.get('sequence'),     # recipe 순번
            'cooking_type' : page_params.get('cat4_name'),    # 요리 분류 Ex 밑반찬, 
            'title'        : '',                              # 
            'ingredient'   : '',
            'source'       : '',
            'uri'          : recipe_url
        }
        
        # beautifulsoup로 html doc return
        html_recipe_soup = fn_get_html(recipe_url)
    
        ## Titile
        data_params['title'] = html_recipe_soup.find('div', {'class' : 'view2_summary st3'}).find('h3').get_text()
         
        # 데이터 가공시 error가 나더라도 존재하는 데이터만 가지고 저장을 진행하고 다음 프로세스를 진행한다.
        try:
            
            ## Recipe
            recipe_area = html_recipe_soup.find('div', {'id' : 'divConfirmedMaterialArea'})
    
            ## Recipe - span 제거 
            span_elements = recipe_area.find_all('span')
            for span in span_elements:
                span.decompose()
    
            ## Recipe And Source List
            list_recipe = [x.get_text(strip=True) for x in recipe_area.find_all('ul')[0].find_all('li')]
            data_params['ingredient'] = ','.join(list_recipe)
            
            if(len(recipe_area.find_all('ul')) > 1):
                list_source = [x.get_text(strip=True) for x in recipe_area.find_all('ul')[1].find_all('li')]
                data_params['source'] = ','.join(list_source)
        finally:
            fn_save_csv(data_params)
            fn_insert_sql(data_params)
    ```
    
    

* 전처리 및 유사도분석

  * ```python
    # 재료 입력
    def get_ingredients():
        cnt = 1
        recipe_list = []
        ingredient = ''
        print('최대 5개까지의 재료를 입력해주시고, 입력이 마무리 된 경우 x 입력해주세요')
        while(cnt < 6):
            ingredient_element = input('재료를 입력해주세요. : ')
            #재료입력에 대소문자 'X' 혹은 공백이 들어올 경우 입력이 마무리 됨
            if ingredient_element == 'x' or ingredient_element=='X' or ingredient_element ==' ': 
                break
            else:
                recipe_list.append(ingredient_element)
            cnt += 1
        print(f'선택된 재료들은 {recipe_list} 입니다.')
        input_recipe = ','.join(recipe_list)
        return input_recipe
    ```

  * ```python
    # 벡터화된 레시피 값을 받아 유사도 분석 후 분석된 레시피의 기존 index 값과 유사도 상위 5개를 DataFrame 반환
    def sim_indexPreprocess (recipe_vec):
        # 입력된 recipe와 전체 recipe 의 유사도분석
        sim = pd.DataFrame(cosine_similarity(recipe_vec[-1] , recipe_vec))
        # 유사도분석 된 자료를 정렬 후 상위 5개까지 추출 정렬
        sim.sort_values(by=0,axis=1,ascending=False,inplace=True)
        # 유사도 분석 df와 레시피 df의 인덱스 행을 맞추기 위해 행열 전환처리(T=transpose) + 0번을 제외한 유사도 상위 5개 반환
        sim_per = sim.iloc[ : , 1:6].T
        # 컬렴명( = recipe index 번호 추출)
        recipe_index = sim_per.index.tolist()
        return recipe_index, sim_per
    ```

    

## 📌 트러블슈팅

* 데이터 수집

  * 세부 페이지 에서 재료를 가져올 때 유형이 페이지 유형이 2가지 이상 확인되어 try - except 문으로 예외 발생 시 수집 없이 넘어가도록 처리

    (결측치 발생 부분은 유사도분석 초기 파트에서 다시 처리)



## 📌 후속 과제

* 데이터 수집을 더 진행 해 레시피 데이터 유사도의 정확성 향상
* 현재 구현해 둔 코드를 웹페이지나 어플로 구현



## 📌 새로 알게 된 내용

* 분류 - 빵, 재료 - 밀가루 만 넣어서 유사도 분석 시 [물, 밀가루(100%)] , [밀가루, 우유(85.7%)] 처럼 같은 밀가루를 포함하는데 유사도가 다르게 나와 확인해보니 코사인 유사도는 각 글자가 얼마나 완전히 겹치는지를 기준 결과가 아니라 다차원 상에서 2개의 벡터 사이의 각도를 기준으로 하여 -1~1 사이의 값으로 결정 되는 것이라 전단계인 TF-IDF를 통한 벡터화에서 '물' 이라는 내용이 너무 많이 들어있어 가중치가 너무 작은 숫자로 곱해졌기 때문에 코사인 유사도 계산 시 "물"은 거의 영향이 없고 '우유'는 물보다 비교적 영향을 주기 때문에 유사도 %에 차이가 있었던 것 보인다.

