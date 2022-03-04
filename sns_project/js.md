Js 정리
=============

 Vanilla js와 Jquery 차이
-------------
- Vanilla js 
  + 순수한 자바스크립트로서 아무것도 다운받지 않고 구동이 가능
  + 다른 프레임워크(라이브러리)에 비해서 속도면에서 우수
  + 웹 표준을 잘 지키는 웹브라우저들에 대해서는 크로스 브라우징이 잘 되는 특성이 있다
    (크로스 브라우징 이란? 웹 페이지 제작 시에 모든 브라우저에서 깨지지 않고 의도한 대로 올바르게(호환성) 나오게 하는 작업을 말한다.)  
- Vanilla js 사용 예
```
// textarea의 value를 가져옴
document.getElementById('popup_textarea').value;
// plus_background의 class 지정
document.querySelector(".plus_background").className = "plus_background show";
// p 태그 생성
document.createElement('p');
// c_url태그에 create_div 태그를 자식 태그로 추가
document.getElementById(c_url).appendChild(create_div);
```
- Jquery
  + HTML 속 클라이언트 사이드 스크립트 언어를 단순화하도록 설계된 브라우저 호환성이 있는 JavaScript 라이브러리  
  + jQuery는 JavaScript를 편하게 사용할 수 있게 지원하는 라이브러리이지 정식 언어가 아니다
  + 무지하게 쉽고 간편하다
  + JQuery 라이브러리를 거치면서 브라우저에 맞는 네이티브 자바스크립트로 변환된 뒤 실행되기 때문에 속도가 느리다.
- Jquery 사용 예
```
// attr을 사용해서 해동 태그의 속성 변경
$('#change_pw_btn').attr('disabled', true);
// 해당 태그의 부모 태그 자식 태그 찾는 법
var div_btn = $(this).parent().children();
// addclass, removeclass
$(div_btn).addClass('none');
email_send_container.removeClass('none');
// p태그 innerText 변경
$(create_p).text('요청이 수락됐습니다.');
// input tag의 value 값 가져오기
let send_email = $('#send_email').val();
// 원하는 속성 삭제
$('.password_rule').removeAttr('style', 'color:red');
```

ajax
-------------
Ajax란 Asynchronous JavaScript and XML의 약자로 빠르게 동작하는 동적인 웹 페이지를 만들기 위한 개발 기법의 하나이다.  
Ajax는 웹 페이지 전체를 다시 로딩하지 않고도, 웹 페이지의 일부분만을 갱신할 수 있다.  
즉 Ajax를 이용하면 백그라운드 영역에서 서버와 통신하여, 그 결과를 웹 페이지의 일부분에만 표시할 수 있다.  
서버와는 JSON, XML, HTML, 텍스트 파일 형태의 데이터를 주고받을 수 있다. 

사용 예시 (post.js )
```
# like btn ajax
$('[id$=_icon]').click(function(){
    let btn_value = $(this).attr('value');
    let post_id = $(this).attr('post_id');
    let btn = $(this)
    var request_data = {
        "flag": btn_value,
        "post_id": post_id
    }
    // 해당 post의 좋아요 버튼
    let content_like = $(btn).parent().children('.content_like')
    // 해당 post의 좋아요 누른 user 수
    let like_count = Number($(content_like).text().slice(0,1))
    // 클릭한 버튼의 해당 post의 like_container_back를 찾기 위해 parent 및 children을 사용
    let like_div = $(btn).parent().children('.like_container_back').children().children('.like_user_list_container')[0]

    $.ajax({
        type: 'POST',
        url: "/content_like_submit",
        data: JSON.stringify(request_data),
        dataType: 'JSON',
        contentType: "application/json",
        success: function(data){
            // session user가 이미 좋아요를 누른 상태
            if (btn_value == "cancel") {
                $(btn).attr('value', 'plus')
                $(btn).attr('src', '../static/img/plus.png')
                like_count -= 1
                $(content_like).text(String(like_count) + '개')

                let chiled = $(like_div).children()
                //해당 div의 모든 자식 요소를 돌며
                for (let i = 0; i < chiled.length; i++) {
                    console.log($(chiled[i]).attr('value'))
                    //입력한 값과 일치하는 속성 값을 가진 자식요소를 찾고
                    if ($(chiled[i]).attr('value') == data['session_user']['nickname']) {
                        // 해당 요소를 지움
                        $(chiled[i]).remove();
                    }
                }
            // session user가 좋아요를 누른지 않은 상태
            }else{ 
                $(btn).attr('value', 'cancel')
                $(btn).attr('src', '../static/img/like.png')
                like_count += 1
                $(content_like).text(String(like_count) + '개')
                
                // 좋아요 누른 user 리스트에 추가할 user 태그들 생성
                const create_div = document.createElement('div');
                const create_a_img = document.createElement('a');
                const create_a_nickname = document.createElement('a');
                const create_img = document.createElement('img');
                // 좋아요 리스트에 추가할 div 태그
                $(create_div).attr({
                    'class': 'like_user_list',
                    'value': data['session_user']['nickname']
                });
                // 이미지를 감쌀 a 테그
                $(create_a_img).attr({
                    'href': '/user/'+data['session_user']['nickname']
                });
                // 닉네임 태그
                $(create_a_nickname).attr({
                    'href': '/user/'+data['session_user']['nickname'],
                    'class': 'like_user_nickname',
                });
                $(create_a_nickname).text(data['session_user']['nickname'])
                // 이미지 테그
                $(create_img).attr({
                    'src': data['session_user']['profile_img'][1],
                    'class': 'content_user_img'
                });
                // 생성한 태그들 구조에 맞게 append
                create_a_img.appendChild(create_img);
                create_div.appendChild(create_a_img);
                create_div.appendChild(create_a_nickname);
                // 좋아요 리스트에 최종적으로 div 태그 append
                like_div.appendChild(create_div);
            }
        },
        error: function(request, status, error){
            alert('ajax 통신 실패')
            alert(error);
        }
    })
});
```
  + type 에서 method 지정
  + url 에서 통신할 api route의 url 지정  
  + data 에서 송신할 data 정의  
   
