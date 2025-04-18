pipeline {
    agent any
    tools{
        jdk 'jdk21'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('git clone') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '45407007-b29f-4e77-895e-1c1dea2ab1df', poll: false, url: 'https://github.com/Lalit-Tiwa/Ekart.git'
            }
        }
         stage('compile') {
            steps {
                sh 'mvn clean compile -DskipTests=true'
            }
        }
          stage('owasp dc') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=shopping-app -Dsonar.projectKey=shopping-app -Dsonar.java.binaries=.'
 
                }
            }
        }
        stage('docker build') {
            steps {
                script{
                withDockerRegistry(credentialsId: 'f91778bb-f84e-4418-9615-a53422353f6e', toolName: 'docker') {
                    sh 'docker pull adijaiswal/shopping-cart:latest'
                    sh 'docker tag adijaiswal/shopping-cart:latest lalit1995/shopping-cart:latest'
                    sh 'docker push lalit1995/shopping-cart:latest'
                }
               } 
            }
            
        }
        stage('deploy') {
            steps {
                sh 'docker run -d --name shopping -p 8070:8070 lalit1995/shopping-cart:latest'
            }
        }
        
    }
}
