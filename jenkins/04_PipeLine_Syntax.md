https://www.jenkins.io/doc/book/pipeline/syntax/

Pipeline Syntax
=============

1.agent
---------------
![image](https://user-images.githubusercontent.com/86212081/156156774-25b6665a-e8ee-4f24-931a-5031126c499b.png)  
1. 파이프라인 맨 위에서 agent를 none으로 설정할 수 도 있다. 이렇게 되면 각 stage 마다 agnet를 선언해 주어야 한다.

2.Post
--------------
Post는 파이프라인 또는 스테이지 실행 완료 시 실행되는 하나 이상의 추가 단계를 정의한다  
![image](https://user-images.githubusercontent.com/86212081/156157309-7acb9fd4-1246-4891-b2ad-4c37b40c1d1e.png)  

**post의 종류**
1. **always** : 파이프라인 또는 스테이지 실행의 완료 상태에 관계없이 실행
2. **changed** : 현재 단계 실행이 이전 실행과 다른 완료 상태인 경우에만 실행
3. **fixed** : 현재 단계 실행이 성공적이고 이전 실행이 실패했거나 불안정한 경우에만 사후 단계를 실행
4. **regression** : 현재 단계의 실행 상태가 실패, 불안정 또는 중단이고 이전 실행이 성공한 경우에만 사후 단계를 실행
5. **aborted** : 현재 파이프라인 또는 단계의 실행이 "중단됨" 상태인 경우에만 사후 단계를 실(일반적으로 웹 UI에서 회색으로 표시)  
6. **failure** : 현재 파이프라인 또는 단계의 실행이 일반적으로 웹 UI에서 빨간색으로 표시되는 "실패" 상태인 경우에만 사후 단계를 실행
7. **success** : 현재 파이프라인 또는 단계의 실행이 일반적으로 웹 UI에서 파란색 또는 녹색으로 표시되는 "성공" 상태인 경우에만 사후 단계를 실행
8. **unstable** : 현재 파이프라인 또는 단계의 실행이 일반적으로 테스트 실패, 코드 위반 등으로 인해 "불안정한" 상태인 경우에만 사후 단계를 실행(노란색 표시)
9. **unsuccessful** : failure과 똑같음
10. **cleanup** : 파이프라인이나 단계의 상태에 관계없이 다른 모든 사후 조건이 평가된 후 이 사후 조건의 단계를 실행

3.Option(사이트 참고)
------------
options 지시문을 사용하면 파이프라인 자체 내에서 파이프라인별 옵션을 구성할 수 있다.  
파이프라인은 buildDiscarder와 같은 여러 옵션을 제공하지만 타임스탬프와 같은 플러그인에서 제공할 수도 있다.  

4.Parameter
-----------
![image](https://user-images.githubusercontent.com/86212081/156159896-d679b795-c817-4b36-84ac-79efcbb94d44.png)  

5.triggers
----------
triggers 지시문은 파이프라인이 다시 트리거되어야 하는 자동화된 방법을 정의한다.  
GitHub 또는 BitBucket과 같은 소스와 통합된 파이프라인의 경우 웹훅 기반 통합이 이미 존재할 가능성이 있으므로 트리거가 필요하지 않을 수 있다.  
현재 사용 가능한 트리거는 cron, pollSCM 및 upstream이다.  
![image](https://user-images.githubusercontent.com/86212081/156160202-37a525bf-2ce3-4f58-aed4-3d0fff20520f.png)

그 외 문법은 사이트 참고;;
