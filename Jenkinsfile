pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'thuandocker'
        DOCKER_IMAGE = 'tqthuan2504/my-app'
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/tranquangthuan1211/automation_with_jenkins.git', branch: 'main'
            }
        }

        stage('Build Docker Image and Push') {
            steps {
                echo "Building Docker image with tag: ${env.IMAGE_TAG}"
                sh "../mvnw clean install -P buildDocker -DskipTests"
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
                        sh "docker tag ${imageName} ${imageName}:${commitId}"
                        sh "docker push ${imageName}:${commitId}"
                        sh "docker push ${imageName}:latest"
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
