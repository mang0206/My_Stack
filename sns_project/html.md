html 정리
=============

block
-------------
```
# base html 파일
<div class="both_layout">
    {% block index %}
    {% endblock %}
</div>

# block 사용 방법
{% extends 'index_base.html' %}
{% block index %}

{% endblock %}
```
block의 경우 상위 html 파일을 상속 받으며 block의 해당하는 파일이 열리는 경우 상위 html 파일도 같이 열린다.  
block안에 해당하는 html 파일을 열경우 상속 받는 html 파일에 block이 여러개 있다고 하더라도 해당 부분만 extend 해서 열린다.  
  
form 태그 
-------------
* action  
action 속성은 form태그가 어디로 데이터를 보낼 것인지에 대한 내용이다. 즉 form 태그 안의 데이터를 어떤 url(함수)로 보내는지 명시해준다.  
default값으로는 현재 패이지로 데이터 전송.  

* method  
method 속성은 해당 form 태그가 어떤 method 형태로 데이터를 전송하는지 정의해준다.  
get, post, patch, put, delete가 있다.  
  - Create 에는 Get, Post 사용   
  - Read 에는 Get 사용  
  - Update 에는 put, patch 사용  
  - Delete 에는 delete 사용  
  - form 태그는 get과 post만 지원한다고 한다 그래서 보통은 form 태그 내부에 input 태그들의 value 속성에 해당 method를 정의해준다.  

* button  
form 태그안의 데이터 제출 역할을 담당하는 태그로 클릭이 가능하며 type 속성의 default 값이 submit이다.  
type이 submit으로 되어있어야 데이터 전송이 가능하다. default 값이 submit이기 때문에 한 form 태그안에 button이 여러 개일 경우  
submit을 할 버튼을 제외하고는 반드시 type 속성을 button으고 바꿔줘야 한다.  
  
form 태그안에서 required 속성을 가진 input, textarea등의 태그가 비어있을 시 post 전송을 해도 get으로 전송하거나 오류 페이지를 반환  
required 속성은 해당 테그의 데이터가 제출되기 전 반드시 채워져 있어야 하는 것을 명시해주는 속성이다.  
  
* 한 페이지에서의 중복 form 태그 활용  
한 페이지에서 form 태그가 여러개 존재할시 form 태그 안의 submit 버튼의 name 속성 값으로 구분이 가능하다.
```
#(html 파일 부분)
<form action="/setting" method="post" name="setting_form_ide">
    <p class="setting_header">닉네임 변경</p>
    <input type="text" placeholder="새로운 닉네임" name="setting_input_ide">
    <button type="submit" class="setting_submit_btn" name="setting_button_ide">닉네임 저장</button>
</form>
<form action="/setting" method="post" name="setting_form_bio">
    <p class="setting_header">소개글 수정</p>
    <input type="text"placeholder="새로운 소개글" name="setting_input_bio">
    <button type="submit" class="setting_submit_btn" name="setting_button_bio">소개글 저장</button>
</form>

#(python 파일 부분)
if 'setting_button_ide' in request.form:
    input_ide = request.form.get('setting_input_ide')
    col_user.update_one(
        {'user_id': session['login']},
        {'$set' : {'nickname': input_ide}}
    )
    session['nickname'] = input_ide

if 'setting_button_bio' in request.form:
    bio = request.form.get('setting_input_bio')
    col_user.update_one(
        {'user_id': session['login']},
        {'$set' : {'bio': bio}}
    )
```
  
알림 - flask flash를 위한 alert 코드
-------------
```
{% with messages = get_flashed_messages() %}
    {% if messages %}
        <script>
            alert("{{messages[-1]}}")
        </script>
    {% endif %}
{% endwith %}
```
  

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




