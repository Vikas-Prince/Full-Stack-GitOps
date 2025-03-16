# Full Stack Application Deployment with Kustomize, GitOps and ArgoCD

## Overview

This repository demonstrates a **GitOps-driven deployment** for both **frontend** and **backend** applications using **ArgoCD** as the continuous deployment tool. The repository provides a clean and scalable setup for deploying both applications in a Kubernetes environment while following **best practices** and **deployment strategies**.

The **GitOps principles** ensure that the entire deployment lifecycle, including updates and rollbacks, is automated, declarative, and version-controlled, all managed through ArgoCD.

## Key Concepts and Strategies

### ArgoCD Hub-Spoke Model for Multi-Environment Deployment

In this setup, we have used the **ArgoCD Hub-Spoke model** to manage multiple environments (staging and production). The **staging cluster** acts as the **Hub** and is responsible for deploying applications to both the **staging** and **production** clusters (the **Spokes**). This model centralizes deployment management in the staging environment, which simplifies and automates the deployment process across multiple clusters.

- **Staging Cluster (Hub)**: Responsible for managing and deploying the application to both staging and production environments.
- **Production Cluster (Spoke)**: Receives deployments managed by the staging cluster, ensuring minimal manual intervention and centralized control.

This model provides greater control over deployments and ensures a streamlined process for rolling out changes to both environments simultaneously.

### Key Benefits of This Approach

- **Declarative Deployment**: All deployment configurations and updates are managed as code, ensuring repeatability and version control.
- **Automated Rollbacks**: Using GitOps and ArgoCD, rolling back to a previous stable version can be easily done with minimal downtime.
- **Minimal Risk**: The Canary deployment strategy reduces the impact of introducing new versions, ensuring that any issues can be addressed quickly without affecting the majority of users.
- **Centralized Control**: The Hub-Spoke model ensures that the staging cluster manages deployments to both the staging and production environments, minimizing complexity and human error.

### GitOps with ArgoCD

ArgoCD is used for managing the deployment of both the frontend and backend applications. ArgoCD continuously syncs the Git repository with the Kubernetes cluster, automatically deploying changes as they are pushed to the repository.

### Key Components of the Repo

1. **Frontend Application**:

   - **Deployment Strategy**: Blue-Green Deployment
   - **Kubernetes Resources**: Deployment, Service, Ingress, HPA
   - **ArgoCD Application**: Automatically manages deployment in **staging** and **production** environments.

2. **Backend Application**:
   - **Deployment Strategy**: Canary Deployment
   - **Kubernetes Resources**: Deployment, Service, Ingress, HPA, Secret
   - **ArgoCD Application**: Automatically manages deployment in  **staging**, and **production** environments.

## Installation and Setup

### ArgoCD Setup with Hub-Spoke Model

To set up ArgoCD using the Hub-Spoke model for your environment, please refer to the detailed guide provided below:

**Install ArgoCD**: Follow the [ArgoCD installation guide](https://argo-cd.readthedocs.io/en/stable/getting_started/) to install ArgoCD on your Kubernetes cluster.

For a full walkthrough of setting up ArgoCD with the Hub-Spoke model, refer to the detailed setup instructions linked below:

- **[ArgoCD Hub-Spoke Model Setup Guide](https://github.com/Vikas-Prince/Full-Stack-Infra-Setup/blob/main/ArgoCD_config/installation.md)**

This guide covers everything, including environment-specific configurations and how to manage your deployments using GitOps with ArgoCD.


### Setup GitOps and ArgoCD

1. **Link the repository** to your ArgoCD instance. Create ArgoCD applications for both the frontend and backend.
2. For each application, set the **sync policy** to auto-sync so that ArgoCD automatically deploys any changes made to the repository.
3. Make sure the appropriate environment is linked (dev, staging, prod).

## Deploy the Backend and Frontend

### Deployment Flow

1. **For Backend**:

   - **Canary Deployment** helps you gradually roll out updates by directing a small percentage of traffic to the new version (green). This provides the ability to monitor and validate the new version without affecting the entire user base.
   - ArgoCD will automatically sync any changes from the Git repository to the backend Kubernetes deployments.

2. **For Frontend**:
   - **Blue-Green Deployment** ensures that users always have access to a stable version of the frontend. You deploy the new version in the green environment, validate it, and then switch traffic to the new version.
   - The frontend's Kubernetes resources are also managed by ArgoCD for seamless deployments.

## How GitOps Works in This Repo

The **GitOps** model ensures that any change made to the Git repository (whether it's code or configuration) is automatically deployed to your Kubernetes cluster through **ArgoCD**. This method reduces human intervention, providing faster, more reliable deployments.

- **Kustomize** (in the `base/` and `overlays/` directories) is used for resource management, allowing you to create customized Kubernetes resources for different environments (such as dev, staging, and production) without duplicating the base configuration. 
- **ArgoCD** monitors the Git repository, detects changes, and applies them to the Kubernetes cluster using the customized resources from Kustomize.


## Conclusion

This repository demonstrates a robust **GitOps** approach for deploying **frontend** and **backend** applications using **ArgoCD**. The **Hub-Spoke model** allows centralized management of deployments across multiple environments, with the **staging cluster** acting as the Hub, and the **production cluster** as the Spoke.

The use of **Canary Deployment** for the backend and **Blue-Green Deployment** for the frontend ensures minimal downtime and risk during rollouts. With **GitOps**, deployments are automated, version-controlled, and declarative, reducing manual intervention and ensuring consistency.

This setup provides a streamlined, scalable, and reliable method for managing application deployments across multiple environments, making it ideal for modern cloud-native applications.
