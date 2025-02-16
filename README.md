# Full-Stack Application Deployment with GitOps

## Overview

This repository is designed to deploy a **Full-Stack MERN Application** on a Kubernetes (EKS) cluster using **ArgoCD** for continuous delivery and **GitOps** principles. The repository employs a **modular infrastructure** for managing backend, frontend, and MongoDB deployment. It follows best practices and enterprise standards, keeping each layer (MongoDB, Backend, Frontend) as distinct resources.

The deployment process ensures that the **Backend** service is deployed first, followed by the **Frontend**. This is achieved by leveraging **Sync Waves** in ArgoCD, which ensures the right order of deployment.

### Key Technologies and Tools

- **GitOps**: A Kubernetes deployment model where the Git repository is the source of truth for the application’s desired state.
- **ArgoCD**: A continuous delivery tool for Kubernetes that allows declarative management of applications from Git repositories.
- **Kubernetes (EKS)**: A managed Kubernetes service from AWS for deploying containerized applications.
- **Sync Waves**: A feature in ArgoCD that enables you to control the order of resource deployments.

## Repository Structure

This repository follows the **standard GitOps structure** and is organized as follows:

```bash
gitops-repo/
├── base/
│   ├── backend/
│   │   ├── backend-deployment.yaml         # Backend Deployment Manifest
│   │   ├── backend-service.yaml            # Backend Service Manifest
│   │   ├── backend-hpa.yaml                # HorizontalPodAutoscaler for Backend
│   │   └── kustomization.yaml              # Kustomization for Backend
│   ├── frontend/
│   │   ├── frontend-deployment.yaml        # Frontend Deployment Manifest
│   │   ├── frontend-service.yaml           # Frontend Service Manifest
│   │   ├── frontend-hpa.yaml               # HorizontalPodAutoscaler for Frontend
│   │   └── kustomization.yaml              # Kustomization for Frontend
│   ├── namespaces/
│   │   ├── backend-ns.yaml                 # Namespace for Backend
│   │   └── frontend-ns.yaml                # Namespace for Frontend
├── README.md                               # Project Documentation
├── argo-app.yaml                           # ArgoCD Application Manifest

```

## How It Works

### 1. ArgoCD Application Deployment

The application is managed and deployed using **ArgoCD**, a GitOps continuous delivery tool for Kubernetes. ArgoCD ensures that the state of the cluster matches the desired state stored in this Git repository. This Git repository serves as the source of truth for deploying backend and frontend applications to the Kubernetes cluster.

### 2. Sequential Deployment using Sync Waves

To ensure the backend is deployed before the frontend, **Sync Waves** are utilized. Sync Waves in ArgoCD allow you to define the sequence in which Kubernetes resources should be deployed. The sync wave system uses annotations to determine the order of deployment.

- **Backend Deployment**: The backend application is deployed first.
- **Frontend Deployment**: After the backend is successfully deployed, the frontend application is deployed.

By applying sync waves, we control the deployment order so that the frontend waits for the backend to be ready before it starts.

### 3. Modular Infrastructure

The infrastructure is modular, allowing for flexibility in managing and scaling individual components.

- **MongoDB**: The MongoDB sharded cluster (in a separate namespace) is expected to be available for the backend to consume data. It is managed independently, and its deployment can be done separately as needed.
- **Backend**: The backend application, responsible for providing data to the frontend, is deployed in its own namespace.
- **Frontend**: The frontend application connects to the backend service to fetch and display data.

This modular approach promotes scalability, as different components can be independently scaled and managed.

### 4. Kustomization for Environment Management

The project utilizes **Kustomize** to manage different environments. Kustomize is used to manage Kubernetes resources and create environment-specific configurations without changing the underlying base resources.

In this case, Kustomize is used to apply different configurations for backend and frontend in a consistent manner, allowing easy promotion of changes across different environments.

### 5. ArgoCD Sync

ArgoCD continuously monitors the Git repository and synchronizes the Kubernetes cluster with the defined state. The repository contains the necessary resources, including:

- Namespaces for both backend and frontend applications.
- Configurations for backend and frontend deployments, services, and auto-scaling.
- Sync waves annotations to enforce the correct deployment order.

### 6. Deployment Flow

The general flow for deploying this application is as follows:

1. **MongoDB (Sharded Cluster)**: The sharded MongoDB cluster is deployed and available in its designated namespace. The backend application can interact with it for database operations.
2. **Backend**: Once MongoDB is available, the backend application is deployed. The backend service is exposed to the frontend using a Kubernetes service.
3. **Frontend**: After the backend is deployed and fully operational, the frontend application is deployed. The frontend application connects to the backend service for data and user interactions.

### 7. Automated Sync and Self-Heal

ArgoCD automatically manages synchronization of the Kubernetes resources, ensuring that the cluster matches the state in the Git repository. It also supports **self-healing**, meaning that if any manual changes are made directly in the cluster, ArgoCD will automatically correct the cluster to match the Git state.

### 8. GitOps Workflow

The GitOps approach provides the following benefits:

- **Declarative Deployment**: The state of the applications is declared in Git. ArgoCD automatically synchronizes the cluster with the Git repository.
- **Version Control**: Changes to the infrastructure and applications are versioned, making it easy to track updates and roll back if needed.
- **Seamless Rollback**: If something goes wrong with the deployment, ArgoCD can automatically revert to the last known good state by syncing with the Git repository.
- **Collaboration**: Teams can collaborate effectively, as all deployment configurations are stored in the Git repository.

By using GitOps with ArgoCD, this setup automates the entire deployment pipeline, ensuring the backend and frontend applications are continuously deployed in a consistent, repeatable manner across different environments.

## Getting Started

To deploy this project, follow these steps:

1. **Setup ArgoCD** in your Kubernetes cluster (for example, on AWS EKS, GKE, or any other managed Kubernetes service).
2. **Connect your Git repository** to ArgoCD.
3. **Deploy MongoDB**, either as a standalone service or a sharded cluster, in a separate namespace.
4. **Configure and deploy the backend application** using ArgoCD.
5. **Deploy the frontend application** after the backend is fully deployed.

This setup ensures that your full-stack application is deployed in the correct order, following GitOps principles with ArgoCD and Kubernetes.


