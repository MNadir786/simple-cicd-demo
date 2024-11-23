pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials' // Jenkins credential ID
        DOCKER_IMAGE_BACKEND = "MNadir786/backend:${env.BUILD_ID}"
        DOCKER_IMAGE_FRONTEND = "MNadir786/frontend:${env.BUILD_ID}"
        REGISTRY = "https://index.docker.io/v1/"
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
                    // Build Docker images
                    docker.build(DOCKER_IMAGE_BACKEND, './backend')
                    docker.build(DOCKER_IMAGE_FRONTEND, './frontend')
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Use Jenkins credentials store to authenticate with Docker Hub
                    docker.withRegistry(REGISTRY, DOCKER_HUB_CREDENTIALS) {
                        docker.image(DOCKER_IMAGE_BACKEND).push()
                        docker.image(DOCKER_IMAGE_FRONTEND).push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh 'kubectl apply -f k8s/backend-deployment.yaml'
                    sh 'kubectl apply -f k8s/frontend-deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean up workspace
        }
    }
}

