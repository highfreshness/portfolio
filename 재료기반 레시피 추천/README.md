# π΄ λμ₯κ³  μ λ¨μ μ¬λ£λ‘ λ§λλ λ§κ°μλ μνΌ
---


## π νλ‘μ νΈ μ£Όμ 

* μ§κΈ λμ₯κ³ μ μλ μ¬λ£λ₯Ό μλ ₯νκ³  λ§κ°μλ μνΌ μ¬μ΄νΈμμ κ΄λ ¨λκ° λμ λ μνΌλ₯Ό μΆμ² λ°μλ³΄μ

  

## π νλ‘μ νΈκΈ°κ° 

* 2021λ 10μ 1μΌ ~ 10μ 8μΌκΉμ§(1μ£Ό)

  

## πνλ‘μ νΈ μ°Έμ¬ μΈμ

* μ μ²λ¦¬ -  3λͺ

* μ μ¬λ λΆμ - 2λͺ ( κΈμ΄μ΄ : μ μ¬λ λΆμ ννΈ λ΄λΉ )
  
  

## πμ¬μ©λ κ°λ° νκ²½ / λΌμ΄λΈλ¬λ¦¬

* κ°λ° νκ²½

  * Window10 21H1(OS λΉλ 19043.1237)

  * Python 3.7.3

  * Jupyter-Notebook

  * MySQL 5.6.50-log MySQL Community Server

    

* λΌμ΄λΈλ¬λ¦¬

  * λ°μ΄ν° μμ§ λ° μ μ²λ¦¬

    * requests ( HTTP νΈμΆ λ° μλ΅ ) - 2.21.0
  
    * BeautifulSoup ( HTML κ΅¬λΆ λ¬Έμ ) - 4.7.1

    * tqdm ( λ°μ΄ν° μμ§ μ§νλ₯  νλ‘μΈμ€λ° ) - 4.31.1

      

  * μ μ¬λ λΆμ

    * pandas(DataFrame) - 1.2.5

    * scikit-learn - 1.0

      * TfidfVectorizer(TF-IDF λ²‘ν°ν)
  
      * cosine_similarity(μ½μ¬μΈ μ μ¬λ λΆμ)
  
        

## π νλ‘μ νΈ μ€λͺ

* λ°μ΄ν° μμ§

  * 'λ§κ°μλ μνΌ' μ¬μ΄νΈμμ λΆλ₯ λ³ λ μνΌ μ λ³΄ μΆμΆ

  * μΆμΆν λ μνΌ URLμμ μ λͺ©/μ¬λ£/μμ€ λ±μ SQL DBμ INSERT

    

*  λ°μ΄ν° μμ§ λ° μ μ¬λλΆμ

  * μμ§λ λ°μ΄ν°λ₯Ό λ°μ κ²°μΈ‘μΉ λ° μμ μ¬ν­ νμΈ ν μ²λ¦¬

  * μ μ μκ² λ μνΌ λΆλ₯μ μ¬λ£λ₯Ό μλ ₯ λ°μ λ μνΌ λΆλ₯λ₯Ό ν΅ν΄ λ°μ΄ν° νν°λ§

  * λ°μ΄ν°λ₯Ό TF-IDFλ‘ λ²‘ν°ν μν¨ ν μλ ₯λ°μ μ¬λ£μ μ μ²΄ λ°μ΄ν° μ¬μ΄μ μ½μ¬μΈ μ μ¬λ νμΈ ν λ μνΌ μΆμ²

    

## π ν΅μ¬ μ½λ

