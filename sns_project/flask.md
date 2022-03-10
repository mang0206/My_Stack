Flask 정리
=========

Route
---------
라우트는 외부 웹브라우져에서 웹서버로 접근 시 해당 주소로 입력을 하게 되면 특정 함수가 실행되게 도와주는 기능을 한다.  
또한 Get, Post와 같은 method를 지정해 줄 수 있다. 이렇게 method를 지정해 줌으로써 rest-full한 웹을 개발할 수 있다.
```
# 게시물 관리할 때 사용한 함수들로 url 구분과 methods를 구분 함으로써 C,R,U,D 중 C,U,D 수행
@app.route("/content_submit", methods=["POST"])
def content_submit():

@app.route("/content_submit/<post_id>", methods=["POST"])
def content_update_submit(post_id):

@app.route("/content_submit/<post_id>", methods=["DELETE"])
def delete_post():
```
게시물 Update의 경우 ajax로 수행했는데 **Put이나 Patch methods를 사용하고 싶었지만 ajax는 Get, Post method만 사용가능하기 때문에**
Post함수로 대체했다.  

Render template, Redirect, Jsonify
---------
3개의 함수 모두 해당 프로젝트 대부분 route 함수의 return으로 사용한 함수들이다. (route 함수란 함수에 route 데코레이션을 해준 함수를 뜻한다.)  

1. Render template
Render template 함수는 표현 그대로 template를 렌더링 한다는 의미로 HTML 파일을 웹 브라우저에 보내주기 위해 사용하는 함수다.  
즉 render_template() 함수를 사용해 해당하는 html 파일을 웹 브라우저에 띄우게 된다.  
```
# render template의 경우 데이터를 보내줘서 template engine(python 에서는 jinja)을 통해 해당 HTML을 알맞게 수정한다.
return render_template('index.html', friend_dic = friend_dic, post_dic=post_dic)
```

2. Redirect
Redirect 함수는 현재 url에서 다른 url로 이동할 때 사용하는 함수이다. flask 에서는 url_for 메서드와 주로 함께사용하는데  
url_for 함수를 사용하면 url을 작성하는것이 아닌 해당 url이 정의된 route 함수 명을 작성하는 방식이다.  
```
return redirect(url_for('user', user=session['nickname']))

# redirect로 url을 이동할 때 데이터도 같이 받을 때는 아래와 같이 url을 작성해야 해당 데이터를 받는다.
@app.route("/user/<user>")
def user(user):
```

3. Jsonify
웹에서의 데이터 이동은 대부분 json으로 이루어진다. 비동기 통신인 ajax 역시 대부분의 데이터를 json 형식으로 통신하는데 이렇게 json 형식의 데이터 통신을 위해 Jsonify 함수를 사용한다.
```
return jsonify(result = "success", session_user=session_user)
```
Jsonify 함수 뿐만아니라 Render template 함수와 Redirect 함수도 위와같이 데이터를 같이 보내 줄 수 있다.

