# FastAPI Deployment App

A simple FastAPI application deployed using Docker and Kubernetes, with Jenkins CI/CD and Argo CD for continuous delivery.

## Project Structure

```text
deployment-app/
│
├── .gitignore
├── app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile
├── README.md
│
└── k8s/
    ├── deployment.yaml
    └── service.yaml