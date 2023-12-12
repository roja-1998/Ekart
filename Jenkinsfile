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
        stage('COMPILE') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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
    }
}
