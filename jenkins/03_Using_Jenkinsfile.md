https://www.jenkins.io/doc/book/pipeline/jenkinsfile/

Jenkins file 사용예시
=============

Jenkins file 사용 장점
------------
  1. 파이프라인 코드 반복 검토 용이
  2. 파이프라인 검사 및 추척

![image](https://user-images.githubusercontent.com/86212081/155975844-b46446d0-a144-4d64-8ac5-bae0145bd03c.png)  
위의 3단계가 반드시 필요한건 아니지만 대부분 위 사진과 같이 pipeline을 구성한다.  

에이전트 지시문 **agent any**는 파이프라인에 대한 실행 프로그램과 작업 공간을 할당하도록 지시한다.  
에이전트 지시문이 없으면 파이프라인 문법 자체가 유효하지 않을 뿐만 아니라 어떤 작업도 수행할 수 없다.  

stage와 step 지시어는 jenkins가 무엇을 실행하고 어느 단계에서 실행하는 지시한다.  

Build
-----------
많은 프로젝트에서 **파이프라인 작업 시작은 Build 단계이다.**  
Build 단계는 소스 코드가 합쳐지고, 컴파일 또는 패키지 되는 단계이다.  
Jenkins는 GNU/Make, Maven, Gradle 같은 Build 도구를 대체하는 것이 아니라 
프로젝트 개발 생명 주기(빌드, 테스트, 베포 등)의 여러 단계를 연결 시켜주는 것이다.  

Jenkins에는 빌드 도구를 호출하기 위한 여러 플러그인이 있지만 아래 예제에서는 단순히 셸 단계(sh)에서 make를 호출한다.  
리눅스 기반일 때는 **sh**를 사용하고 윈도우 기반일 때는 **bat**을 사용한다.  
![image](https://user-images.githubusercontent.com/86212081/155977494-b6a6048e-e612-4432-8886-e245d1859534.png)  
  
  1. sh 단계에서 make 명령을 호출하고 명령에 의해 0 종료 코드가 반환되는 경우에만 파이프라인이 계속 진행된다.  (0이 아닐시 파이프라인 중지)
  2. archiveArtifacts는 (*/target/*.jar)과 일치하여 빌드된 파일을 jenkins 컨트롤러에 저장한다.  
 
Test
------------
Test 작업을 자동화 시키는것은 중요하다. 따라서 Jenkins에는 Test를 위한 여러 플러그인에 제공하는  
테스트 기록, 보고 및 시각화 기능이 많이 있다.  
기본적인 수준에서는 테스트 실패가 있을 때 Jenkins가 웹 UI에서 보고 및 시각화를 위해 실패를 기록하도록 하는 것이 유용하다.  

![image](https://user-images.githubusercontent.com/86212081/155978128-4960bc9b-ddfb-46c2-8cf0-e5abc9bf04a3.png)  
  
  1. 인라인 셸 조건부(sh 'make check || true')를 사용하면 sh 단계에서 항상 0 종료 코드를 볼 수 있으므로 junit 단계에서 테스트 보고서를 캡처하고 처리할 수 있다.  
  2. junit은 포함 패턴(*/target/*.xml)과 일치하는 JUnit XML 파일을 캡처하고 연결한다.  


Deploy
------------
배포는 프로젝트 또는 조직 요구 사항에 따라 다양한 단계를 의미할 수 있으며 빌드된 아티팩트를 Artifactory 서버에  
게시하는 것부터 코드를 프로덕션 시스템에 푸시하는 것까지 무엇이든 될 수 있다.  

본질적으로 **"배포" 단계는 이전 단계가 성공적으로 완료되었다고 가정하고만 실행된다.** 그렇지 않으면 파이프라인이 일찍 종료되었을 것이다.  

![image](https://user-images.githubusercontent.com/86212081/155978503-6755f1b1-af4a-4c9d-b51f-4e0cfa4b7e5c.png)  
  

  1. currentBuild.result 변수에 액세스하면 파이프라인에서 테스트 실패가 있는지 확인할 수 있다. 이 경우 값은 UNSTABLE이 된다.  

Jenkins Pipeline 예제에서 모든 것이 성공적으로 실행되었다고 가정하면 각 성공적인 파이프라인 실행에는  
관련 빌드 아티팩트가 보관되고 테스트 결과가 보고되며 Jenkins의 전체 콘솔 출력이 모두 표시된다.  

환경 변수 종류 및 사용법은 사이트 참조  
환경 변수 사용 예시  
```
environment {
      AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
      AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
      AWS_DEFAULT_REGION = 'ap-northeast-2'
      HOME = '/home/MJ' // Avoid npm root owned
    }
```
