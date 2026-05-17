# Kubernetes - Container Orchestration

This directory contains Kubernetes manifests, Helm charts, and ArgoCD configurations for EKS deployments.

## 📂 Structure

```
kubernetes/
├── manifests/
│   ├── deployments/
│   ├── services/
│   ├── statefulsets/
│   ├── configmaps/
│   └── secrets/
├── helm-charts/
├── rbac/
├── argocd/
├── networking/
└── README.md
```

## 🎯 Key Components

### Manifests
- **Deployments**: Stateless application deployments with rolling updates
- **Services**: ClusterIP, NodePort, LoadBalancer configurations
- **StatefulSets**: Stateful workloads with persistent storage
- **ConfigMaps**: Configuration management
- **Secrets**: Secure sensitive data storage
- **Ingress**: External access and routing

### RBAC (Role-Based Access Control)
- ClusterRoles and Roles for permissions
- RoleBindings and ClusterRoleBindings
- Service accounts for pod authentication
- Namespace-level access control

### Network Policies
- Ingress and egress rules
- Pod-to-pod communication
- Network segmentation

### ArgoCD GitOps
- Application definitions
- ApplicationSets for cluster add-ons
- Automated sync policies
- Health monitoring

## 🚀 Example Deployment

### Basic Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      serviceAccountName: my-app
      containers:
      - name: my-app
        image: my-registry.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

## 🔒 Security Best Practices

✅ **RBAC**: Least privilege access control  
✅ **Network Policies**: Restrict pod-to-pod communication  
✅ **Resource Limits**: CPU and memory constraints  
✅ **Health Checks**: Liveness and readiness probes  
✅ **Secrets**: Use Kubernetes Secrets for sensitive data  
✅ **Security Context**: Run as non-root user  
✅ **Pod Security Policy**: Enforce security standards  

## 📖 Getting Started

1. Configure kubectl: `kubectl config use-context my-cluster`
2. Create namespace: `kubectl create namespace production`
3. Apply manifests: `kubectl apply -f manifests/`
4. Check status: `kubectl get pods -n production`

## 📚 References

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [EKS Documentation](https://docs.aws.amazon.com/eks/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Helm Documentation](https://helm.sh/docs/)

---

**Note**: Add your Kubernetes manifests here. I'm ready to incorporate them!
