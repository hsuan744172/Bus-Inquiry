pipeline {
    agent any
    tools {
        nodejs 'NodeJS'  // Make sure to configure NodeJS tool in Jenkins
    }
    environment {
        SONAR_SCANNER_OPTS = "-Xmx2048m"
        SONAR_HOST_URL = 'http://sonarqube:9000'  // Use SonarQube container name
    }
    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        stage('Verify Node') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    scannerHome = tool 'SonarQube Scanner'  // Ensure SonarQube Scanner is configured in Jenkins
                }
                withSonarQubeEnv('sonarqube') {  // Ensure the correct SonarQube environment configuration is used
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=connect-jenkins \
                        -Dsonar.projectName=MyProject \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sourceEncoding=UTF-8 \
                        -Dsonar.sources=src \
                        -Dsonar.javascript.node=/usr/bin/node \
                        -Dsonar.nodejs.executable=/usr/bin/node \
                        -Dsonar.scanner.metadataFilePath=/var/jenkins_home/workspace/SonarScanner/.scannerwork/report-task.txt
                    """
                }
            }
        }
    }
}
