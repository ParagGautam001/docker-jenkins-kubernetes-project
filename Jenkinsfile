pipeline {
    agent any

    environment {
        IMAGE_NAME = "paraggautam1/myapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
        K8S_SERVER = "172.31.32.129"   // Kubernetes Private IP
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo "Repository cloned by Jenkins"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_SERVER} '
                kubectl set image deployment/myapp-deployment myapp=${IMAGE_NAME}:${IMAGE_TAG}
                '
                """
            }
        }

        stage('Verify Rollout') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_SERVER} '
                kubectl rollout status deployment/myapp-deployment
                '
                """
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
