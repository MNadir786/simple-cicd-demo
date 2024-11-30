pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials' // Jenkins credential ID for Docker Hub
        GIT_COMMIT_HASH = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim() // Get the short Git commit hash for image tag
        DOCKER_IMAGE_BACKEND = "nadirtech1/backend:${GIT_COMMIT_HASH}" // Tag with commit hash
        DOCKER_IMAGE_FRONTEND = "nadirtech1/frontend:${GIT_COMMIT_HASH}" // Tag with commit hash
        REGISTRY = "https://index.docker.io/v1/"
        KUBECONFIG = '/var/lib/jenkins/.kube/config' // Path to kubeconfig file on Jenkins server
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
                    // Build backend and frontend images with Docker BuildKit enabled and tag them with the Git commit hash
                    sh "docker build -t ${DOCKER_IMAGE_BACKEND} ./backend"
                    sh "docker build -t ${DOCKER_IMAGE_FRONTEND} ./frontend"
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Push backend and frontend images to Docker Hub with the tag
                    docker.withRegistry(REGISTRY, DOCKER_HUB_CREDENTIALS) {
                        sh "docker push ${DOCKER_IMAGE_BACKEND}"
                        sh "docker push ${DOCKER_IMAGE_FRONTEND}"
                    }
                }
            }
        }

        stage('Update Kubernetes Deployments') {
            steps {
                script {
                    // Update Kubernetes deployment YAML files with the correct image tag
                    sh "sed -i 's#nadirtech1/backend:latest#${DOCKER_IMAGE_BACKEND}#g' k8s/backend-deployment.yaml"
                    sh "sed -i 's#nadirtech1/frontend:latest#${DOCKER_IMAGE_FRONTEND}#g' k8s/frontend-deployment.yaml"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Ensure kubectl is configured with the correct kubeconfig file
                    sh 'kubectl config use-context kubernetes-admin@kubernetes' // Replace with your actual cluster context if needed
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

