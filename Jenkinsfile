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
		stage('Sonarqube Analysis') {
            steps {
                    withSonarQubeEnv('sonar') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Boardgame \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Boardgame '''
    
                }
            }
        }
         stage('Build & tag docker image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t vkulkarni0303/boardgame:latest ."
    
            
                        
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
		stage("Deploy "){
            steps{
                sh "docker run --name boardgame -d -p 8000:8000  boardgame:latest "
            }
        }
        
         
    }
}
