Jenkins Pipeline code 구성
=============

![image](https://user-images.githubusercontent.com/86212081/155478981-406b2fce-c072-4f08-87f7-c69eb20a70c6.png)

  1. 사용 가능한 에이전트에서 이 파이프라인 또는 해당 단계를 실행한다.
  2. 빌드 stage 정의
  3. 빌드와 관련된 step들 수행
  4. test stage 정의
  5. test와 관련된 step들 수행
  6. 베포 stage 정의
  7. 베포와 관련된 step들 수행 

**jenkins 파이프라인은 stage를 통해 어떤 역할을 수행할것인지 정의하고**
**step을 통해 작성된 역할을 수행한다.**

Pipeline 정의
-----------
선언적 파이프라인과 스크립트 파이프라인은 모두 소프트웨어 제공 파이프라인의 일부를 설명하는 DSL이다.  
Scripted Pipeline은 제한된 형태의 Groovy 구문으로 작성되어있다.  
  (Pipeline을 작성하는데 Groovy 구문을 이해하는 것은 필수 요소는 아니다.)  
  (Groovy 문법은 javaScript 문법과 비슷하다.)  

Pipeline 생성 방법
------------
  1. **Blue Ocean을 통해:**  
Blue Ocean에서 파이프라인 프로젝트를 설정한 후 Blue Ocean UI를 통해 파이프라인의  
Jenkinsfile을 작성하고소스 제어에 커밋할 수 있다.  
  2. **classic UI를 통해:**  
클래식 UI를 통해 Jenkins에서 직접 기본 파이프라인을 입력할 수 있다.    
  3. **SCM:**  
Jenkinsfile을 수동으로 작성하여 프로젝트의 소스 제어 저장소에 커밋할 수 있다.   
  
위 정의된 각각의 방식으로 파이프 라인을 정의하는 구문은 동일하지만 jenkins는 파이프라인을  
클래식 UI에 직접 입력하는 것보다는 Jenkins가 소스 제어에서 직접 로드할 Jenkinsfile에서  
파이프라인을 정의하는 것을 추천한다.  
  
Blue Ocean
-----------
Jenkins Pipeline을 처음 사용하는 경우 Blue Ocean UI는 파이프라인 프로젝트를 설정하는 데 도움이 되며  
**그래픽 파이프라인 편집기를 통해 자동으로 파이프라인(예: Jenkinsfile)을 생성 및 작성한다.**  
  
Blue Ocean에서 파이프라인 프로젝트를 설정하는 과정의 일부로 Jenkins는 프로젝트의 소스 제어 리포지토리에  
대한 안전하고 적절하게 인증된 연결을 구성한다. 따라서 Blue Ocean의 파이프라인 편집기를 통해  
Jenkinsfile을 변경하면 자동으로 저장되고 소스 제어에 커밋된다.  

classic UI
----------
클래식 UI를 사용하여 생성된 Jenkinsfile은 Jenkins 자체에(Jenkins 홈 디렉터리 내) 저장된다.  

Jenkins 클래식 UI를 통해 기본 파이프라인을 생성  
![image](https://user-images.githubusercontent.com/86212081/155512890-ca1a29b3-82d0-437f-9df3-c9bd0b40f575.png)  
이름을 작성한 후 Pipeline을 클릭해서 작성할 수 있다.(jenkins는 이 이름을 가지고 디렉토리를 만듬으로 공백은 넣지 않는다.)  

SCM
-----------
복잡한 파이프라인은 클래식 UI 텍스트 영역 내에서 작성하고 유지하기 어렵다.  
좀더 쉽게 파이프라인을 작성하고 관리하기 위해서는 jsnkinsfile을 ide에서 작성하고 소스 제어에 커밋할 수 있다.  

**jenkinsfile을 사용하여 파이프라인 프로젝트 구성**  
  1. 우선 classic UI와 똑같이 new item에서 Pipeline을 클릭해서 들어간다.  
  2.   
![image](https://user-images.githubusercontent.com/86212081/155514604-e2f355b9-bae6-42ac-b6fc-6ad19357d9f8.png)  
위 사진과 같이 scm을 선택한다.  
  3. 
![image](https://user-images.githubusercontent.com/86212081/155514903-dc98ca66-be2d-4050-96c4-1eb374d95f6e.png)  
위 사진과 같이 jenkinsfile이 포함된 git repository 주소와 Credentials을 선택  
  (credential 설정 방법 -> https://github.com/mang0206/My_flow/blob/9cbafbdffc63f79f8b37cd0ecc652c8b29783b44/jenkins/jenkins%20%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0.txt#L9)  

  4.  
![image](https://user-images.githubusercontent.com/86212081/155515533-435098cb-a66e-498f-a899-7ae8f1565b43.png)  
마지막으로 git repository에서의 jenkinsfile 경로를 작성해준다.  
(맨 밑에 Pipeline Syntext는 키워드를 통해 자동으로 문법을 생성해주는 것 같은데 잘 모르겠다)  

Global Variable Reference
--------------
Pipeline은 전역변수도 제공한다.  
![image](https://user-images.githubusercontent.com/86212081/155517656-3f5c7997-ab98-488d-b386-7586f3fb2621.png)  


