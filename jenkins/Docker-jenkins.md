Jenkins Docker로 설치
==========

Docker jenkins image 다운
----------
```
docker pull jenkins/jenkins
```
버전은 생략해서 latest 버전을 다운  

Docker jenkins container 실행
----------
```
docker run --name jenkins-docker -d -p 8080:8080 -p 50000:50000 -v /home/jenkins:/var/jenkins_home -u root jenkins/jenkins
```
port forwarding을 통해 8080 port로 접속가능하게 해준다. 볼륨 마운트를 사용해 /home/jenkins에 jenkins 정보 저장

Unlock jenkins 해제
---------
![image](https://user-images.githubusercontent.com/86212081/158506107-a73fbd5d-b0c8-4fb7-9477-7470f3c0edce.png)  
볼륨 마운트를 통해 jenkins 디렉토리에 접속해서 해당 패스워드를 확인할 수도 있지만 docker log를 사용해 확인 가능
```
docker logs jenkins-docker
```
**Git credentials 등록후 pipeline 등록**
