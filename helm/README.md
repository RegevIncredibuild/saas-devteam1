# Demo App Helm Chart

A basic demo application Helm chart designed for ArgoCD GitOps deployment.

## Description

This Helm chart deploys a simple nginx-based web application with the following features:

- **Deployment**: Configurable replica count with health checks
- **Service**: ClusterIP service for internal access
- **ServiceAccount**: Dedicated service account with configurable annotations
- **Ingress**: Optional ingress configuration for external access
- **HPA**: Optional horizontal pod autoscaling
- **Security**: Non-root container with read-only filesystem and security contexts

## Installation

### Via Helm CLI
```bash
helm install demo-app ./helm
```

### Via ArgoCD
Create an ArgoCD Application pointing to this repository:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/RegevIncredibuild/saas-devteam1.git
    targetRevision: HEAD
    path: helm
  destination:
    server: https://kubernetes.default.svc
    namespace: demo-app-ns
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

## Configuration

The following table lists the configurable parameters and their default values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `image.repository` | Container image repository | `nginx` |
| `image.tag` | Container image tag | `1.25-alpine` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `ingress.enabled` | Enable ingress | `false` |
| `resources.limits.cpu` | CPU limit | `100m` |
| `resources.limits.memory` | Memory limit | `128Mi` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `app.name` | Application name | `demo-app` |
| `app.version` | Application version | `1.0.0` |
| `app.environment` | Environment name | `development` |

## Values Override

Create a custom values file for different environments:

```yaml
# values-production.yaml
replicaCount: 3
app:
  environment: production
resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

## Testing

Test the chart template rendering:
```bash
helm template demo-app ./helm
```

Validate the chart:
```bash
helm lint ./helm
```

## ArgoCD Integration

This chart is designed to work seamlessly with ArgoCD:

1. **GitOps Ready**: All configurations are declarative and version-controlled
2. **Namespace Management**: Supports automatic namespace creation
3. **Health Checks**: Includes proper liveness and readiness probes
4. **Security**: Follows Kubernetes security best practices
5. **Observability**: Structured logging and monitoring-ready

## Monitoring

The application exposes metrics and logs that can be collected by:
- Prometheus (via service monitors)
- Grafana (for visualization)
- ELK/EFK stack (for log aggregation)

## Security Features

- Non-root user execution
- Read-only root filesystem
- Security contexts applied
- Resource limits enforced
- Network policies ready (add as needed)

## Troubleshooting

Check pod status:
```bash
kubectl get pods -l app.kubernetes.io/name=demo-app
```

View logs:
```bash
kubectl logs -l app.kubernetes.io/name=demo-app -f
```

Describe deployment:
```bash
kubectl describe deployment demo-app
```
