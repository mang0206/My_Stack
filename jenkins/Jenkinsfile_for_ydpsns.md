실제로 오류 없이 배포까지 완료된 jenkins file
-------------

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

            dir (''){
                sh """
                pip3 install -r requirement.txt
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
        
        stage('Deploy Backend') {
          agent any

          steps {
            echo 'Build Backend'

            dir ('/home/MJ/ydpsns/YDP-SNS'){
                sh '''
                python3 main.py
                '''
            }
          }

          post {
            success {
              mail  to: 'alswosp0206@gmail.com',
                    subject: "Deploy Success",
                    body: "Successfully deployed!"
                  
            }
          }
        }
    }
}
```
기존의 목표는 해당 git에 pull request 요청이 있다면 해당 jenkins가 실행되며  
pull request를 완료해준 다음 pull 한다음에 python 가상환경을 실행 시키고 requirements.txt를 통해 라이브러리를 설치한다음  
최종적으로 main.py가 실행되게끔 하고 싶었다.  

하지만 pull request의 경우 jenkins에서 제공해주는 Freestyle project를 통해서가 아닌 코드로서 pipline을 작성하는 방법을 찾을 수가 없어서 
trigger를 사용해 3분 주기로 branch를 pull해오는 방식을 선택했다.  
pull한 다음으로는 가상환경을 실행해야 하지만 jenkins에서는 가상환경을 실행하기 위한 python 경로가 기존 python 가상환경과 달랐다.  
이를 찾아야 했지만 추후에 도커를 사용하는 방식으로 발전시킬것이므로 현재는 그냥 가상환경 없이 라이브러리들을 설치했다.  
최종적으로 main.py를 실행시키는 것으로 마무리했다.

![image](https://user-images.githubusercontent.com/86212081/157670769-7b191652-2739-4647-b820-2c4662aff40e.png)  
처음 시도해보는 것이라 많이 미흡하지만 추후에 도커 및 쿠버네티스와 test 단계까지 추가할 것이다.  
