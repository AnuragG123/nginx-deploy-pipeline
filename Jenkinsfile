pipeline {
    agent any

    tools {
        maven 'Maven 3'
        jdk 'JDK 11'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token')
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/youruser/my-nginx-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    dockerImage = docker.build("my-nginx-app:latest")
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials : ['ec2-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no Dockerfile ec2-user@<EC2_IP>:/home/ec2-user/
                    ssh ec2-user@<EC2_IP> "docker build -t my-nginx-app /home/ec2-user && docker run -d -p 80:80 my-nginx-app"
                    '''
                }
            }
        }
    }
}
