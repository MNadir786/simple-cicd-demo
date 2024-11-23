pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials' // Jenkins credential ID
        DOCKER_IMAGE_BACKEND = "mnadir786/backend:${env.BUILD_ID}" // Lowercase repository name
        DOCKER_IMAGE_FRONTEND = "mnadir786/frontend:${env.BUILD_ID}" // Lowercase repository name
        REGISTRY = "https://index.docker.io/v1/"
        DOCKER_BUILDKIT = "1" // Enable Docker BuildKit
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Build backend and frontend images with Docker BuildKit enabled
                    sh "docker build -t ${DOCKER_IMAGE_BACKEND} ./backend"
                    sh "docker build -t ${DOCKER_IMAGE_FRONTEND} ./frontend"
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Push backend and frontend images to Docker Hub
                    docker.withRegistry(REGISTRY, DOCKER_HUB_CREDENTIALS) {
                        sh "docker push ${DOCKER_IMAGE_BACKEND}"
                        sh "docker push ${DOCKER_IMAGE_FRONTEND}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy the updated images to Kubernetes
                    sh 'kubectl apply -f k8s/backend-deployment.yaml'
                    sh 'kubectl apply -f k8s/frontend-deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after pipeline run
        }
    }
}

