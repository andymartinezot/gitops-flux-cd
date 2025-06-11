# GitOps with Flux CD

This repository demonstrates a GitOps implementation using Flux CD for managing Kubernetes deployments. It provides a practical example of how to implement GitOps practices using Flux CD as the continuous delivery tool.

## Repository Overview

This repository follows GitOps principles where:
- All infrastructure and application configurations are stored in Git
- Flux CD automatically syncs the cluster state with the desired state in Git
- Changes to the cluster are made through Git commits
- The deployment process is automated and declarative

## Repository Structure

```
.
├── apps/                    # Application definitions
│   └── hello-app/          # Sample application
│       ├── base/           # Base Kubernetes manifests
│       └── overlays/       # Environment-specific configurations
│           ├── int/        # Integration environment configuration
│           ├── stg/        # Staging environment configuration
│           └── prd/        # Production environment configuration
└── clusters/               # Cluster-specific configurations
    └── my-cluster/        # Configuration for a specific cluster
        ├── int-hello-app.yaml  # Integration deployment configuration
        ├── stg-hello-app.yaml  # Staging deployment configuration
        └── prd-hello-app.yaml  # Production deployment configuration
```

## Components

### Applications (`apps/`)
- Contains the application definitions and their Kubernetes manifests
- Each application has a `base` directory with common configurations
- `overlays` directory contains environment-specific customizations

### Clusters (`clusters/`)
- Contains cluster-specific configurations
- Defines how applications are deployed to different environments
- Manages Flux CD Kustomization resources

## Current Configuration

The repository is configured to deploy a hello-app to three environments with the following settings:
- Deployment interval: 1 minute
- Target namespaces: 
  - `int` (Integration)
  - `stg` (Staging)
  - `prd` (Production)
- Deployment timeout: 2 minutes
- Automatic pruning of removed resources
- Git-based source of truth

## Prerequisites

- Kubernetes cluster
- Flux CD installed in the cluster
- Git repository access
- kubectl configured to access the cluster

## Getting Started

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd gitops-flux-cd
   ```

2. **Bootstrap Flux CD** (if not already done)
   ```bash
   flux bootstrap github \
     --owner=<your-github-username> \
     --repository=gitops-flux-cd \
     --branch=main \
     --path=./clusters/my-cluster
   ```

3. **Verify the deployment**
   ```bash
   flux get kustomizations
   kubectl get pods -n prd
   ```

## Making Changes

1. Make changes to the application manifests in the `apps/` directory
2. Commit and push changes to Git
3. Flux CD will automatically detect changes and apply them to the cluster

## Best Practices

- Always make changes through Git commits
- Use meaningful commit messages
- Test changes in a non-production environment first
- Keep the repository structure clean and organized
- Document any significant changes

## Troubleshooting

If you encounter issues:
1. Check Flux CD logs:
   ```bash
   flux get kustomizations
   flux logs
   ```
2. Verify cluster connectivity
3. Check application logs in the target namespace

## Accessing Services on Mac

When running on macOS with Minikube, the cluster IP might not be directly reachable. To access the services, use the following commands:

### Accessing the Hello App Service

To access the hello-app service in different environments:

```bash
# For integration environment
minikube service hello-app --url -n int
# This will output something like: http://127.0.0.1:XXXXX
# Use this URL to access the hello-app service in your browser

# For staging environment
minikube service hello-app --url -n stg
# This will output something like: http://127.0.0.1:XXXXX
# Use this URL to access the hello-app service in your browser

# For production environment
minikube service hello-app --url -n prd
# This will output something like: http://127.0.0.1:XXXXX
# Use this URL to access the hello-app service in your browser
```

### Accessing Flux CD UI (if enabled)

If you have the Flux CD UI enabled:

```bash
# Get the Flux CD UI URL
minikube service flux-webui --url -n flux-system
# This will output something like: http://127.0.0.1:XXXXX
# Use this URL to access the Flux CD UI in your browser
```

Note: Make sure Minikube is running before executing these commands. If you get a "service not found" error, verify that:
1. The service exists in the specified namespace
2. The service is of type LoadBalancer or NodePort
3. The pods are running correctly

### Environment-Specific Notes

- **Integration (int)**: Used for development and integration testing
- **Staging (stg)**: Used for pre-production testing and validation
- **Production (prd)**: Used for live production workloads

Each environment has its own namespace and configuration, allowing for isolated testing and deployment.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request