* λ°μ΄ν° μμ§

  * ```python
    def fn_insert_sql(params):
    
        #----------------------------------------
        # Database(Mysql) Connect Info
        #----------------------------------------
        
        # mysql μ°κ²°μ μν μ λ³΄λ₯Ό μ μ₯νκ³  μλ mysql Connect κ°μ²΄ μμ±νλ€.
        # host : ipμμΉ(localhost : νμ¬ pc)
        # port : mysql port λ²νΈ
        # db   : λ°μ΄ν°λ² μ΄μ€ μ΄λ¦
        # user : λ°μ΄ν°λ² μ΄μ€ μ κ·Ό login Id
        # pw   : λ°μ΄ν°λ² μ΄μ€ μ κ·Ό login Idμ password
        # charset : character Set
        # autocommit = True : Insert, Updaete, Delete λ±μ΄ μλ Commit λλ€.
        # pymysql.cursors.DictCursor : κ²°κ³Όκ°μ Dict(Key : Value)λ‘ Returnνλ€.
        
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
            
            # μ΄ λμ corsorμμ databaseμ μ°κ²° λ° Queryλ₯Ό λ³΄λ΄κ³ , returen κ°μ λ°λλ€.
            # μ€μ  μ΄λ μ°κ²°λλ€ λ³Ό μ μλ€.
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
            #Database μ°κ²° ν΄μ 
            mysql_conn.close()
        
        return rtn;
    ```
    
    
    
  * ```python
    # Recipe μμΈ νμ΄μ§μ μ€ν¬λν λ° λ°μ΄ν° μ μ₯ Function
    def fn_get_recipe(recipe_url):
        
        ## Request Url & Params
        target_url['recipe'] = recipe_url                                 # Ex : /recipe/6912734
        recipe_url = target_url.get('domain') + target_url.get('recipe')  # Ex : https://www.10000recipe.com + /recipe/6912734
        
        
        # λ°μ΄ν° μ μ₯μ μν dict
        data_params = {
            'page_num'     : page_params.get('page'),         # νμ΄μ§ λ²νΈ 
            'sequence'     : page_params.get('sequence'),     # recipe μλ²
            'cooking_type' : page_params.get('cat4_name'),    # μλ¦¬ λΆλ₯ Ex λ°λ°μ°¬, 
            'title'        : '',                              # 
            'ingredient'   : '',
            'source'       : '',
            'uri'          : recipe_url
        }
        
        # beautifulsoupλ‘ html doc return
        html_recipe_soup = fn_get_html(recipe_url)
    
        ## Titile
        data_params['title'] = html_recipe_soup.find('div', {'class' : 'view2_summary st3'}).find('h3').get_text()
         
        # λ°μ΄ν° κ°κ³΅μ errorκ° λλλΌλ μ‘΄μ¬νλ λ°μ΄ν°λ§ κ°μ§κ³  μ μ₯μ μ§ννκ³  λ€μ νλ‘μΈμ€λ₯Ό μ§ννλ€.
        try:
            
            ## Recipe
            recipe_area = html_recipe_soup.find('div', {'id' : 'divConfirmedMaterialArea'})
    
            ## Recipe - span μ κ±° 
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
    
    

* μλ ₯ λ° μ μ¬λλΆμ

  * ```python
    # μ¬λ£ μλ ₯
    def get_ingredients():
        cnt = 1
        recipe_list = []
        ingredient = ''
        print('μ΅λ 5κ°κΉμ§μ μ¬λ£λ₯Ό μλ ₯ν΄μ£Όμκ³ , μλ ₯μ΄ λ§λ¬΄λ¦¬ λ κ²½μ° x μλ ₯ν΄μ£ΌμΈμ')
        while(cnt < 6):
            ingredient_element = input('μ¬λ£λ₯Ό μλ ₯ν΄μ£ΌμΈμ. : ')
            #μ¬λ£μλ ₯μ λμλ¬Έμ 'X' νΉμ κ³΅λ°±μ΄ λ€μ΄μ¬ κ²½μ° μλ ₯μ΄ λ§λ¬΄λ¦¬ λ¨
            if ingredient_element == 'x' or ingredient_element=='X' or ingredient_element ==' ': 
                break
            else:
                recipe_list.append(ingredient_element)
            cnt += 1
        print(f'μ νλ μ¬λ£λ€μ {recipe_list} μλλ€.')
        input_recipe = ','.join(recipe_list)
        return input_recipe
    ```

  * ```python
    # λ²‘ν°νλ λ μνΌ κ°μ λ°μ μ μ¬λ λΆμ ν λΆμλ λ μνΌμ κΈ°μ‘΄ index κ°κ³Ό μ μ¬λ μμ 5κ°λ₯Ό DataFrame λ°ν
    def sim_indexPreprocess (recipe_vec):
        # μλ ₯λ recipeμ μ μ²΄ recipe μ μ μ¬λλΆμ
        sim = pd.DataFrame(cosine_similarity(recipe_vec[-1] , recipe_vec))
        # μ μ¬λλΆμ λ μλ£λ₯Ό μ λ ¬ ν μμ 5κ°κΉμ§ μΆμΆ μ λ ¬
        sim.sort_values(by=0,axis=1,ascending=False,inplace=True)
        # μ μ¬λ λΆμ dfμ λ μνΌ dfμ μΈλ±μ€ νμ λ§μΆκΈ° μν΄ νμ΄ μ νμ²λ¦¬(T=transpose) + 0λ²μ μ μΈν μ μ¬λ μμ 5κ° λ°ν
        sim_per = sim.iloc[ : , 1:6].T
        # μ»¬λ ΄λͺ( = recipe index λ²νΈ μΆμΆ)
        recipe_index = sim_per.index.tolist()
        return recipe_index, sim_per
    ```

    

## π νΈλ¬λΈμν

* λ°μ΄ν° μμ§ μ νμ΄μ§ μ νμ΄ 2κ°μ§ μ΄μμΈ κ²½μ° μ€λ₯ λ°μ

  * μΈλΆ λ μνΌ νμ΄μ§μμ λ°μ΄ν°λ₯Ό μ€ν¬λν ν  λ λλΆλΆ ν΄λΉνλ μ νμ΄ μλ ννλ‘ νμ΄μ§κ° κ΅¬μ±λμ΄ μμΌλ©΄ μ€ν¬λν λ°©μμ΄ λ¬λΌμ ΈμΌ ν΄

    try - except λ¬ΈμΌλ‘ μμΈ λ°μ μ ν΄λΉ νμ΄μ§μ λ§λ λ°©μμΌλ‘ λ°μ΄ν°λ₯Ό μ€ν¬λν νλλ‘ μ½λ μμ±



## π νμ κ³Όμ 

* μ μ²΄ λ μνΌ λ°μ΄ν°μ DBν
* νμ¬ κ΅¬νν΄ λ μ½λλ₯Ό μΉνμ΄μ§λ μ΄ν κ°μ μλΉμ€ ννλ‘ κ΅¬ν



## π νκ³  / λλ μ 

* TF-IDF Vectorizer κ°μ κ²½μ° λ΄λΆμ κ° μ€ λΉλ²νκ² λ°μνλ κ°μ λν΄μλ κ°μ€μΉλ₯Ό μ κ² λΆμ¬ν΄ κ±°μ μλ―Έ μλ ννλ‘ λ§λ€μ΄μ£ΌκΈ° λλ¬Έμ λΉλ νμκ° λ?μ ν­λͺ©μ λν΄ μ μ¬λλ₯Ό κ΅¬νκΈ° νΈλ¦¬νλ€. μν©μ λ°λΌ Vectorizerλ₯Ό κ΅¬λΆν΄ μ¬μ©ν΄μΌ μ¬λ°λ₯Έ ν΄λ΅μ μ»μ μ μμ κ²μΌλ‘ μκ°λλ€.
* μ½μ¬μΈ μ μ¬λλ λ²‘ν°ν λ λ°μ΄ν°κ° λ€μ°¨μ μμ κ°λλ₯Ό κΈ°μ€μΌλ‘ νμ¬ -1~1 μ¬μ΄μ κ°μΌλ‘ κ²°μ  λλ κ²μΌλ‘ μ°λ¦¬κ° μΌλ°μ μΌλ‘ μκ°νλ κΈμλ λ€λ₯Έ ννκ° κ°μ κ²μ²λΌ μ μ¬λλ₯Ό κ΅¬νλ κ²μ΄ μλμλ€. 
* μ¬λ¬κ°μ§ λ°©λ²μΌλ‘ μ μ¬λλ₯Ό κ΅¬ν  μ μμνλ° μ΄λ€ μλ¦¬λ‘ μΈν΄ μ μ¬λλ₯Ό κ΅¬νλ κ²μΈμ§ νμνκ³  μ΄λ€λ©΄ λμ€μ μΆμ² μλΉμ€ κ°μ λΆλΆμμ μ μ©νκ² μ΄μ©ν  μ μμ κ²μ΄λΌκ³  μκ°λλ€.

