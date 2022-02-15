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

이미지 저장, 불러오기 방법
----------
  * gridFs 이용하기 
```
# 이미지 mongodb에 저장
fs = gridfs.GridFS(db)
  if 'setting_button_profile' in request.form:
    input_profile = request.files.get('setting_input_profile')

    # colection에 파일 저장 put 함수는 저장된 document id를 반환한다
    _id = fs.put(input_profile)
    # 해당 documet id 정보를 현재 session user document에 추가
    col_user.update_one(
      {'user_id': session['login']},
      {'$set' : {'profile_img': _id}}
    )

# 이미지 파일 불러오기 
fs = gridfs.GridFS(db)
session_user = col_user.find_one({'user_id': session['login']})
if session_user['profile_img']:
  img = fs.get(session_user['profile_img'])
  
  base64_data = codecs.encode(img.read(), 'base64')
  profile_img = base64_data.decode('utf-8')
  print(profile_img)
```
gridFs를 사용해서 이미지 저장하는 방법은 우선 이미지 저장하는 collection을 따로 만들고 거기에 이미지를 저장한다.  
gridFs에 내장되어 있는 함수 put을 사용하면 이미지를 mongodb에 저장할 수 있는 형태로 인코드하고 저장되는 document Id 값을 반환해 준다.  
반환받은 document Id 값을 원하는 document에 저장한다.  
이미지 불러오는 방식은 document에 저장된 이미지 Id값을 get 함수를 통해 byte 형태로 저장된 파일을 디코드하여 이미지 파일을 불러온다.  

**이렇게 gridFs를 사용하는 방식은 이미지 파일을 불러올때마다 디코더 과정을 거치기 때문에 매우 느리다는 단점이 있었다.**  
  (심할때는 이미지 10장도 불러오지 않는데 20초 이상 걸릴 때도 있었다..... aws의  s3를 사용하게 된 이유)

  * aws s3  
```
# s3 connection 
def s3_connection():
    try:
        s3 = boto3.client(
            service_name="s3",
            region_name="ap-northeast-2", # 자신이 설정한 bucket region
            aws_access_key_id='AKIAYFTWJFXVINK7YLUL',
            aws_secret_access_key='*******************',
        )
    except Exception as e:
        print(e)
    else:
        print("s3 bucket connected!")
        return s3

s3 = s3_connection()

# put image
def s3_put_object(s3, bucket, file, filename, file_kind = 'images'):
    """
    s3 bucket에 지정 파일 업로드
    :param s3: 연결된 s3 객체(boto3 client)
    :param bucket: 버킷명
    :param file: 저장할 파일
    :param filename: 저장 파일명
    :return: 성공 시 True, 실패 시 False 반환
    """
    try:
        s3.put_object(
            Body = file,
	        Bucket = bucket,
            Key = f'{file_kind}/{filename}',
            ContentType = file.content_type
        )
    except Exception as e:
        return False
    return True

# get image
def s3_get_image_url(s3, filename, file_kind = 'images'):
    """
    s3 : 연결된 s3 객체(boto3 client)
    filename : s3에 저장된 파일 명
    """
    location = s3.get_bucket_location(Bucket='ydpsns')["LocationConstraint"]
    return f"https://{'ydpsns'}.s3.{location}.amazonaws.com/{file_kind}/{filename}"
# delete image
def s3_delete_image(filename):
    print('delete =', f'images/{filename}')
    s3.delete_object(Bucket='ydpsns',Key=f'images/{filename}')
```
s3를 이용하는 방법은 s3에 이미지를 저장하고 해당 url 정보를 db에 저장하는 방식이다.  
**s3에 데이터를 put, get, 하기 위해서는 해당 bucket을 public으로 바꿔주어야 한다.**  