Session
--------
![image](https://user-images.githubusercontent.com/86212081/157659730-68e7f9e3-cd3b-40db-b642-880347b4b2c9.png)  
Flask에서는 Sessoion 정보를 dictionary 형태로 저장하고 있다.  
세션 데이터는 쿠키의 위에 저장되고, 서버는 쿠키에 암호화 방식으로 서명하기에 이 때 암호화를 위해 Flask에서는 세션 관리와 암호화를 위해 secret_key가 필요로 하다.  
```
# __init__.py
app.config["SECRET_KEY"] = "~~~~~~~~~~"

# view.py
session['nickname'] = find_user['nickname']
session['name'] = find_user['user_name']
session['profile_img'] = find_user['profile_img'][1]

# html 파일
<img src="{{ session['profile_img'] }}" class='top_bar_user' alt="">
<!-- 프로필,로그아웃,설정 팝업창 -->
<div id="user_popup" class="user_popup none">
  <a href="/user/{{ session['nickname'] }}" class="user_popup_a">프로필 이동</a>
  
 # session 정보 지우기
 session.clear()
```
flask에서는 이러한 Session 정보를 jinja를 통해 HTML 파일에서도 유용하게 사용 가능하다.  

Web socket
=========
ajax를 통해 동기적 문제 해결을 위해 페이지를 이동하거나 전체를 갱신하는것이 아닌 페이지의 일부를 구성하는 DOM을 갱신할 수 있게되었다.  
그러나 여전히 **요청에 의해 서버의 응답을 받아 페이지를 갱신한다는 문제가 있었다.**  즉 실시간 알림은 불가능 했다.  
웹소켓을 이용하면 클라이언트의 별도 요청없이도 서버에서 보내는 데이터를 받아 페이지에 표현할 수 있다.  
일회성의 통신만 제공하는 ajax와는 달리 , 웹소켓은 한번 열어두면 단방향이건 양방항이건 닫기 전까지 계속해서 통신이 가능하다.  
  (웹 소켓을 열어두면 클라이언트와 서버가 서로 **Ping, Pong을 계속해서 주고 받으면서 통신할 준비를 하고 있다.**)  
 
**flask websocket을 위한 라이브러리와 버전**
```
# 웹 소켓의 경우 해당 라이브러리를 맞춰주지 않으면 작동하지 않는다.
python-engineio==3.11.2
python-socketio==4.4.0
Flask-SocketIO==4.2.1
eventlet==0.30.0
```

**socket io 를 사용하기 위해선 기존의 app.run() 방식이 아닌 socket.run() 방식으로 한다.**  
```
# 웹 소켓을 사용하기 위해 수정한 __init__.py와 main.py
socketio = SocketIO(logger=False,engineio_logger=False)

def create_app(debug=False):
    app = Flask(__name__)
    app.debug = debug
    app.config["SECRET_KEY"] = "sns"
    app.config['JSON_AS_ASCII'] = False
    # 메일 인증 기능
    app.config['MAIL_SERVER'] = 'smtp.gmail.com'
    app.config["MAIL_PORT"] = 587
    app.config["MAIL_USERNAME"] = "ydpsns.project@gmail.com" 
    app.config["MAIL_PASSWORD"] = "gqizwnzhwaxwrjjj"
    app.config["MAIL_USE_TLS"] = True 

    socketio.init_app(app)

    from app.events import socketio_init
    socketio_init(socketio)
    
    return app
    
app = create_app(debug=True)

# main.py 부분
if __name__ == "__main__" :
    socketio.run(app, host='0.0.0.0',port=5000)
```

**Socket은 socket.on()을 통해 수신받고 emit을 통해 신한다. Flask에서는 socket을 위한 events.py를 따로 둬서 관리한다.**
```
# events.py 파일
# socketio_init 함수에 socket 함수들을 정의해 놓는다. 
def socketio_init(socketio):
    @socketio.on('comment_post')
    def comment_notice(message):
        if message['kind'] == 'append_reply':
            notice = col_notice.find(
                {'$and': [
                        {'notice_user' : message['create_user']},
                        {'notice_info.nickname' : message['session_user']},
                        {'kind' : 'reply'}]
                }).sort('time', pymongo.DESCENDING).limit(1)
            notice = list(notice)
            notice[0]['_id'] = str(notice[0]['_id'])
            emit('comment_notice',notice, broadcast=True) 
```
socketio.on 데코레이터로 수신받는 socket 함수를 지정해주며 emit 함수를통해 송신하며 수신 받을 socket을 명시해주고 데이터를 수신한다.  
  
**html 및 Js에서 socket을 사용**
```
# base html 부분
<script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/socket.io/1.3.6/socket.io.min.js"></script>
<script type="text/javascript" charset="utf-8" >
        session_nickname = "{{ session['nickname'] }}"
        var socket = io.connect('http://' + document.domain + ':' + location.port+'/');

        socket.on('connect', function(msg) {
            console.log(msg)
        });
    </script>
    
# event.py로 송신하는 Js 부분
var socket = io.connect('http://' + document.domain + ':' + location.port+'/');

$.ajax({
        type: "POST",
        url: "/content_reaction_submit",
        data: JSON.stringify(request_data),
        dataType: 'JSON',
        contentType: "application/json",
        success: function(data){
            indicate_reply(data, add_comment_list, standard_div)
            //해당 댓글의 답글 list show
            $(standard_div).attr('style','display:flex;');
            $(remove_tag).remove()
            $(comment_form).show()
            socket.emit('comment_post', request_data);
            
# event.py로 부터 수신받는 JS 부분
socket.on('request_notice', function(retMessage) {
    let message = retMessage[0]
    if(message['notice_user'] == session_id){
        make_notice_div(message)
        notice_dot.className = 'notice_dot';
    }
});

ssocket.on('comment_notice', function(retMessage) {
    let message = retMessage[0]
    if(message['notice_user'] == session_nickname){
        make_notice_div(message)
        notice_dot.className = 'notice_dot';
    }
});
```
