pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/vishwanath0303/Boardgame.git'
            }
        }
         stage('Compile') {
            steps {
               sh "mvn compile"
            }
        }
         stage('Test') {
            steps {
                sh "mvn clean install"
                }
        }
         stage('Build & tag docker image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerHub', toolName: 'docker') {
                        sh "docker build -t vkulkarni0303/boardgame:latest ."
                        sh " docker run -d -p 8082:8082 vkulkarni0303/boardgame:latest "
            
                        
                    }
            }
            }
        }
        stage('Push docker image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push vkulkarni0303/boardgame:latest"
                        
                    }
            }
            }
        }
        
         
    }
}
