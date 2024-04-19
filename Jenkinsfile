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
         stage('Sonarqube') {
            steps {
               withSonarQubeEnv('sonar') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame \
                        -Dsonar.java.binaries=. '''
                   
        }
        
            }
        }
        stage('Quality Gate') {
            steps {
               script {
                  waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
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
        
        stage('Deploy to kubernetes') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.60.199:6443') {
                  sh 'kubectl apply -f deployment-service.yaml'
                  
}
                }
        }
        
        stage('Verify the Deployment') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.60.199:6443') {
                  sh 'kubectl get pods -n webapps'
                  sh 'kubectl get svc -n webapps'
}
                }
        }
        
         
    }
}
