# Shell Scripts - Automation Utilities

This directory contains useful shell scripts for AWS management, Kubernetes automation, Docker utilities, and system administration.

## 📂 Structure

```
shell-scripts/
├── aws/
│   ├── ec2-management.sh
│   ├── rds-backup.sh
│   └── s3-sync.sh
├── kubernetes/
│   ├── cluster-setup.sh
│   ├── pod-management.sh
│   └── health-check.sh
├── docker/
│   ├── build-push.sh
│   └── cleanup-images.sh
├── monitoring/
│   └── health-monitoring.sh
└── README.md
```

## 🚀 AWS Management Scripts

### EC2 Instance Management

```bash
#!/bin/bash
set -e

REGION="us-east-1"

function list_instances() {
    echo "Listing EC2 instances in $REGION..."
    aws ec2 describe-instances \
        --region $REGION \
        --query 'Reservations[].Instances[].{ID:InstanceId,State:State.Name,Type:InstanceType}' \
        --output table
}

case "${1:-list}" in
    list) list_instances ;;
    *) echo "Usage: $0 [list]" ;;
esac
```

### RDS Backup Script

```bash
#!/bin/bash
set -e

DB_INSTANCE="my-database"
REGION="us-east-1"

function create_snapshot() {
    local snapshot_id="backup-$(date +%Y%m%d-%H%M%S)"
    echo "Creating RDS snapshot: $snapshot_id"
    aws rds create-db-snapshot \
        --db-instance-identifier $DB_INSTANCE \
        --db-snapshot-identifier $snapshot_id \
        --region $REGION
}

case "${1:-create}" in
    create) create_snapshot ;;
    *) echo "Usage: $0 [create]" ;;
esac
```

## 🐳 Kubernetes Automation Scripts

### Cluster Health Check

```bash
#!/bin/bash
set -e

echo "=== Kubernetes Cluster Health Check ==="
echo "Checking Node Status..."
kubectl get nodes -o wide
echo ""
echo "Checking Pod Status..."
kubectl get pods -A
echo ""
echo "=== Health check complete ==="
```

### Pod Management Script

```bash
#!/bin/bash
set -e

function get_pod_logs() {
    local pod_name=$1
    local namespace=${2:-default}
    echo "Getting logs for pod: $pod_name"
    kubectl logs -f $pod_name -n $namespace
}

case "${1:-logs}" in
    logs) get_pod_logs "$2" "$3" ;;
    *) echo "Usage: $0 [logs] [pod-name] [namespace]" ;;
esac
```

## 🐳 Docker Automation Scripts

### Build and Push to ECR

```bash
#!/bin/bash
set -e

AWS_ACCOUNT_ID="123456789012"
AWS_REGION="us-east-1"
ECR_REGISTRY="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

IMAGE_NAME="${1:-my-app}"
TAG="${2:-latest}"

echo "Building Docker image: $IMAGE_NAME:$TAG"
docker build -t $IMAGE_NAME:$TAG .

echo "Logging in to ECR..."
aws ecr get-login-password --region $AWS_REGION | \
    docker login --username AWS --password-stdin $ECR_REGISTRY

echo "Pushing image to ECR..."
docker tag $IMAGE_NAME:$TAG $ECR_REGISTRY/$IMAGE_NAME:$TAG
docker push $ECR_REGISTRY/$IMAGE_NAME:$TAG
```

### Cleanup Docker Images

```bash
#!/bin/bash
set -e

echo "Cleaning up Docker resources..."
docker container prune -f
docker image prune -f
docker network prune -f
docker volume prune -f

echo "Docker disk usage:"
docker system df
```

## ⚙️ Best Practices

✅ Use `set -e` to exit on errors  
✅ Use `set -o pipefail` for pipeline safety  
✅ Add error handling and validation  
✅ Include usage documentation  
✅ Use meaningful variable names  
✅ Add logging for debugging  
✅ Make scripts idempotent  

## 🔒 Security Considerations

✅ Validate input parameters  
✅ Use IAM roles instead of hardcoded credentials  
✅ Set appropriate permissions (chmod 750)  
✅ Avoid logging sensitive information  
✅ Use AWS profiles for authentication  

---

**Note**: Add your shell script manifests here. I'm ready to incorporate them!
