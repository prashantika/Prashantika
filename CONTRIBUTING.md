# Contributing to Prashantika DevOps Portfolio

Thank you for your interest in contributing to this portfolio! This document provides guidelines and instructions for contributing.

## 📋 Code of Conduct

This project and everyone participating in it is governed by a Code of Conduct. By participating, you are expected to uphold this code.

## 🚀 Getting Started

### Prerequisites
- Git
- AWS Account (for testing)
- Docker & Docker Compose (for local testing)
- Kubernetes cluster or kind/minikube for local testing
- Terraform
- GitHub account

### Fork and Clone

```bash
git clone https://github.com/your-username/Prashantika.git
cd Prashantika
```

## 📝 Contribution Guidelines

### Types of Contributions

1. **Bug Fixes**: Fix issues in existing code or configurations
2. **New Examples**: Add new infrastructure examples or use cases
3. **Documentation**: Improve README files and inline documentation
4. **Optimizations**: Improve existing code or configurations
5. **Security**: Report and fix security vulnerabilities

### Process

1. **Create a Branch**: `git checkout -b feature/your-feature-name`
2. **Make Changes**: Follow the coding standards below
3. **Test Locally**: Validate your changes work correctly
4. **Commit**: Write clear, descriptive commit messages
5. **Push**: `git push origin feature/your-feature-name`
6. **Create Pull Request**: Submit PR with detailed description

## 📖 Coding Standards

### Terraform
- Use consistent indentation (2 spaces)
- Include comments for complex logic
- Use descriptive variable and resource names
- Include variables.tf and outputs.tf files
- Validate with `terraform fmt` and `terraform validate`

### Shell Scripts
- Start with shebang: `#!/bin/bash`
- Use `set -e` for error handling
- Include usage documentation
- Use meaningful variable names
- Add comments for complex sections

### Kubernetes YAML
- Use 2-space indentation
- Include labels and annotations
- Specify resource limits
- Use namespaces appropriately

### GitHub Actions Workflows
- Use descriptive job names
- Include comments for complex steps
- Use environment variables for configuration
- Include error handling

## ✅ Pull Request Process

1. Update README files with any new information
2. Test your changes thoroughly
3. Follow commit message conventions:
   - Use imperative mood ("Add feature" not "Added feature")
   - Start with a capital letter
   - Keep first line under 50 characters
   - Reference issues if applicable
4. Ensure CI/CD passes
5. Request review from maintainers

## 🧪 Testing

### Test Terraform
```bash
cd terraform
terraform init
terraform fmt -check
terraform validate
```

### Test Shell Scripts
```bash
bash -n script.sh  # Check syntax
shellcheck script.sh  # Lint with ShellCheck
```

### Test Docker
```bash
docker build -t test:latest .
docker run --rm test:latest
```

## 📚 Documentation

When adding new examples:
1. Include clear README with description
2. Add comments in code files
3. Provide usage examples
4. Document prerequisites
5. Include troubleshooting section if applicable

## 🔒 Security

- **Never commit secrets or credentials**
- Use environment variables for sensitive data
- Follow AWS IAM best practices
- Validate inputs in scripts
- Use HTTPS for remote connections
- Report security issues to maintainer privately

## 🐛 Reporting Issues

If you find a bug:
1. Check existing issues to avoid duplicates
2. Include clear description of the issue
3. Provide steps to reproduce
4. Include expected vs actual behavior
5. Add relevant logs or screenshots
6. Mention your environment details

## 💬 Questions?

- Create a GitHub Issue for questions
- Check existing discussions
- Review documentation

## 📖 Additional Resources

- [Terraform Style Guide](https://www.terraform.io/docs/language/style)
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)

## 🙏 Thank You

Thank you for contributing to make this portfolio better!

---

**Last Updated**: May 2026
