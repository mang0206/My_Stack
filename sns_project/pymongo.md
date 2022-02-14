mongo db
===========
몽고DB(MongoDB←HUMONGOUS)는 크로스 플랫폼 도큐먼트 지향 데이터베이스 시스템이다.   
NoSQL 데이터베이스로 분류되는 몽고DB는 JSON과 같은 동적 스키마형 도큐먼트들(몽고DB는 이러한 포맷을 BSON이라 부름)을  
선호함에 따라 전통적인 테이블 기반 관계형 데이터베이스 구조의 사용을 삼간다.  
이로써 특정한 종류의 애플리케이션을 더 쉽고 더 빠르게 데이터 통합을 가능케 한다.   
아페로 GPL과 아파치 라이선스를 결합하여 공개된 몽고DB는 자유-오픈 소스 소프트웨어이다.  
(출처https://ko.wikipedia.org/wiki/%EB%AA%BD%EA%B3%A0DB)  

NoSQL DBMS란?
------------
기존의 RDBMS은 안정성에 중심을 뒀다면 NoSQL DBMS는 확장성과 성능 최적화에 특화된다.  
→ 기존 RDBMS보다 **확장성, 유연성, 고성능, 고기능성**을 확보함.

BSON
-----------
BSON은 단순히 말하면 Binary JSON입니다. JSON과 동일한 구조지만 Binary 형태로 변경된 구조를 말한다.  
몽고DB가 처음 개발될 때에는 JSON을 이용해서 개발을 진행했다고 한다.  
  + JSON은 텍스트 기반으로 구문 분석이 매우 느리다.
  + JSON은 공간 효율성과는 거리가 멀다. (데이터베이스 문제)
 
이 외에도 여러가지 문제가 발생했다. 그래서 고안해 낸것이 바로 BJSON이다.  
JSON구조의 좋은점은 그대로 가져가면서 기계가 빠르게 읽을 수 있는 binary 형태로 변경하여 저장을 한 것이다.  
(출처 https://koonsland.tistory.com/86)  

Mongo DB 구조
---------
![image](https://user-images.githubusercontent.com/86212081/153831891-52605b09-c9fe-4556-aca8-00e139ab3d2d.png)  

>collection 부분이 RDBMS에서 table, document 부분이 RDMBS에서 각각의 field라고 보면 된다.

Pymongo 사용하여 mongodb 접속
-----------
ec2 서버의 mongodb에 접속
```
conn = pymongo.MongoClient("mongodb://root:study111@13.125.71.134:27017/root?authSource=admin")
```
접속한 mongodb의 database 접속
```
db = conn.get_database('root')
```
database에서의 각각의 collection에 접속
```
col_user = db.get_collection('user')
col_post = db.get_collection('post')
```
document를 찾기 위해 cursor를 선언했다면 함수 마지막 부분에 col.close()를 해줘야 하지만  
db가 여러개가 아니라면 기본적으로 python의 가비지 컬렉션이 자동으로 지워주기 때문에 신경써주지 않아도 된다. 

사용한 Pymongo 문법
-----------
```
col.insert_one(
{ 'user_id': user_id,
  'password': pw,
  'nickname': nickname,
  'user_name': user_name,
  'friend_list': [],
  'profile_img': _default['profile_img'],
  'background_img': _default['background_img'],
  'bio': None,
  'user_email': email
})

# or 연산자와 regex
query = { '$or' : 
        [ {'name': { '$regex' :  search, '$options': '$i'}},\
            {'nickname' :  { '$regex' : search, '$options': '$i'}}
        ]
    }
 search_user = list(col_user.find(query))
    
#document에서 일부분만 가지고 오기
session_user = col_user.find_one({'user_id':session['login']},{'_id':0, 'nickname':1 ,'profile_img':1, 'like':1})



# 여러개의 검색 결과 중 하나의 key 값만 가지고 오기
request_friend_id = [user['user_id'] for user in col_request_friend.find({'request_user':user})]

# update는 set을 통해 값 수정
col_user.update_one(
  {'user_id': session['login']},
  {'$set' : {'background_img': [img_name, s3_get_image_url(s3, img_name)]}}
)

# 리스트 정보를 수정할 때는 push, pull을 사용
col_user.update_one({'user_id':session['login']}, {'$push': {'like': data['post_id']}})
col_post.update_one({'_id':ObjectId(data['post_id'])}, {'$push': {'like': session_user}})

# pull 할 시 리스트의 데이터들이 딕셔너리 형태일 때 사용하는 방법
col_post.update_one({'_id':ObjectId(data['post_id'])}, {'$pull': {'like': { 'nickname' : session['nickname']}}})
```

이미지 저장 방법
----------
