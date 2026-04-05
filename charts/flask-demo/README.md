# GitOps Repository

Kubernetes deployment manifests and Helm charts for the [flask-demo](https://github.com/pouyaarjomandi/devops-cicd-pipeline) application, managed via **ArgoCD**.

## How It Works

1. A commit to `develop`/`staging`/`main` in the [CI repo](https://github.com/pouyaarjomandi/devops-cicd-pipeline) triggers Jenkins.
2. Jenkins builds & pushes the Docker image to Nexus, then updates `image.tag` in the corresponding `apps/<env>/values.yaml`.
3. ArgoCD detects the change and automatically syncs the Kubernetes deployment.

## Repository Structure

```
gitops/
├── charts/
│   └── flask-demo/
│       ├── Chart.yaml              # Helm chart metadata
│       ├── values.yaml             # Default values (includes imagePullSecrets: [])
│       └── templates/
│           ├── deployment.yaml     # Deployment with imagePullSecrets support
│           ├── service.yaml        # ClusterIP Service
│           ├── ingress.yaml        # Ingress with ingressClassName: nginx
│           └── hpa.yaml            # HorizontalPodAutoscaler (conditional)
├── apps/
│   ├── dev/
│   │   └── values.yaml             # Development environment overrides
│   ├── staging/
│   │   └── values.yaml             # Staging environment overrides
│   └── prod/
│       └── values.yaml             # Production environment overrides
└── argocd/
    ├── dev/
    │   └── application.yaml        # ArgoCD Application for dev
    ├── staging/
    │   └── application.yaml        # ArgoCD Application for staging
    └── prod/
        └── application.yaml        # ArgoCD Application for production
```

## Environments

| Branch | Environment | Values File | Namespace | Ingress Host |
|--------|------------|-------------|-----------|--------------|
| `develop` | Dev | `apps/dev/values.yaml` | `dev` | `flask-demo.dev.local` |
| `staging` | Staging | `apps/staging/values.yaml` | `staging` | `flask-demo.staging.local` |
| `main` | Production | `apps/prod/values.yaml` | `prod` | `flask-demo.prod.local` |

## Helm Chart Resources

- **Deployment** — Flask app container with liveness/readiness probes, resource limits, and optional `imagePullSecrets`
- **Service** — ClusterIP on port 5000
- **Ingress** — Host-based routing with `ingressClassName: nginx` (conditionally enabled)
- **HPA** — Horizontal Pod Autoscaler (conditionally enabled)

## Setup ArgoCD Applications

```bash
kubectl apply -f argocd/dev/application.yaml
kubectl apply -f argocd/staging/application.yaml
kubectl apply -f argocd/prod/application.yaml
```

## Tools & Technologies

ArgoCD · Helm · Kubernetes · Docker · Nexus · Jenkins

## Author

**Pouya Arjmandiakram** — [GitHub](https://github.com/pouyaarjomandi) · [LinkedIn](https://www.linkedin.com/in/pouya-arjomandi/)