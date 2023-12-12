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
              git branch: 'main', url: 'https://github.com/roja-1998/Ekart.git'
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
       stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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
                   withDockerRegistry(credentialsId: '26fdf24e-ec9a-43ce-b39b-1bf2707193f9', toolName: 'docker') {
                        sh "docker build -t shopping-cart1:dev -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart1:dev roja199/shopping-cart1:dev"
                    }
                }
            }
        } 
        stage('Push Docker Images') {
            steps {
                script{
                   withDockerRegistry(credentialsId: '26fdf24e-ec9a-43ce-b39b-1bf2707193f9', toolName: 'docker') {
                        sh "docker push roja199/shopping-cart1:dev"
                    }
                }
            }
        } 
        stage('Pull Docker Images') {
            steps {
                script{
                   withDockerRegistry(credentialsId: '26fdf24e-ec9a-43ce-b39b-1bf2707193f9', toolName: 'docker') {
                        sh "docker pull shopping-cart1:dev"
                    }
                }
            }
        } 
        
         stage('Deploy Docker Container') {
            steps {
                script{
                   withDockerRegistry(credentialsId: '26fdf24e-ec9a-43ce-b39b-1bf2707193f9', toolName: 'docker') {
                        sh "docker run -d -p 8070:8070 roja199/shopping-cart1:dev"
                    }
                }
            }
        } 
    }
}
