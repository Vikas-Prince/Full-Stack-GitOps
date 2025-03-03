# Frontend Deployment using Blue-Green Deployment Strategy with GitOps and ArgoCD

## Overview

This repository contains the manifest files and configurations for deploying the frontend application using the **Blue-Green Deployment** strategy in combination with **GitOps** principles and **ArgoCD**. 

### Deployment Strategy

We use a **Blue-Green Deployment** strategy where:
- **Blue environment** represents the stable, currently active version of the frontend app.
- **Green environment** represents the new version of the frontend app that is being deployed.

With **GitOps**, we manage and deploy the application through version-controlled Git repositories, ensuring continuous deployment and automated rollbacks with minimal manual intervention.

### Key Benefits of Blue-Green Deployment:
- **Zero-downtime deployments**: By maintaining two environments (blue and green), we ensure that the application is always available.
- **Easy rollback**: In case of issues with the new version, switching back to the blue environment is straightforward.
- **Reduced risk**: Since the green environment is fully tested and ready to go before routing traffic to it, the risk is minimized.

## Directory Structure

```bash
my-app/
├── base/                       # Common resources for all environments (deployment, service, ingress, etc.)
│   ├── deployment.yaml         # Base deployment file
│   ├── service.yaml            # Base service file
│   ├── ingress.yaml            # Base ingress file
│   ├── hpa.yaml                # Horizontal Pod Autoscaler for scaling
│   └── kustomization.yaml      # Base kustomization file for common resources
├── overlays/                   # Environment-specific overlays (staging, prod)
│   ├── staging/                # Staging environment
│   │   ├── blue/               # Blue environment (current stable)
│   │   │   ├── kustomization.yaml  # Blue-specific kustomization for staging
│   │   │   ├── deployment-patch.yaml  # Blue-specific deployment for staging
│   │   │   ├── service-patch.yaml     # Blue-specific service patch for staging
│   │   │   └── ingress-patch.yaml     # Blue-specific ingress patch for staging
│   │   ├── green/              # Green environment (new version)
│   │   │   ├── kustomization.yaml  # Green-specific kustomization for staging
│   │   │   ├── deployment-patch.yaml  # Green-specific deployment for staging
│   │   │   ├── service-patch.yaml     # Green-specific service patch for staging
│   │   │   └── ingress-patch.yaml     # Green-specific ingress patch for staging
│   └── prod/                   # Production environment
│       ├── blue/               # Blue environment (current stable)
│       │   ├── kustomization.yaml  # Blue-specific kustomization for prod
│       │   ├── deployment-patch.yaml  # Blue-specific deployment for prod
│       │   ├── service-patch.yaml     # Blue-specific service patch for prod
│       │   └── ingress-patch.yaml     # Blue-specific ingress patch for prod
│       ├── green/              # Green environment (new version)
│       │   ├── kustomization.yaml  # Green-specific kustomization for prod
│       │   ├── deployment-patch.yaml  # Green-specific deployment for prod
│       │   ├── service-patch.yaml     # Green-specific service patch for prod
│       │   └── ingress-patch.yaml     # Green-specific ingress patch for prod
└── argo-cd/                     # ArgoCD configurations for dev, staging, prod
    └── applications/
        ├── staging-app.yaml     # ArgoCD application configuration for staging
        └── prod-app.yaml        # ArgoCD application configuration for prod
```


## Key Components in the Repository

1. **Base Directory (`base/`)**
   - Contains the **default common resources** that are shared across all environments (such as **deployment**, **service**, **ingress**, **HPA**, and **kustomization** files).
   - The **`kustomization.yaml`** file in the base directory brings these resources together to form the base structure for deployments.
   - This directory is the foundation for the environments defined in **overlays**.

2. **Overlays Directory (`overlays/`)**
   - Contains **environment-specific overlays** for **staging** and **prod**.
   - Each environment has its own **blue** (current stable) and **green** (new version) subdirectories, with specific patches and kustomizations to differentiate them.
   - The **`kustomization.yaml`** in each of the **blue** and **green** directories applies patches to customize the base resources for the specific environment and version.

3. **ArgoCD Configurations (`argo-cd/`)**
   - Contains the **ArgoCD** application manifests for **staging** and **prod** environments.
   - These manifests are used to automatically deploy and manage the application using ArgoCD.
   - ArgoCD syncs the Git repository to the Kubernetes cluster and deploys the appropriate environment’s configuration.

## Deployment Strategy

### Blue-Green Deployment:
- **Blue Environment**: The **blue** folder represents the stable, current version of the application. This is the environment that is serving production traffic.
- **Green Environment**: The **green** folder represents the new version of the application. Once the green version is deployed and verified, traffic is switched from the blue environment to the green environment.

### Horizontal Pod Autoscaler (HPA):
- The **HPA** (defined in `base/hpa.yaml`) automatically scales the application’s pods based on CPU or memory usage. It ensures that the application can handle varying traffic loads by adjusting the number of replicas.

### ArgoCD Continuous Deployment:
- **ArgoCD** continuously monitors the Git repository for changes and deploys the latest configurations automatically.
- For each environment (**staging** and **prod**), ArgoCD is configured to sync the Git repository with the Kubernetes cluster, deploying the appropriate environment’s configuration.


## Steps to Deploy:

1. Apply the ArgoCD Application Manifests for **staging** and **prod** environments:
   - This step links ArgoCD to the Git repository and configures the automatic deployment of the application in the respective environments.

2. Deploy the Blue and Green Environments:
   - Deploy the blue (stable) and green (new version) environments for both staging and production.
     - For **staging**, apply the blue environment and then apply the green environment.
     - For **prod**, apply the blue environment and then apply the green environment.

3. Switch Traffic to the Green Environment:
   - Once the green version is deployed and validated, traffic is switched from the blue environment to the green environment using either manual ingress updates or via ArgoCD's sync features.

4. Scale the Application:
   - The **Horizontal Pod Autoscaler** (HPA) ensures that the application is automatically scaled based on resource usage, as defined in the base HPA configuration file.
   - Check the HPA status to confirm that the application is scaling correctly.

5. Rollback (if needed):
   - If any issues arise with the green version, you can simply rollback to the blue version by reverting the changes in the Git repository and letting ArgoCD automatically redeploy the stable blue environment.

---

## Conclusion

This setup demonstrates how to use the **Blue-Green Deployment** strategy in conjunction with **GitOps** and **ArgoCD** for continuous deployment and management of frontend applications. By utilizing this strategy, you ensure **zero-downtime updates**, **easy rollback capabilities**, and **automatic scaling** of your application. With ArgoCD managing the deployment process, this setup ensures smooth and efficient management of application versions with minimal manual intervention.