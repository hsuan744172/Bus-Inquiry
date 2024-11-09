pipeline {
    agent any
    tools {
        nodejs 'NodeJS'
    }
    environment {
        PATH = "/usr/local/bin:/usr/bin:/bin:/node_modules/.bin:$PATH"
    }
    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    scannerHome = tool 'SonarQube Scanner'
                }
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.projectKey=connect-jenkins \
                        -Dsonar.projectName=MyProject \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sourceEncoding=UTF-8 \
                        -Dsonar.sources=src"
                }
            }
        }
    }
}