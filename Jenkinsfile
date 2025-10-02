pipeline {
    agent any

    tools {
        jdk 'java'   // your configured JDK in Jenkins
        maven 'Maven'
    }

    environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '172.31.17.72'
        // If using a .pem key, provide the full path here
        PEM_KEY = '/var/lib/jenkins/jenkins.pem'
        JAR_NAME = 'spring-petclinic-3.5.0-SNAPSHOT.jar'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ahmedhamraj/spring-petclinic2.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
               
                // Option 1: Using PEM key for EC2 authentication
                sh "scp -i ${PEM_KEY} -o StrictHostKeyChecking=no target/${JAR_NAME} ${EC2_USER}@${EC2_HOST}:/home/ubuntu/"
                sh "ssh -i ${PEM_KEY} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'nohup java -jar /home/ubuntu/${JAR_NAME} >/dev/null 2>&1 & exit'"
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}