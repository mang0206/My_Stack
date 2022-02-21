flask에서 sqlalchemy(ORM) 사용하기, flask 변수 js 변수와 연결
=============

init.py
-------------
```
from flask_sqlalchemy import SQLAlchemy

from sqlalchemy import desc, text

app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
# app.config['SQLALCHEMY_DATABASE_URI'] = os.environ.get('DATABASE_URI')
app.config['SQLALCHEMY_DATABASE_URI'] = "mysql+pymysql://root:ydp123456@13.124.231.43:3306/diet"
app.config['JSON_AS_ASCII'] = False
```
기본적을 mysql과 연결할 때는 flask_sqlalchemy만 import해도 되지만  
desc와 같이 sqlalchemy문법 을 사용하기 위해서는 sqlalchemy도 import 해야한다.  

app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True 부분은 수정 사항을 추적하고 데이터베이스에 신호를 보낸다.  
데이터베이스에 접근해서 insert, delete가 필요한경우 True로 해야하지만 현재 프로젝트처럼 조회만 할 경우 False로 해도 된다.  
    * True로 설정한 경우 메모리가 추가적으로 필요하므로 상황에 맞게 사용하는것 권장

app.config['JSON_AS_ASCII'] = False 부분은 json으로 파일을 보낼 때 한글이 깨지므로 작성  


sqlalchemy 사용 부분, models.py 
-------------
* models.py 부분
```
class Food(db.Model) :
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    food_name = db.Column(db.String(64), nullable=False)
    calorie = db.Column(db.Float, nullable=False)
    protein = db.Column(db.Float, nullable=False)
```

* views.py 부분
```
food = Food.query.filter(Food.food_name == food_name).first()

foods = Food.query.order_by(desc(text(nutrient))).limit(10)
```
* first() 를 통해 데이터베이스의 데이터를 가지고 오면 class 타입으로 데이터를 가지고 오게된다.
    * food_nutrients[0] = food.calorie
    * food_nutrients[1] = food.protein
    * 그렇기 때문에 위 코드처럼 원하는 컬럼을 명시해 주어야한다.

jinja를 통해 js와 연결
-------------
* Html 파일 부분
```
<div class='' id="result-data" data-nutrients= '{{ nutrients }}' data-result = '{{ foods_nutrients }}' >
    <div class="recommendation_container"></div>
```
* js 부분
```
let input_data = $('#result-data').data().nutrients;
```
jinja를 통해 js와 연결할때 js부분이 html파일에 있다면 단순히 {{ }} 를 통해 연결 되지만  
js 파일을 따로 만들어서 연결했다면 jinja만을 통해 연결 불가  
그렇기 때문에 위 코드처럼 커스텀 데이터 방식을 통해 연결해야 한다.  
data-원하는명 을 통해 얼마든지 연결 가능  

```
json_result_recommend = json.dumps(result_recommend, ensure_ascii = False)
json_foods_nutrients = json.dumps(recommend_foods_nutrients, ensure_ascii = False)

return render_template("check.html",nutrients=json_result_recommend,food_lst=food_lst,\
    foods_nutrients=json_foods_nutrients,result=result, sum_nutrients=sum_nutrients)
```
위의 커스텀 방식으로 flask 변수와 js 변수를 연결했을 때 리스트, string, int 자료형들은 올바르게 연결되지만  
리스트안에 딕셔너리와 같은 형식은 연결할때 string 형식으로 읽게된다.  

(딕셔너리 형태에서 value가 리스트인 경우는 js에서 string으로 인식하지않고 리스트로 인식)  

따라서 flask변수와 js변수를 연결 할때 데이터 형식이 2중 이상의 구조(ex: 리스트안에 딕셔너리)일 경우에는  
json 형식으로 보내주어야 자료형이 string으로 변환되지 않는다.  

##### flask(python)에서 변수를 선언할때 딕셔너리로 선언하고 json으로 변환해서 html에 보내야 js에서 올바르게 인식한다.!!!!





