pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                sh '/usr/local/apache-maven-3.6.0/bin/mvn clean package -DskipTests=true'
            }
        }
    }
}