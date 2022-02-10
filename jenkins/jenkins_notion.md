
젠킨스 -> ci/cd를 자동으로 해주는 것 | 귀찮은 것을 자동으로 해주는 시종~
java runtime 위에서 동작하는 자동화 서버로 빌드, 테스트, 배포 등 모든 것을 자동화 해주는 자동화 서버

다양한 플러그인들을 활용해서 각종 자동화 작업을 처리할 수 있다

일련의 자동화 작업의 순서들의 집합인 pipeline을 통해 ci/cd 파이프라인을 구축함

젠킨스의 대표적인 plugin - credentials plgin, git plugin(git repository source 코드를 긁어오는 기능), pipeline(핵심적인 기능)
	(처음에 그냥 recommend 해주는 것을 깔면 어지간한건 다 깔려있다)	

credentials plgin
jenkins는 단지 서버일 뿐이기 때문에 배포에 필요한 각종 리소스(ssh 접근 등)에 접근하기 위해서는 여러가지 중요 정보들을 저장하고 있어야한다
이런 중요한 정보(aws tocken, git access token)들을 저장해 주는 플러그인이다
	-rsa 기반으로 이런한 정보들을 자동으로 암호화 해줘서 안전하다-

Docker plugin and Docker pipeline
도커 agent를 사용하고 jenkins에서 도커를 사용하기 위함

pipeline plugin
젠킨스의 핵심 기능인 Pipeline을 관리할 수 있게 해주는 플러그인

파이프라인 - ci/cd 파이프라인을 젠킨스에 구현하기 위한 일련의 플러그인들의 집합이자 구성
즉 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함으로써 파이프라인을 통해 서비스가 배포됨 Pipeline DSL(domain specific language)로 작성된다

Pipeline을 구성하는 요소
두가지 형태의 Pipeline syntax가 존재(Declarative, Scripted Pipeline)
	Declarative Pipeline syntax가 최신이며 더 가독성 좋은 문법이다

ㅁPipeline syntax

Sections
(- Agent section, Post section, Stages section, Steps section)
Agent section(젠킨스는 노예다 노예가 혼자면 많은일을 처리하기 힘들기 때문에 여러 slave를 두는데 각 slvae가 어떤 일을 할지 정의해 주는 section이다)
젠킨스에서 많은 일들을 해야 하기 때문에 혼자 하기 버겁다
여러 slave node를 두고 일을 시킬 수 있는데, 이처럼 어떤 젠킨스가 일을 하게 할것인지를 지정한다
젠킨스 노드 관리에서 새로 노드를 띄우거나 혹은 docker 이미지등을 통해서 처리할 수 있다

Post section(pipe line 각 단계가 끝날때마다 조취를 취해주는 section)
스테이지가 끝난 이후의 결과에 따라서 후속 조치를 취할 수 있다
ex 성공시에 성공 이메일, 실패하면 중단 혹은 건너뛰기 등등
	
Stages section
어떤 일들을 처리할 건지 일련의 stage를 정의함 즉 순서를 정해준다

Steps section
한 stage 안에서의 단계로 일련의 스텝을 보여준다.

Declarative (각 stage안에서 어떤어떤 일을 할것인지에 대한 종류 - 즉 분기문을 넣을 수 있다)
Environment, stage, options, parameters, griggers, when 등이 있다
Environment ->  어떤 pipeline이나 stage scope의 환경 변수 설정
parameter -> 파이프라인 실행 시 파라미터 받음
Triggers -> 어떤 형태로 트리거 되는가(어떤 주기로 실행 되는가)
When -> 언제 실행되는가

Steps
stage 내부에는 여러가니 스텝들로 구성
여러 작업들을 실행 가능하며 플러그인을 깔면 사용할 수 있는 스텝들이 생겨난다

ci/cd
=============
continuius intergration - 통합, 
continuous Delivery - 무엇을 배달한다
사용자에게 제품을 서비스를 지속적으로 배달하는것
코드베이스가 항상 베포가능한 상태를 유지하는 것을 뜻한다
continuous Depolyment
코드베이스를 사용자가 사용가능한 환경에 배포하는것을 자동화함

