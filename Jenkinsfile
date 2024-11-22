pipeline {
    agent any
    environment {
        BACKEND_IMAGE = 'nadirtech/backend'
        FRONTEND_IMAGE = 'nadirtech/frontend'
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
                    docker.build("${BACKEND_IMAGE}:${env.BUILD_ID}", "./backend")
                    docker.build("${FRONTEND_IMAGE}:${env.BUILD_ID}", "./frontend")
                }
            }
        }
        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                            docker.image("${BACKEND_IMAGE}:${env.BUILD_ID}").push()
                            docker.image("${FRONTEND_IMAGE}:${env.BUILD_ID}").push()
                        }
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    script {
                        sh 'kubectl --kubeconfig=$KUBECONFIG apply -f k8s-deployment.yml'
                    }
                }
            }
        }
    }
}

