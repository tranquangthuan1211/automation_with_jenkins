pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'thuandocker' // ID đã lưu trong Jenkins
        DOCKER_IMAGE = 'tqthuan2504/my-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/tranquangthuan1211/automation_with_jenkins.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    sed 's|IMAGE_PLACEHOLDER|${DOCKER_IMAGE}:${IMAGE_TAG}|' deployment.yaml > k8s.yaml
                    kubectl apply -f k8s.yaml
                    """
                }
            }
        }
    }
}
