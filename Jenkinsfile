#this pipelinefile is written by surendra
pipeline {
    agent any
    environment{
          scannerHome = tool 'mysonar'
    }
    stages {
        stage ('clean WS') {
            steps {
                cleanWs()
            }
        }
        stage ('code pull') {
            steps {
                git branch: 'main', url: 'https://github.com/devops0014/Zomato-Repo.git'
            }
        }
        stage ('CQA') {
            steps {
                withSonarQubeEnv('mysonar') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey='zomato'" 
               }
            }
        }
            stage ('Qwality Gate') {
                steps {
                    script {
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-cred'
                    }
                }
            }
            stage ('build') {
                steps {
                    sh 'docker build -t image1 .'
                }
            }
            stage ('trivy scan') {
                steps {
                    sh 'trivy image image1'
                }
            }
            stage ('tag') {
                steps {
                    sh 'docker tag image1 surendra84/zomato:zomato1'
                }
            }
            stage ('push') {
                steps {
                    script {
                       withDockerRegistry(credentialsId: 'dockerhub-tok') {
                        sh 'docker push surendra84/zomato:zomato1'
                      }
                    }
                }
            }
            stage ('docker deploy') {
                steps {
                    sh 'docker run -itd --name zomato -p 3000:3000 surendra84/zomato:zomato1 '
                }
            }
    }
}
