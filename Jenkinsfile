pipeline {
    agent any
    environment {
        BACKEND_IMAGE = 'nadirtech/backend'
        FRONTEND_IMAGE = 'nadirtech/frontend'
    }
    stages {
        stage('Clone Repository') {
            steps {
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
                    docker.build(DOCKER_IMAGE_BACKEND, './backend')
                    docker.build(DOCKER_IMAGE_FRONTEND, './frontend')
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
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
                    // Assuming kubectl is configured on Jenkins server
                    sh 'kubectl apply -f k8s/backend-deployment.yaml'
                    sh 'kubectl apply -f k8s/frontend-deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

