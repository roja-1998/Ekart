pipeline {
    agent any
    tools{
        jdk  'jdk17'
        maven  'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
             git branch: 'main', credentialsId: 'github-cred', url: 'https://github.com/roja-1998/Ekart.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Test') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Ekart \
                   -Dsonar.projectKey=Ekart  -Dsonar.java.binaries=. '''
               }
            }
        }   
      stage('Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        } 
        stage('Docker Build & Tag') {
            steps {
                script{
                 withDockerRegistry(credentialsId: 'br') {
                        sh "docker build -t shopping-cart:dev -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart:dev roja199/shopping-cart:dev"
                    }
                }
            }
        } 
        stage('Push Docker Images') {
            steps {
                script{
                  withDockerRegistry(credentialsId: 'br') {
                        sh "docker push roja199/shopping-cart:dev"
                    }
                }
            }
        } 
        
         stage('Deploy Docker Container') {
            steps {
                script{
                 withDockerRegistry(credentialsId: 'br'){
                        sh "docker run -d -p 8070:8070 roja199/shopping-cart:dev"
                    }
                }
            }
        } 
    }
}
