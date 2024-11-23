# CI/CD Pipeline with Jenkins, Docker, and Kubernetes

This project demonstrates a CI/CD pipeline using Jenkins, Docker, and Kubernetes. The pipeline includes:

- Automated builds and pushes of Docker images.
- Deployment of backend and frontend services to Kubernetes.
- Integration with GitHub via webhooks.

## Project Structure

```plaintext
├── backend/                 # Backend application code
├── frontend/                # Frontend application code
├── Jenkinsfile              # Jenkins pipeline configuration
├── k8s/                     # Kubernetes manifests
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   ├── service.yaml
├── .gitignore               # Git ignore rules
└── README.md                # Project documentation

