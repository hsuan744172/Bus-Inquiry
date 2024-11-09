pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // 使用構建工具進行專案的建置，例如：sh './gradlew build'
                sh './gradlew build' // 或者使用其他構建工具，例如 mvn clean install
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // 'SonarQube' 是您在 Jenkins 中配置的 SonarQube 服務名稱
                    sh """
                    sonar-scanner \
                      -Dsonar.projectKey=test \
                      -Dsonar.projectName=test \
                      -Dsonar.projectVersion=1.0 \
                      -Dsonar.sourceEncoding=UTF-8 \
                      -Dsonar.modules=javascript-module \
                      -Djavascript-module.sonar.projectName=JavaScript Module \
                      -Djavascript-module.sonar.language=js \
                      -Djavascript-module.sonar.sources=. \
                      -Djavascript-module.sonar.projectBaseDir=src
                    """
                }
            }
        }
    }
}
