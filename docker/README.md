# Docker - Container Management

This directory contains Docker examples demonstrating best practices for containerization and image optimization.

## 📂 Structure

```
docker/
├── examples/
│   ├── nodejs-app/
│   ├── python-app/
│   └── go-app/
├── base-images/
├── security/
│   └── scanning/
└── README.md
```

## 🎯 Best Practices

### Multi-Stage Builds
- Reduce image size
- Minimize attack surface
- Separate build and runtime environments

### Example Multi-Stage Dockerfile

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

# Stage 2: Runtime
FROM node:18-alpine

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package.json ./

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000', (r) => {if (r.statusCode !== 200) throw new Error(r.statusCode)})"

CMD ["node", "dist/index.js"]
```

## 🔒 Security Best Practices

✅ **Non-root User**: Run containers as non-root user  
✅ **Minimal Base Images**: Use alpine or distroless images  
✅ **No Secrets in Images**: Use environment variables or secrets management  
✅ **Health Checks**: Implement liveness probes  
✅ **Resource Limits**: Set memory and CPU limits  
✅ **Vulnerability Scanning**: Scan images for CVEs  
✅ **Image Signing**: Sign images for verification  

## 📦 Image Optimization

### Size Reduction Techniques
1. Use multi-stage builds
2. Use slim/alpine base images
3. Remove unnecessary files
4. Combine RUN commands
5. Use .dockerignore

### Example .dockerignore
```
.git
.gitignore
.env
.env.local
node_modules
.next
.cache
COVERAGE
.vscode
.idea
DOCKERFILE
*.md
```

## 🔍 Image Scanning

### Using Trivy (Amazon's open-source scanner)
```bash
# Install Trivy
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Scan image
trivy image my-image:latest

# Generate report
trivy image --format json my-image:latest > report.json
```

### Using AWS ECR
```bash
# Scan image in ECR
aws ecr start-image-scan \
  --repository-name my-image \
  --image-id imageTag=latest

# Get scan results
aws ecr describe-image-scan-findings \
  --repository-name my-image \
  --image-id imageTag=latest
```

## 🚀 Docker Build & Push

### Build Image
```bash
docker build -t my-app:1.0.0 .
docker tag my-app:1.0.0 my-app:latest
```

### Login to ECR
```bash
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  123456789012.dkr.ecr.us-east-1.amazonaws.com
```

### Push to ECR
```bash
docker tag my-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
```

## 📚 References

- [Docker Documentation](https://docs.docker.com/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Amazon ECR Documentation](https://docs.aws.amazon.com/ecr/)
- [Trivy Scanner](https://github.com/aquasecurity/trivy)

---

**Note**: Add your Dockerfile examples here. I'm ready to incorporate them!
