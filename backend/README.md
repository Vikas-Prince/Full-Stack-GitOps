# Backend Deployment using Canary Model with ArgoCD

This project demonstrates how to deploy a **Backend Application** using a **Canary Deployment Strategy** in Kubernetes, managed with **ArgoCD** and following **GitOps** principles.

## Project Structure

The repository follows the **Kustomize** and **ArgoCD** methodologies for managing Kubernetes configurations. The directory structure is organized as follows:

```bash
my-app/
├── base/                       # Common resources for all environments (stable version by default)
│   ├── deployment.yaml         # Default stable deployment file (no versioning here)
│   ├── service.yaml            # Default stable service file
│   ├── ingress.yaml            # Default stable ingress file
│   ├── hpa.yaml                # Default Horizontal Pod Autoscaler
│   ├── secret.yaml             # Default secret (ConfigMap, etc.)
│   └── kustomization.yaml      # Base kustomization file (points to the base resources)
│
├── overlays/                   # Environment-specific overlays (staging, prod)
│   ├── staging/                # Staging environment
│   │   ├── canary/             # Canary environment for staging
│   │   │   ├── kustomization.yaml  # Canary-specific kustomization.yaml for staging
│   │   │   ├── deployment-patch.yaml  # Canary-specific deployment for staging
│   │   │   ├── ingress-patch.yaml     # Canary-specific ingress patch for staging
│   │   └── kustomization.yaml  # Default stable version for staging (points to base resources)
│   │
│   └── prod/                   # Prod environment
│       ├── canary/             # Canary environment for prod
│       │   ├── kustomization.yaml  # Canary-specific kustomization.yaml for prod
│       │   ├── deployment-patch.yaml  # Canary-specific deployment for prod
│       │   ├── ingress-patch.yaml     # Canary-specific ingress patch for prod
│       └── kustomization.yaml  # Default stable version for prod (points to base resources)
│
├── argo-cd/                    # ArgoCD configurations for staging, prod
│   └── applications/
│       ├── staging-app.yaml    # ArgoCD application configuration for staging
│       └── prod-app.yaml       # ArgoCD application configuration for prod
└── README.md                   # Documentation of your deployment process

```

## Steps to Deploy

### 1. **Base Resources Setup**
In the `base/` directory, we have common resources used by all environments (default stable version):
- **`deployment.yaml`**: The default stable deployment configuration.
- **`service.yaml`**: Defines the Kubernetes service for the application.
- **`ingress.yaml`**: The ingress configuration to route traffic to the backend service.
- **`hpa.yaml`**: The Horizontal Pod Autoscaler that scales the application based on resource utilization (e.g., CPU usage).
- **`secret.yaml`**: The secret (or ConfigMap) used by the application, such as a MongoDB URL.
- **`kustomization.yaml`**: Base kustomization file that references all the resources mentioned above.

### 2. **Environment-Specific Overlays**
The `overlays/` directory contains environment-specific configurations (e.g., **staging** and **production**). Each environment may have its own **canary** deployment and can apply patches on top of the base resources.

#### Staging Environment:
- **`staging/canary/`**: The canary configuration for the staging environment.
  - **`kustomization.yaml`**: Defines which resources to apply for canary deployment in staging.
  - **`deployment-patch.yaml`**: Patches the base deployment to use the canary version.
  - **`ingress-patch.yaml`**: Modifies ingress to route a portion of traffic to the canary version.

- **`staging/kustomization.yaml`**: Default stable version for staging (points to the base resources).

#### Production Environment:
- **`prod/canary/`**: The canary configuration for the production environment.
  - **`kustomization.yaml`**: Defines which resources to apply for canary deployment in production.
  - **`deployment-patch.yaml`**: Patches the base deployment to use the canary version.
  - **`ingress-patch.yaml`**: Modifies ingress to route a portion of traffic to the canary version.

- **`prod/kustomization.yaml`**: Default stable version for production (points to the base resources).

### 3. **Kustomize Patches**
The Canary deployment applies the following patches for each environment:

- **`deployment-patch.yaml`**: Updates the deployment to use the canary image version and can include other necessary modifications such as resource limits.
- **`ingress-patch.yaml`**: Modifies the ingress to direct a specific percentage of traffic to the canary version. It uses the following annotations to enable the Canary traffic split:
  - **`nginx.ingress.kubernetes.io/canary: "true"`**: Marks this ingress as part of the Canary deployment.
  - **`nginx.ingress.kubernetes.io/canary-weight: "10"`**: Defines the traffic weight split between stable and canary (e.g., 10% traffic goes to the canary version).

### 4. **ArgoCD Application Configuration**
The **ArgoCD** application configurations are set up for staging and production environments:

- **`staging-app.yaml`**: Defines the ArgoCD application configuration for staging.
- **`prod-app.yaml`**: Defines the ArgoCD application configuration for production.

ArgoCD will automatically deploy and synchronize the application to the Kubernetes clusters based on these configurations.

### 5. **Canary Traffic Split**
The **Canary Deployment** traffic split is managed by the ingress configuration:
- **`nginx.ingress.kubernetes.io/canary: "true"`**: Marks the route as part of the canary deployment.
- **`nginx.ingress.kubernetes.io/canary-weight: "10"`**: Specifies the percentage of traffic (e.g., 10%) routed to the canary version of the application, while the rest (90%) is routed to the stable version.

### 6. **Scaling and Auto-scaling**
A **Horizontal Pod Autoscaler (HPA)** is defined for the backend application in the `base/hpa.yaml` file. The HPA scales the application pods based on the CPU usage (targeting 80% CPU utilization). This ensures that the application can handle increased load during deployment.

### 7. **Rollout Process**
- **Initial Stable Deployment**: The base resources in the `base/` directory are deployed first, which represents the stable version of the application.
- **Canary Deployment**: When deploying a new version, the canary environment's configuration is updated to point to the new version. A small percentage of traffic (e.g., 10%) is routed to the canary version while the rest continues to use the stable version.
- **Gradual Rollout**: If the canary deployment is successful, the percentage of traffic routed to the canary version is gradually increased.
- **Full Rollout**: Once the canary version is fully validated, all traffic is routed to the new version and the previous version is deprecated.
- **Rollback (If Needed)**: If issues arise during the canary deployment, traffic can be shifted back to the stable version or the canary version can be rolled back entirely.

### 8. **ArgoCD Sync**
ArgoCD automatically syncs the Kubernetes cluster with the desired state defined in the Git repository. The application will be deployed and updated according to the configurations in Git. Any changes made to the repository will trigger an automatic sync in ArgoCD.

### 9. **Applying the Configuration Manually (Optional)**
If you prefer to apply the configuration manually, you can use the following `kubectl` commands:

```bash
# Apply base resources (stable version)
kubectl apply -k base/

# Apply the staging environment with canary
kubectl apply -k overlays/staging/

# Apply the prod environment with canary
kubectl apply -k overlays/prod/

```

Alternatively, once ArgoCD is set up, it will automatically manage the deployment process.

This process provides a clear and structured deployment flow using the Canary Deployment Strategy and GitOps principles for managing backend application updates with minimal risk.
