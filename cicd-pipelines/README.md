# CI/CD Pipelines - Automation & Deployment

This directory contains CI/CD pipeline examples using GitHub Actions, Jenkins, and ArgoCD.

## 📂 Structure

```
cicd-pipelines/
├── github-actions/
│   ├── build-push-ecr.yml
│   ├── deploy-lambda.yml
│   ├── terraform-plan.yml
│   └── security-scanning.yml
├── jenkins/
│   ├── Jenkinsfile
│   ├── shared-library/
│   └── pipeline-templates/
├── argocd/
│   ├── applications/
│   └── appsets/
└── README.md
```

## 🚀 GitHub Actions Workflows

### Build and Push to ECR

```yaml
name: Build & Push to ECR

on:
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - 'Dockerfile'

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        role-to-assume: arn:aws:iam::${{ secrets.AWS_ACCOUNT_ID }}:role/github-actions-role
        aws-region: us-east-1
    
    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1
    
    - name: Build, tag, and push image to ECR
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: my-app
        IMAGE_TAG: ${{ github.sha }}
      run: |
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker tag $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG $ECR_REGISTRY/$ECR_REPOSITORY:latest
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:latest
```

### Deploy Lambda Function

```yaml
name: Deploy Lambda

on:
  push:
    branches: [main]
    paths:
      - 'lambda/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-python@v4
      with:
        python-version: '3.11'
    
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        role-to-assume: arn:aws:iam::${{ secrets.AWS_ACCOUNT_ID }}:role/github-actions-lambda-role
        aws-region: us-east-1
    
    - name: Deploy to Lambda
      run: |
        aws lambda update-function-code \
          --function-name my-lambda-function \
          --zip-file fileb://lambda/lambda-deployment.zip
```

## 🔧 Jenkins Pipeline

### Declarative Pipeline Example

```groovy
pipeline {
    agent any
    
    options {
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'docker build -t my-app:${BUILD_NUMBER} .'
            }
        }
        
        stage('Test') {
            steps {
                sh 'docker run --rm my-app:${BUILD_NUMBER} npm test'
            }
        }
        
        stage('Push to ECR') {
            when { branch 'main' }
            steps {
                withAWS(credentials: 'aws-credentials') {
                    sh '''
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_REGISTRY
                        docker push $ECR_REGISTRY/my-app:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
}
```

## 📊 ArgoCD Applications

### Application Set for Cluster Add-ons

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: cluster-addons
  namespace: argocd
spec:
  generators:
  - list:
      elements:
      - name: metrics-server
        chart: metrics-server/metrics-server
        namespace: kube-system
      - name: nginx-ingress
        chart: ingress-nginx/ingress-nginx
        namespace: ingress-nginx
  template:
    metadata:
      name: '{{ name }}'
    spec:
      project: default
      source:
        repoURL: https://charts.helm.sh/stable
        chart: '{{ chart }}'
        targetRevision: "*"
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{ namespace }}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

## 📚 References

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)

---

**Note**: Add your CI/CD pipeline manifests here. I'm ready to incorporate them!
