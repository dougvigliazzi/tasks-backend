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
        stage ('Quality Gate') {
            steps {
                sleep(5)
                timeout(time: 1, unit:'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.0.118:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
            steps {
                dir('api-test') {
                    git credentialsId: 'GithubCredential', url: 'https://github.com/dougvigliazzi/tasks-api-test'
                    sh '/usr/local/apache-maven-3.6.0/bin/mvn test'
                }
            }
        }
        stage ('Deploy Frontend') {
            steps {
                dir('frontend') {
                    git credentialsId: 'GithubCredential', url: 'https://github.com/dougvigliazzi/tasks-frontend'
                    sh '/usr/local/apache-maven-3.6.0/bin/mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.0.118:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
                
            }
        }
        stage ('Functional Test') {
            steps {
                dir('functional-test') {
                    git credentialsId: 'GithubCredential', url: 'https://github.com/dougvigliazzi/tasks-functional-tests'
                    sh '/usr/local/apache-maven-3.6.0/bin/mvn test'
                }
            }
        }
    }
}

