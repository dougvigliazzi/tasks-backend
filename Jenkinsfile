pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                sh '/usr/local/apache-maven-3.6.0/bin/mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Test ') {
            steps {
                sh '/usr/local/apache-maven-3.6.0/bin/mvn test'
            }
        }
        stage ('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.0.120:9000 -Dsonar.login=6852b0c4c4cc5d20de9eed3da28ebea1c240372a -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
    }
}
