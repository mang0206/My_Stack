Jenkins와 도커를 사용해 서비스 베포해보기
============
(docker run --name jenkins-docker -d -p 8080:8080 -p 50000:50000 -v /home/jenkins:/var/jenkins_home -u root jenkins/jenkins)로 jenkins 실행 중

1.Jenkins로 git pull 테스트
-----------
```
pipeline {
    agent any

    //깃을 3분 주기로 끌어온다
    triggers {
        pollSCM('*/3 * * * *')
    }
    stages {
        // 레포지토리를 다운로드 받음
        stage('Prepare') {
            agent any
            // 이 스텝을 통해 pull 하는 것
            steps {
                echo 'Clonning Repository'

                git url: 'https://github.com/mang0206/YDP-SNS.git',
                    branch: 'MJ',
                    credentialsId: 'snsMJ'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    echo 'Successfully Cloned Repository'
                }

                always {
                  echo "i tried..."
                }
                // cleanup은 post가 끝났을때 진행할 내용 여기서는 로그만 찍어줬다
                cleanup {
                  echo "after all other post condition"
                }
            }
        }
    }
}
```
위 파이프라인 실행 결과  
![image](https://user-images.githubusercontent.com/86212081/158512749-3072d964-1660-4a19-96d8-de7f32863b7b.png)  
jenkins 로그를 확인해본 결과 /var/jenkins_home/workspace/sns_project@2로 git clone이 진행된걸 볼 수 있다.  

실제 해당 경로로 이동 확인  
![image](https://user-images.githubusercontent.com/86212081/158512874-358d135d-5ca9-4d08-859d-c14f28672628.png)
  (@2의 의미는 잘 모르겠다)  
  
 2.Jenkins로 pull한 디렉토리 home 디렉토리로 복사
 ---------
 ```
 pipeline {
    agent any

    //깃을 3분 주기로 끌어온다
    triggers {
        pollSCM('*/3 * * * *')
    }
    stages {
        // 레포지토리를 다운로드 받음
        stage('Prepare') {
            agent any
            // 이 스텝을 통해 pull 하는 것
            steps {
                echo 'Clonning Repository'

                git url: 'https://github.com/mang0206/YDP-SNS.git',
                    branch: 'MJ',
                    credentialsId: 'snsMJ'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    echo 'Successfully Cloned Repository'
                }

                always {
                  echo "i tried..."
                }
                // cleanup은 post가 끝났을때 진행할 내용 여기서는 로그만 찍어줬다
                cleanup {
                  echo "after all other post condition"
                }
            }
        }
        stage('Bulid Backend') {
          agent any
          steps {
            echo 'Build Backend'
            // Jenkins에서 pull한 파일을 /home/ubuntu 경로에 app 이름으로 복사
            dir (''){
                sh """
                cp -r /home/jenkins/workspace/sns_project@2 /home/ubuntu/app
                """
            }
          }

          post {
            // 작업 실패 시 pipe line을 종료한다.
            failure {
              error 'This pipeline stops here...'
            }
          }
        }
    }
}
 ```
![image](https://user-images.githubusercontent.com/86212081/158514479-f0e5b820-f650-45cd-a124-9b6da1bc6c77.png)  
triger에서 3분 주기로 해당 piplene이 실행때문에 별도의 재 build 없이 자동으로 bulid 했지만 복사하는 과정에서 오류 발생  
**log 확인 결과 해당 파일을 찾을수 없다고 한다.**  
![image](https://user-images.githubusercontent.com/86212081/158514649-2e9429cd-64b4-418f-85c6-a17927019e3b.png)  
log를 보면 **Running in /var/jenkins_home/workspace/sns_project@2**가 있어서 복사 명령어 변경 시도  
  (이전 단계가 실패했으므로 수동 bulid)

```
dir (''){
    sh """
    cp -r /home/jenkins/workspace/sns_project@2 /home/ubuntu/app
    """
}
 이 부분을 아래와 같이 바꿈          
 dir (''){
    sh """
    cp -r * /home/ubuntu/app
    """
}        
```
build 결과 아래와 같은 오류 발생  
![image](https://user-images.githubusercontent.com/86212081/158515893-975fddcc-6817-4e82-8ac2-219176bf65bf.png)  
해당 log로 추측해볼 때 현재 jenkins는 docker에서 실행 중이다. 그래서 현재 서버의 경로가 아닌 docker host에서 위 명령어를 실행하기 때문이라고 판단  

![image](https://user-images.githubusercontent.com/86212081/158516823-80573c26-6ac7-4b73-901e-4a661d466bf1.png)  
docker exec를 통해 확인결과 추측한것이 맞다고 확신  
jenkins가 docker container에서 실행되기 때문에 docker host에서 벗어날 필요가 있다.....
벗어나지 못한다면 다른 docker를 실행시키기 위해서 docker host 안에서 다시 docker를 설치해야 하는 문제점 발생
