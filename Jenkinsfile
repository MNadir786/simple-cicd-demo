pipeline {
    agent any

    environment {
        REGISTRY = "https://index.docker.io/v1/"
        IMAGE_NAME = "nadirtech1/simple-web"  // Docker Hub image name
        IMAGE_TAG = "latest" // You can use an environment variable like build ID for unique tags if needed
        KUBECONFIG = '/var/lib/jenkins/.kube/config' // Path to kubeconfig file on Jenkins server
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Create Dockerfile') {
            steps {
                script {
                    // Create a Dockerfile that copies the index.html into an Nginx container
                    echo 'FROM nginx:alpine' > Dockerfile
                    echo 'COPY index.html /usr/share/nginx/html/index.html' >> Dockerfile
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image from the created Dockerfile
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Authenticate with Docker Hub and push the image
                    docker.withRegistry(REGISTRY, 'docker-hub-credentials') {
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh 'kubectl config use-context kubernetes-admin@kubernetes' // Ensure you're using the correct K8s context
                    sh 'kubectl apply -f k8s/simple-web-deployment.yaml' // Apply your Kubernetes deployment
                    sh 'kubectl apply -f k8s/service.yaml' // Apply your Kubernetes service
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

