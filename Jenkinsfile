pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'java'
    }
    stages {
        stage('Checkout') {
            steps { git 'https://github.com/ahmedhamraj/spring-petclinic' }
        }
        stage('Build') {
            steps { sh 'mvn clean package' }
        }
        stage('Deploy') {
            steps {
                sh 'scp target/*.war ubuntu@<EC2-IP>:/opt/tomcat/webapps/'
            }
        }
    }
}
