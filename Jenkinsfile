peline {
    agent any

    environment {
        REGISTRY = "https://index.docker.io/v1/"
        KUBECONFIG = '/var/lib/jenkins/.kube/config' // Path to kubeconfig file on Jenkins server
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Push Simple Static HTML') {
            steps {
                script {
                    // Write the Dockerfile to the workspace
                    writeFile file: 'Dockerfile', text: """
                    FROM nginx:alpine
                    COPY index.html /usr/share/nginx/html/index.html
                    """

                    // Build and tag the Docker image
                    sh "docker build -t nadirtech1/simple-web:latest ."

                    // Push the Docker image to Docker Hub
                    sh "docker push nadirtech1/simple-web:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Ensure kubectl is configured with the correct kubeconfig file
                    sh 'kubectl config use-context kubernetes-admin@kubernetes' // Replace with your actual cluster context if needed
                    sh 'kubectl apply -f k8s/simple-deployment.yaml'
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

