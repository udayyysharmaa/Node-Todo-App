pipeline {
    agent any
    
    environment{
        Sonar_Home = tool "sonar-scanner"
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/udayyysharmaa/Node-Todo-App.git'
            }
        }
        stage('Sonar-qube-check') {
            steps {
                withSonarQubeEnv('sonar') {
                sh "$Sonar_Home/bin/sonar-scanner -Dsonar.projectName=nodeproject -Dsonar.projectKey=nodeproject"
    }
            }
        }
        stage('Quality Gate Check') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: false
                }
                
            }
        }
        stage('Trivy Check') {
            steps {
                sh "trivy fs --format json -o file.json ."
            }
        }
        stage('doker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub') {
                        sh "docker build -t onlinelearningofficial/node-project:v1 ."
                    }
                }
            }
        }
        stage('Image Scan') {
            steps {
                sh "trivy image --format json -o image.js onlinelearningofficial/node-project:v1 "
            }
        }
        stage('Push To Docker Hub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub') {
                        sh "docker push onlinelearningofficial/node-project:v1 "
                    }
                }
            }
        }
        stage('Build the Project') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub') {
                        sh "docker run -d -p 8000:8000 onlinelearningofficial/node-project:v1 "
                    }
                }
            }
        }

    }
}
