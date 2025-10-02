pipeline {
    agent any

    tools {
        jdk 'java'      // Jenkins configured JDK
        maven 'Maven'   // Jenkins configured Maven
    }

    environment {
        EC2_HOST = '172.31.28.40'
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
                sshagent(credentials: ['ec2-ssh']) {
                    sh '''
                        # Stop running instance
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_HOST} "pkill -f ${JAR_NAME} || true"

                        # Copy the new jar
                        scp -o StrictHostKeyChecking=no target/${JAR_NAME} ubuntu@${EC2_HOST}:/home/ubuntu/

                        # Start the jar in background
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_HOST} \
                            "nohup java -jar /home/ubuntu/${JAR_NAME} >/dev/null 2>&1 &"
                    '''
                }
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
