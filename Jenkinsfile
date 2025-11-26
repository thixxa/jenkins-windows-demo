pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('test-dockerhubpassword')
        IMAGE_NAME = "thixxa/jenkins-windows-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/thixxa/jenkins-windows-demo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'No tests to run'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Use environment variable expansion on Windows
                bat "docker build -t ${env.IMAGE_NAME} ."

            }
        }

        stage('Docker Login') {
            steps {
                bat "echo ${env.DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${env.DOCKERHUB_CREDENTIALS_USR} --password-stdin"

            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%"
            }
        }

        stage('Deploy') {
            steps {
                bat """
                docker stop jenkins-windows-demo || exit 0
                docker rm jenkins-windows-demo || exit 0
                docker run -d -p 3000:3000 --name jenkins-windows-demo ${env.IMAGE_NAME}
                """
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console logs.'
        }
    }
}
