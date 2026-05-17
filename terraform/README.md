# Terraform - Infrastructure as Code

This directory contains reusable Terraform modules and configurations for AWS infrastructure provisioning.

## 📂 Structure

```
terraform/
├── modules/
│   ├── vpc/
│   ├── eks/
│   ├── rds/
│   ├── s3/
│   └── iam/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
└── README.md
```

## 🎯 Key Features

### VPC Module
- Multi-AZ VPC setup
- Public and private subnets
- NAT Gateway for private subnet internet access
- VPC Flow Logs for security monitoring
- IPv4 and IPv6 support

### EKS Module
- Managed Kubernetes cluster
- Worker node groups with auto-scaling
- RBAC configuration
- Add-on management (VPC CNI, CoreDNS, kube-proxy)
- Security group configuration

### RDS Module
- Multi-AZ database instances
- Automated backups
- Performance Insights
- Parameter group customization
- Security group management

### S3 Module
- Bucket creation with versioning
- Encryption at rest (SSE-S3 or KMS)
- Block public access settings
- Lifecycle policies
- Access logging

### IAM Module
- Role creation
- Policy management
- Service account configuration
- Trust relationships

## 🚀 Usage Example

```hcl
module "vpc" {
  source = "./modules/vpc"
  
  vpc_cidr           = "10.0.0.0/16"
  environment        = "prod"
  availability_zones = ["us-east-1a", "us-east-1b", "us-east-1c"]
}

module "eks" {
  source = "./modules/eks"
  
  cluster_name    = "my-cluster"
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.private_subnet_ids
  environment     = "prod"
  node_group_size = 3
}
```

## ✅ Best Practices Implemented

✅ **Modularity**: Reusable, composable modules  
✅ **State Management**: Remote state with locking  
✅ **Variables**: Environment-specific configurations  
✅ **Outputs**: Clear output definitions  
✅ **Documentation**: Inline comments and README files  
✅ **Security**: Principle of least privilege, encryption enabled  
✅ **Tagging**: Consistent resource tagging strategy  
✅ **Versioning**: Explicit provider versions  

## 🔒 Security Considerations

- All resources are encrypted by default
- IAM roles follow principle of least privilege
- VPC security groups restrict traffic appropriately
- S3 buckets have block public access enabled
- RDS instances are in private subnets
- Backup and disaster recovery enabled

## 📖 Getting Started

1. Install Terraform (v1.0+)
2. Configure AWS credentials
3. Initialize Terraform: `terraform init`
4. Create workspace: `terraform workspace new prod`
5. Plan deployment: `terraform plan`
6. Apply configuration: `terraform apply`

## 🔄 State Management

Use S3 backend for remote state:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

## 📚 References

- [Terraform AWS Provider Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [AWS Best Practices](https://docs.aws.amazon.com/)
- [Terraform Best Practices](https://www.terraform.io/docs/cloud/guides/recommended-practices.html)

---

**Note**: Add your manifest files here. I'm ready to incorporate them!
