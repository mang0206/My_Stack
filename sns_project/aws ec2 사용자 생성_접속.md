유저 생성하기 -> https://snepbnt.tistory.com/442
--------

1. 계정 생성
```
sudo adduser 'user이름'
```
2. 생성한 계정 비밀번호 설정
```
sudo passwd 'user이름' 
```
3. NewUser 홈 디렉토리에 .ssh 경로 생성
```
sudo mkdir /home/NewUser/.ssh
```
4. authorized_key 파일 복사
```
sudo cp /home/ec2-user/.ssh/authorized_keys /home/NewUser/.ssh/authorized_keys
```
5. 접속 권한 변경
```
sudo chown -R NewUser:NewUser /home/NewUse/.ssh
```
6. 서비스 재시작
```
sudo service sshd restart
```


생성한 유저로 ssh에 접속 -> https://tbang.tistory.com/118
--------
1. 우선 root 계정 사용 설정
```
sudo passwd root 
```
2. 파일 수정
```
sudo vi /etc/ssh/sshd_config
```
```
#PermitRootLogin no 
PermitRootLogin yes

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication yes  <--이부분을 yes로 수정
```

3. 서비스 재시작
```
sudo service sshd restart
```
ssh 사용자 이름@퍼블릭 주소로 접속해보기~


생성한 계정 root 권한 부여해주기 -> https://starseeker711.tistory.com/176
---------

우선 root 계정으로 접근
```
vi /etc/sudoers 파일 접근
root    ALL=(ALL)       ALL		이부분 밑에
사용자이름 ALL=(ALL) ALL 추가해 주기
```
.ssf config파일 수정으로 간편하게 접석
----------
.ssh 디렉토리에 config 파일 수정 (vi ~/.ssh/config 로 접속)
```
Host ydpsns
User MJ
HostName 13.125.71.134
# IdentityFile ~/.ssh/jenkins_pt.pem
```
Host는 ssh 명령어 다음으로 사용할 별칭
User 로그인할 이름
HostName 접속할 도메인
IdentityFile 키페어를 사용하는것이라면 키페어 위치와 파일명 ex) ~/.ssh/key이름
