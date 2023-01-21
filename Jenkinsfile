pipeline {
    agent any 
    environment {
    DOCKERHUB_CREDENTIALS = credentials('oayanda-dockerhub')
    }
    stages { 
        stage("Initial cleanup") {
			steps {
				dir("${WORKSPACE}") {
					deleteDir()
				}
			}
		}
        stage('SCM Checkout') {
            steps{
            git branch: 'main', url: 'https://github.com/oayanda/Todo_App.git'
            }
        }

        stage('Build docker image') {
            steps {  
                sh 'docker build -t oayanda/todo:$GIT_BRANCH-0.0.$BUILD_NUMBER .'
            }
        }
      
       stage('Creating docker container') {
            steps {
                script {
                    sh " docker run -d --name todo-app -p 8000:8000 oayanda/todo:$GIT_BRANCH-0.0.$BUILD_NUMBER"
                }
            }
        }

        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 54.209.4.85:8000"
                }
            }
        }
      
        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
      
        stage('push image') {
            steps{
                sh 'docker push oayanda/todo:$GIT_BRANCH-0.0.$BUILD_NUMBER'
            }
        }
      
       stage('Cleanup') {
			steps {
				cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
				
		                sh " docker stop todo-app"
                    sh " docker rm todo-app"
                    sh " docker rmi oayanda/todo:$GIT_BRANCH-0.0.$BUILD_NUMBER"				
			}
		}
      
      stage ('logout Docker') {
            steps {
                script {
                    sh " docker logout"
                }
            }
        }
    }

}
