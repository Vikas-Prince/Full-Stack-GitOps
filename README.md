# Full Stack Application Deployment with GitOps and ArgoCD

## Overview

This repository demonstrates a **GitOps-driven deployment** for both **frontend** and **backend** applications using **ArgoCD** as the continuous deployment tool. The repository provides a clean and scalable setup for deploying both applications in a Kubernetes environment while following **best practices** and **deployment strategies**.

The **GitOps principles** ensure that the entire deployment lifecycle, including updates and rollbacks, is automated, declarative, and version-controlled, all managed through ArgoCD.

## Key Concepts and Strategies

### Backend Deployment: **Canary Deployment Strategy**

For the **backend application**, we have implemented a **Canary Deployment strategy**. This allows us to:

- Gradually release the new version of the backend service.
- Route a small percentage of traffic to the new version while the stable version (canary) continues serving the majority of users.
- Ensure minimal risk while deploying new versions, and making the rollback easier if issues arise in production.

In this setup:

- The **stable (v1.1)** version of the backend application remains active.
- A small portion of traffic is routed to the **new (v1.2)** version.
- If the new version proves stable, traffic is fully shifted to the v1.2 version, and the old v1.1 can be scaled down or removed.

### Frontend Deployment: **Blue-Green Deployment Strategy**

For the **frontend application**, a **Blue-Green Deployment strategy** is used. This strategy ensures **zero-downtime updates** for the frontend application. In this setup:

- The **blue** environment serves the current stable version of the frontend.
- The **green** environment serves the new version of the frontend.
- After the green version is deployed and validated, traffic is switched from blue to green.

This ensures that the users will always have access to a stable version of the frontend while the new version is tested and validated before it's fully promoted.

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

### Step 1: Install ArgoCD

To get started, you will need to install **ArgoCD** on your Kubernetes cluster. Follow the official ArgoCD installation documentation to get it up and running in your environment.

### Step 2: Configure the LoadBalancer

In the **service.yaml** files for both the frontend and backend, update the **ClusterIP** type services to **LoadBalancer** type, so that they can be accessed externally.

Once the service is created, you can use the external IP to access the applications. The LoadBalancer will route traffic to the appropriate pod based on the environment's current state.

### Step 3: Decrypt and View Secrets

Secrets are encrypted and stored in Kubernetes for both frontend and backend configurations. To view and decrypt them:

1. Retrieve the secret using the `kubectl get secrets` command.
2. Decrypt the secret using appropriate command.
3. Ensure that the sensitive data is securely handled during the decryption process.

### Step 4: Setup GitOps and ArgoCD

1. **Link the repository** to your ArgoCD instance. Create ArgoCD applications for both the frontend and backend.
2. For each application, set the **sync policy** to auto-sync so that ArgoCD automatically deploys any changes made to the repository.
3. Make sure the appropriate environment is linked (dev, staging, prod).

### Step 5: Deploy the Backend and Frontend

1. **Backend Deployment**:

   - The **Canary Deployment** strategy is used for backend. Initially, traffic is routed to the stable backend (blue) version. A small portion of traffic is routed to the new backend (green) version.
   - Once the green version proves stable, traffic is fully switched, and the stable version becomes green.

2. **Frontend Deployment**:
   - The **Blue-Green Deployment** strategy is applied to the frontend.
   - The blue environment is the stable, live version, and the green environment is the new version of the frontend.
   - Once validated, traffic is switched to the green environment, making it the live version, while the blue version is held as a backup.

## Deployment Flow

1. **For Backend**:

   - **Canary Deployment** helps you gradually roll out updates by directing a small percentage of traffic to the new version (green). This provides the ability to monitor and validate the new version without affecting the entire user base.
   - ArgoCD will automatically sync any changes from the Git repository to the backend Kubernetes deployments.

2. **For Frontend**:
   - **Blue-Green Deployment** ensures that users always have access to a stable version of the frontend. You deploy the new version in the green environment, validate it, and then switch traffic to the new version.
   - The frontend's Kubernetes resources are also managed by ArgoCD for seamless deployments.

## How GitOps Works in This Repo

The **GitOps** model ensures that any change made to the Git repository (whether it's code or configuration) is automatically deployed to your Kubernetes cluster through ArgoCD. This method reduces human intervention, providing faster, more reliable deployments.

- **Kubernetes Manifests** (in the `base/` and `overlays/` directories) describe the desired state of your applications.
- ArgoCD monitors the Git repository, detects changes, and applies them to the Kubernetes cluster.

## Summary

This repository serves as an example of how to implement **GitOps with ArgoCD** for managing **backend and frontend applications**. By using the **Canary Deployment strategy for backend** and the **Blue-Green Deployment strategy for frontend**, we ensure that new versions are released with minimal downtime and risk.

- **Backend**: Canary Deployment for controlled rollouts.
- **Frontend**: Blue-Green Deployment for zero-downtime updates.
- **GitOps & ArgoCD**: Automated, continuous deployment from Git to Kubernetes.

By following this setup, developers can ensure reliable, efficient, and automated application deployments while showcasing the power of GitOps principles in modern cloud-native environments.