ajax로 flask와 통신하기 위해서는 form태그가 아닌 div 태그로 html코드를 작성해야한다.  
ajax 안에서는 Jquery의 this를 사용할 수 없다. 따라서 this를 사용하고 싶을 때는 ajax 윗 부분에서 this 정보를 따로 저장한다음 사용해야한다.  
서버에서 받은 데이터를 ajax에서 사용할 때는 data 안에 해당하는 변수가 json 형식으로 되어있기 때문에 data[''] 방식으로 사용하면 된다.  
 
DataTransfer를 사용해서 이미지 파일 전송을 위한 input tag 동적 제어
------------
기본적으로 input 태그에서 file을 변경하게 되면 filelist가 항상 새롭게 초기화된다.
filelist는 read only이기 때문에 수정이 불가능하다. 따라서 동적으로 파일 관리하는기 위해서는 DataTransfer() 를 사용해서 filelist 자체를 바꿔 주어야한다.

Datatransfer을 사용한 이미지 파일 동적처리 예제
```
let dt = new DataTransfer();
function image_select() {
    var image = document.getElementById('popup_input_file').files;
    for (i = 0; i < image.length; i++) {
        console.log(image[i])
        if (check_duplicate(image[i].name)) {
            // 미리보기 목록에 추가
            images.push({
                "name" : image[i].name,
                "url" : URL.createObjectURL(image[i]),
                // "file" : image[i],
            })
            // input FileList 목록에 추가
            dt.items.add(image[i]);
            console.log(dt)
        } else {
            alert(image[i].name + "이미 선택한 파일입니다.");
        }
    }
    document.getElementById('file_container').innerHTML = image_show();
    // input FileList 업데이트
    document.querySelector('#popup_input_file').files = dt.files;

    // img를 선택하면 생성되는 div의 value를 할당
    img_value = document.getElementById('file_preview').getAttribute('value');
}


// 이미지 삭제
function delete_image(e) {

    // 삭제한 이미지 -> 미리보기 및 FileList 에서 삭제
    images.splice(e, 1);
    dt.items.remove(e);

    // 이미지 삭제 후 미리보기 및 input FileList 동기화
    document.getElementById('file_container').innerHTML = image_show();
    document.querySelector('#popup_input_file').files = dt.files;

}
```
input 태그에서 파일을 새롭게 업로드하면 filelist가 초기화 되기 때문에 DataTransfer에 새로 업로드한 파일을 add 하고 filelist를 DataTransfer로 업로드하는 방식이다.  
삭제 할때도 마찬가지로 DataTransfer에 해당하는 이미지 데이터를 지우고 filelist를 DataTransfer로 업로드하는 방식으로 한다.  


동적으로 생성된 tag들 제어
------------
js 코드로 의해 생성된 tag들은 .click() 함수가 작동되지 않는다.  
```
$(document).on("click",".reply_submit",function(){
    ~~~~~~~~~~~~
}
```
동적으로 생성된 HtML tag들은 위와 같이 **on() 함수**를 사용해서 처리한다.  
