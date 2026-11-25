# GitOps Workflow Using ArgoCD on Kubernetes

## Project Overview

This project demonstrates how to implement a GitOps workflow using ArgoCD and Kubernetes.

The main goal is to automate application deployment using GitHub as the source of truth. ArgoCD continuously monitors the GitHub repository and automatically synchronizes Kubernetes resources whenever deployment configuration changes are pushed to GitHub.

The application is packaged using Docker and deployed on a local Kubernetes cluster using Minikube.

## Project Objectives

- Understand GitOps principles and workflow.
- Build a Docker image for a web application.
- Deploy applications using Kubernetes.
- Integrate GitHub with ArgoCD.
- Automate application synchronization.
- Manage application deployments through Git changes.
- Verify application health and deployment status.

## Tools and Technologies

| Tool | Purpose |
|---|---|
| Docker | Containerize the web application |
| Kubernetes | Manage application containers |
| Minikube | Run a local Kubernetes cluster |
| ArgoCD | Automate GitOps deployment |
| Git | Version control |
| GitHub | Store application code and Kubernetes manifests |
| NGINX | Host the web application |
| YAML | Define Kubernetes resources |

## Project Architecture

GitHub Repository → ArgoCD → Kubernetes → NGINX Application

1. The application source code and Kubernetes YAML files are stored in GitHub.
2. ArgoCD monitors the GitHub repository.
3. When changes are pushed to GitHub, ArgoCD detects the updated Kubernetes manifests.
4. ArgoCD automatically synchronizes the changes with the Kubernetes cluster.
5. Kubernetes creates or updates the application pods.
6. The application is accessed through a Kubernetes Service.

## Project Folder Structure

```text
-ArgoCD-and-Kubernetes/
│
├── app/
│   ├── Dockerfile
│   └── index.html
│
├── kubernetes/
│   ├── deployment.yaml
│   └── service.yaml
│
├── argocd/
│   └── application.yaml
│
└── README.md
```

## Implementation Steps

### 1. Create the Web Application

Created a simple HTML web application using NGINX.

The application displays a welcome message and project information.

### 2. Create Docker Image

Created a Dockerfile using the NGINX Alpine base image.

Built the Docker image:

```powershell
minikube image build -t gitops-demo:v1 ./app
```

The image was built directly inside Minikube so Kubernetes could use it locally.

### 3. Start Kubernetes Cluster

Started a local Kubernetes cluster using Minikube:

```powershell
minikube start --driver=docker --cpus=2 --memory=3900
```

Verified the cluster:

```powershell
kubectl get nodes
```

### 4. Configure ArgoCD

Created the ArgoCD namespace:

```powershell
kubectl create namespace argocd
```

Installed ArgoCD in the Kubernetes cluster.

Verified ArgoCD components:

```powershell
kubectl get pods -n argocd
```

### 5. Create Kubernetes Deployment

Created `deployment.yaml` to manage the application pods.

Configured two replicas to run the application.

Important configurations:
- Deployment name: gitops-demo
- Replicas: 2
- Container port: 80
- Image: gitops-demo:v1

### 6. Create Kubernetes Service

Created `service.yaml` using NodePort.

The Service exposes the application on port 30080 within the Kubernetes cluster.

### 7. Push Project to GitHub

Stored the application source code and Kubernetes manifests in GitHub.

Repository:

https://github.com/prabhajayaraj/-ArgoCD-and-Kubernetes

### 8. Configure ArgoCD Application

Created `application.yaml` to connect ArgoCD with the GitHub repository.

Configured:
- Repository URL
- Main branch
- Kubernetes manifest directory
- Destination Kubernetes namespace
- Automated synchronization
- Prune
- Self-healing

Applied the ArgoCD Application:

```powershell
kubectl apply -f argocd/application.yaml
```

### 9. Enable Automatic Synchronization

Configured automated synchronization using:

```yaml
syncPolicy:
  automated:
    prune: true
    selfHeal: true
```

**Prune:** Removes Kubernetes resources that are deleted from the Git repository.

**Self-Heal:** Automatically corrects manual changes made directly to Kubernetes resources when they differ from Git.

### 10. Verify Application Deployment

Checked application pods:

```powershell
kubectl get pods
```

Checked Deployment:

```powershell
kubectl get deployments
```

Checked Service:

```powershell
kubectl get services
```

Verified ArgoCD application status:

```powershell
kubectl get applications -n argocd
```

The application successfully reached:

- Sync Status: Synced
- Health Status: Healthy
- Deployment: 2/2 Ready

### 11. Test GitOps Automatic Deployment

Updated the application image from Version 1 to Version 2.

Built the updated image:

```powershell
minikube image build -t gitops-demo:v2 ./app
```

Updated the image in `deployment.yaml`:

```yaml
image: gitops-demo:v2
imagePullPolicy: IfNotPresent
```

Committed and pushed the updated manifest to GitHub.

ArgoCD detected the new configuration and automatically synchronized the Kubernetes Deployment.

Verified the new image:

```powershell
kubectl get deployment gitops-demo -o wide
```

The Version 2 application was successfully deployed and verified in the browser.

## Access the Application

Used Kubernetes port forwarding:

```powershell
kubectl port-forward svc/gitops-demo-service 8082:80
```

Access the application:

http://localhost:8082

## Final Project Results

- Successfully created a Dockerized NGINX application.
- Successfully deployed the application on Minikube.
- Successfully integrated GitHub with ArgoCD.
- Configured automated GitOps synchronization.
- Successfully deployed two Kubernetes replicas.
- Updated the application image through GitHub.
- Verified automatic Kubernetes deployment updates.
- Successfully accessed the updated application in the browser.

## What I Learned

- GitOps concepts and implementation.
- Kubernetes Deployments, Pods and Services.
- Docker image building and containerization.
- ArgoCD installation and configuration.
- GitHub repository integration.
- Automated synchronization and self-healing.
- Kubernetes application monitoring and troubleshooting.
- Managing deployments through Git version control.

## GitHub Repository

https://github.com/prabhajayaraj/-ArgoCD-and-Kubernetes

## Conclusion

This project helped me understand how GitOps simplifies application deployment and management using Kubernetes and ArgoCD.

By integrating GitHub with ArgoCD, I implemented an automated deployment workflow where Git serves as the source of truth for Kubernetes configurations.

This project provided practical experience in containerization, Kubernetes orchestration, GitOps automation and continuous deployment.
