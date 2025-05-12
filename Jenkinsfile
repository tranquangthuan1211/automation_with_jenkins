pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'thuandocker'
        DOCKER_IMAGE = 'tqthuan2504/my-app'
        IMAGE_TAG = "latest"
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
                     withCredentials([usernamePassword(
                        credentialsId: env.DOCKER_CREDENTIALS_ID,
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )]) {
                        sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
                        
                        sh "docker build -t app:${env.IMAGE_TAG} ."
                        sh "docker tag app:${env.IMAGE_TAG} ${env.DOCKER_IMAGE}:latest"
                        sh "docker push ${env.DOCKER_IMAGE}:latest"
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
