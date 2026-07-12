pipeline {

    agent any

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Repository already checked out by Jenkins'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp:v1 .'
            }
        }

        stage('Remove Old Container') {
            steps {
                sh '''
                docker stop myapp-container || true
                docker rm myapp-container || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker run -d \
                --name myapp-container \
                -p 80:80 \
                myapp:v1
                '''
            }
        }

    }

}