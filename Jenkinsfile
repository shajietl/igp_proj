pipeline {
    agent any

    environment {
        DEPLOY_HOST = "3.93.239.24"           // Your EC2 public IP
        DEPLOY_USER = "ec2-user"              // EC2 user
        SSH_CREDENTIALS = "ec2-deploy-key"    // Jenkins SSH key ID
        APP_DIR = "/home/ec2-user/IGP_proj"   // Deployment folder on EC2
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shajietl/IGP_proj.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIALS}"]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} '
                        mkdir -p ${APP_DIR}
                        cd ${APP_DIR}
                        git pull || git clone https://github.com/shajietl/IGP_proj.git .
                        cp target/*.jar ${APP_DIR}/app.jar
                        pkill -f "java -jar app.jar" || true
                        nohup java -jar ${APP_DIR}/app.jar > ${APP_DIR}/app.log 2>&1 &
                    '
                    """
                }
            }
        }
    }
}
