pipeline {
    agent any

    tools {
        jdk 'java'   // your configured JDK in Jenkins
        maven 'Maven'
    }

    environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '172.31.28.40'
        PEM_KEY = '/var/lib/jenkins/.ssh/jenkins.pem'   // your EC2 PEM file
        JAR_NAME = 'spring-petclinic-3.5.0-SNAPSHOT.jar'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ahmedhamraj/spring-petclinic.git'
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
                sh '''
                # Stop any running instance of the jar
                ssh -i ${PEM_KEY} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'pkill -f ${JAR_NAME} || true'

                # Copy the new jar to EC2
                scp -i ${PEM_KEY} -o StrictHostKeyChecking=no target/${JAR_NAME} ${EC2_USER}@${EC2_HOST}:/home/ubuntu/

                # Start the jar in background and detach SSH
                ssh -i ${PEM_KEY} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} \
                    'nohup java -jar /home/ubuntu/${JAR_NAME} >/dev/null 2>&1 & exit'
                '''
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
