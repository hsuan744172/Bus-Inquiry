pipeline {
    agent any
    stages {
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // 'SonarQube' 是您在 Jenkins 中配置的 SonarQube 服務名稱
                    sh """
                    sonar-scanner \
                      -Dsonar.projectKey=my-project \
                      -Dsonar.projectName=MyProject \
                      -Dsonar.projectVersion=1.0 \
                      -Dsonar.sourceEncoding=UTF-8 \
                      -Dsonar.sources=src \
                      -Dsonar.language=js
                    """
                }
            }
        }
    }
}
