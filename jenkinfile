pipeline {
    agent any
    tools {
        jdk 'jdk'
        maven 'maven'
        }
     environment{
        SCANNER_HOME= tool 'sonar-scanner'
        }
     stages {
         stage('Git Checkout') {
             steps {
                git branch: 'main', changelog: false, credentialsId: '--', poll: false, url: 'https://github.com/--'
            }
        }
         stage('Sonarqube') {
             steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Ekart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Ekart '''
                }
            } 
        }
         stage('COMPILE') {
             steps{
                sh "mvn clean compile -DskipTests=true"   
            }
        }
         stage('Image-Build') {
             steps{
                sh "mvn clean package -DskipTests=true"   
            }
        }
         stage('Docker Build Push') {
            steps{
                script{
                    withDockerRegistry(credentialsId: '--', toolName: 'docker') {
                        sh "docker build -t ekart -f docker/Dockerfile ."
                        sh "docker tag ekart narsi23/ekart:latest"
                        sh "docker push narsi23/ekart:latest"
                    }
                }    
            }
        }
         stage('Docker Container') {
            steps{
                script{
                    withDockerRegistry(credentialsId: '---', toolName: 'docker') {
                        sh "docker run -d --name ekartapp -p 8070:8070 narsi23/ekart:latest"
                    }
                }    
            }
        }
    }
}
