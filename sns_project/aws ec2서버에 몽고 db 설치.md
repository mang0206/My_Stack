  * 몽고 db 설치 참고사이트 -> https://chichi.space/post/%ED%95%9C%EB%B2%88%EC%97%90-%EB%81%9D%EB%82%B4%EB%8A%94-AWS-EC2%EC%97%90-MongoDB-%EC%84%A4%EC%B9%98%ED%95%98%EA%B3%A0-%EB%B3%B4%EC%95%88%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/  
 
 ----------------
몽고 db 설치 순서
---------
1. 몽고 db 설치 repe 파일 생성
vi /etc/yum.repos.d/mongodb-org-4.0.repo -> 입력 

```
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```
   위와 같은 내용으로 저장  


2. 설치 진행
```
yum install -y mongodb-org 
```

3. 설치가 완료 되면 service 실행
```
service mongod start
```
4. 몽고db 접속
```
mongo
```

5. mongo db 계정 생성  

-> 관리자로 변환
```
use admin 
```
-> 계정 생성
```
db.createUser({ user: "root",
  pwd: "study111",
  roles: [ "userAdminAnyDatabase",
    "dbAdminAnyDatabase",
    "readWriteAnyDatabase"
  ]
})
```
사용할 db 계정 생성
```
use customDB
db.createUser({ user: "MJ",
  pwd: "study111",
  roles: ["dbAdmin", "readWrite"]
})
```
6. 몽고 db 보안 설정
vi /etc/mongod.conf 파일 수정
```
# network interfaces
net:
  port: 27017
  #bindIp: 127.0.0.1
bindIp 부분을 주석 처리해서 지정된 ip가 아니더라도 몽고 db에 접근할 수 있게한다.


security:
    authorization: enabled
security 주석 해제하고 authorization: enabled옵션 추가 -> 몽고 db에 익명으로 로그인할 수 없다.
```


설정이 끝났다면 service mongod restart 를 통해 몽고 db 재시작