ci가 필요한 이유
-------------
여러명의 개발자가 개발한것을 나중에 한번에 합칠때 merge하기가 힘들다...  
- 지속적으로 통합하는것이 필요하다 즉 모든 개발자들이 안심하고 개발을 하기 위해, 내 코드와 맨탈을 위해~  
- 10명의 개발자가 열심히 개발 > 커밋 > 로컬 테스트 통과 > 코드베이스 머지  
- 테스트 코드 없는 무서운 코드, 버그 더미 코드를 애초에 코드베이스에 등록하지 않음  

cd가 필요한 이유
-------------
즉시 즉시 베포하기 위해서  

jenkins
=============  
java runtime 위에서 동작하는 자동화 서버로 빌드, 테스트, 배포 등 모든 것을 자동화 해주는 자동화 서버 
	(귀찮은 것을 자동으로 해주는 시종~ )

다양한 플러그인들을 활용해서 각종 자동화 작업을 처리할 수 있다  

일련의 자동화 작업의 순서들의 집합인 pipeline을 통해 ci/cd 파이프라인을 구축함  

젠킨스의 대표적인 plugin
--------------

- credentials plgin  
jenkins는 단지 서버일 뿐이기 때문에 배포에 필요한 각종 리소스(ssh 접근 등)에 접근하기 위해서는 여러가지 중요 정보들을 저장하고 있어야한다  
이런 중요한 정보(aws tocken, git access token)들을 저장해 주는 플러그인이다  
	-rsa 기반으로 이런한 정보들을 자동으로 암호화 해줘서 안전하다-  
	
- git plugin
git repository source 코드를 긁어오는 기능  

- Docker plugin and Docker pipeline  
도커 agent를 사용하고 jenkins에서 도커를 사용하기 위함

- pipeline plugin
젠킨스의 핵심 기능인 Pipeline을 관리할 수 있게 해주는 플러그인  


Pipline
-------------
파이프라인 - ci/cd 파이프라인을 젠킨스에 구현하기 위한 일련의 플러그인들의 집합이자 구성  
즉 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함으로써 파이프라인을 통해 서비스가 배포됨 Pipeline DSL(domain specific language)로 작성된다  
  
Pipeline 구성하는 요소
- 두가지 형태의 Pipeline syntax가 존재(Declarative, Scripted Pipeline)  
	Declarative Pipeline syntax가 최신이며 더 가독성 좋은 문법이다

Pipeline syntax
------------
- Sections  
(- Agent section, Post section, Stages section, Steps section)  
Agent section(젠킨스는 노예다 노예가 혼자면 많은일을 처리하기 힘들기 때문에 여러 slave를 두는데 각 slvae가 어떤 일을 할지 정의해 주는 section이다)  
젠킨스에서 많은 일들을 해야 하기 때문에 혼자 하기 버겁다  
여러 slave node를 두고 일을 시킬 수 있는데, 이처럼 어떤 젠킨스가 일을 하게 할것인지를 지정한다  
젠킨스 노드 관리에서 새로 노드를 띄우거나 혹은 docker 이미지등을 통해서 처리할 수 있다  

-Post section(pipe line 각 단계가 끝날때마다 조취를 취해주는 section)
스테이지가 끝난 이후의 결과에 따라서 후속 조치를 취할 수 있다
ex 성공시에 성공 이메일, 실패하면 중단 혹은 건너뛰기 등등
	
-Stages section
어떤 일들을 처리할 건지 일련의 stage를 정의함 즉 순서를 정해준다

-Steps section
한 stage 안에서의 단계로 일련의 스텝을 보여준다.
 + Declarative (각 stage안에서 어떤어떤 일을 할것인지에 대한 종류 - 즉 분기문을 넣을 수 있다)
 + Environment, stage, options, parameters, griggers, when 등이 있다
 + Environment ->  어떤 pipeline이나 stage scope의 환경 변수 설정
 + parameter -> 파이프라인 실행 시 파라미터 받음
 + Triggers -> 어떤 형태로 트리거 되는가(어떤 주기로 실행 되는가)
 + When -> 언제 실행되는가

Steps
stage 내부에는 여러가니 스텝들로 구성
여러 작업들을 실행 가능하며 플러그인을 깔면 사용할 수 있는 스텝들이 생겨난다
