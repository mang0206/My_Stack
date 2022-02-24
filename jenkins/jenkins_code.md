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
  1. 
