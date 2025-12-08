---
layout: doc
title: DevOps & Infrastructure
---

# 🐳 DevOps & Infrastructure

Tài liệu về DevOps practices, containerization, CI/CD, và infrastructure management.

## 📚 Overview

DevOps kết hợp development và operations để tăng tốc delivery. Section này bao gồm:

- **Docker**: Containerization cho applications
- **CI/CD**: Automated testing và deployment
- **Kubernetes**: Container orchestration
- **Server**: Nginx, hosting, SSL/TLS

---

## 🗂️ Content Structure

### 🐳 Docker

Containerization platform phổ biến nhất.

**Topics:**
- [Dockerfile Best Practices](/devops/docker/dockerfile-best-practices) - Optimize images
- [Docker Compose](/devops/docker/docker-compose) - Multi-container apps
- [Multi-stage Builds](/devops/docker/multi-stage-builds) - Reduce image size
- [Docker Networking](/devops/docker/docker-networking) - Container communication

**Key Concepts:**
- Images vs Containers
- Layers & caching
- Volumes & bind mounts
- Networks (bridge, host, overlay)
- Docker registry (Docker Hub)
- Container lifecycle

**Common Use Cases:**
- Development environments
- Microservices deployment
- CI/CD pipelines
- Production hosting

---

### 🔄 CI/CD

Continuous Integration & Continuous Deployment.

**Topics:**
- [GitHub Actions](/devops/ci-cd/github-actions) - CI/CD trên GitHub
- [GitLab CI](/devops/ci-cd/gitlab-ci) - GitLab pipelines
- [Deployment Strategies](/devops/ci-cd/deployment-strategies) - Blue-green, canary, rolling

**Key Concepts:**

**CI (Continuous Integration):**
- Automated testing
- Code quality checks (linting, formatting)
- Build automation
- Pull request validation

**CD (Continuous Deployment):**
- Automated deployment
- Environment management (dev, staging, prod)
- Rollback strategies
- Release automation

**Pipeline Stages:**
```
Code Push → Lint → Test → Build → Deploy → Monitor
```

---

### ☸️ Kubernetes

Container orchestration platform.

**Topics:**
- [K8s Basics](/devops/kubernetes/k8s-basics) - Core concepts
- [Deployments](/devops/kubernetes/deployments) - Application deployment
- [Services](/devops/kubernetes/services) - Networking & load balancing

**Key Concepts:**
- Pods - Smallest deployable units
- Deployments - Declarative updates
- Services - Expose applications
- ConfigMaps & Secrets
- Namespaces - Resource isolation
- Ingress - HTTP routing

**Use Cases:**
- Large-scale applications
- Microservices architecture
- Auto-scaling workloads
- Multi-cloud deployments

---

### 🖥️ Server & Hosting

Web servers và hosting setup.

**Topics:**
- [Nginx Configuration](/devops/server/nginx-config) - Reverse proxy & static files
- [SSL/TLS Setup](/devops/server/ssl-tls-setup) - HTTPS configuration

**Key Concepts:**
- Reverse proxy
- Load balancing
- Static file serving
- HTTPS/SSL certificates (Let's Encrypt)
- Domain configuration
- Security headers

---

## 🎯 Learning Path

### 👶 Beginner (0-1 tháng)

```markdown
1. Docker Fundamentals
   - Install Docker Desktop
   - Basic commands (run, build, ps)
   - Dockerfile basics
   - Docker Hub

2. Docker Compose
   - Multi-container apps
   - Service definition
   - Volumes & networks

3. Basic Deployment
   - Deploy to VPS
   - Nginx basics
   - Domain setup
```

**Goal:** Containerize và deploy simple app

---

### 💪 Intermediate (1-3 tháng)

```markdown
1. Docker Advanced
   - Multi-stage builds
   - Image optimization
   - Docker networking
   - Security best practices

2. CI/CD Setup
   - GitHub Actions workflow
   - Automated testing
   - Build & deploy pipeline
   - Environment variables

3. Server Management
   - Nginx configuration
   - SSL/TLS setup
   - Monitoring basics
   - Log management
```

**Goal:** Production-ready deployment với CI/CD

---

### 🏆 Advanced (3+ tháng)

```markdown
1. Kubernetes
   - K8s architecture
   - Deployments & Services
   - Helm charts
   - Auto-scaling

2. Advanced CI/CD
   - Multi-stage pipelines
   - Deployment strategies
   - Testing strategies
   - Rollback mechanisms

3. Infrastructure as Code
   - Terraform basics
   - Configuration management

4. Monitoring & Observability
   - Prometheus + Grafana
   - Logging (ELK stack)
   - Alerting
```

**Goal:** DevOps engineer với K8s knowledge

---

## 🚀 Quick Start

### Docker Setup

**Install Docker:**
```bash
# macOS
brew install docker

# Linux (Ubuntu)
sudo apt-get update
sudo apt-get install docker.io

# Verify installation
docker --version
```

**Hello World:**
```bash
# Run first container
docker run hello-world

# Run Nginx
docker run -d -p 8080:80 nginx

# Check running containers
docker ps

# Stop container
docker stop <container-id>
```

---

### Create Dockerfile

**Basic Node.js App:**
```dockerfile
# Dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

**Build & Run:**
```bash
# Build image
docker build -t my-app .

# Run container
docker run -p 3000:3000 my-app
```

---

### Docker Compose Example

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

**Run:**
```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

---

### GitHub Actions Example

**.github/workflows/ci.yml:**
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to production
        run: echo "Deploying..."
```

---

## 🛠️ Essential Tools

### Docker Tools
- **Docker Desktop** - Local development
- **Portainer** - Container management UI
- **Docker Scout** - Security scanning
- **Dive** - Image layer inspection

### CI/CD Platforms
- **GitHub Actions** - Free for public repos
- **GitLab CI** - Built-in GitLab
- **CircleCI** - Cloud CI/CD
- **Jenkins** - Self-hosted

### Monitoring
- **Grafana** - Visualization
- **Prometheus** - Metrics collection
- **ELK Stack** - Logging (Elasticsearch, Logstash, Kibana)

---

## 📖 Recommended Topics

### For Docker

1. **Start here:** [Dockerfile Best Practices](/devops/docker/dockerfile-best-practices)
2. **Multi-container:** [Docker Compose](/devops/docker/docker-compose)
3. **Optimization:** [Multi-stage Builds](/devops/docker/multi-stage-builds)
4. **Networking:** [Docker Networking](/devops/docker/docker-networking)

### For CI/CD

1. **GitHub:** [GitHub Actions](/devops/ci-cd/github-actions)
2. **GitLab:** [GitLab CI](/devops/ci-cd/gitlab-ci)
3. **Strategies:** [Deployment Strategies](/devops/ci-cd/deployment-strategies)

### For Kubernetes

1. **Basics:** [K8s Basics](/devops/kubernetes/k8s-basics)
2. **Deploy:** [Deployments](/devops/kubernetes/deployments)
3. **Expose:** [Services](/devops/kubernetes/services)

### For Server

1. **Web Server:** [Nginx Configuration](/devops/server/nginx-config)
2. **HTTPS:** [SSL/TLS Setup](/devops/server/ssl-tls-setup)

---

## 💡 Best Practices

### Dockerfile Optimization

```dockerfile
# ✅ Good - Multi-stage build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/index.js"]

# ❌ Bad - Single stage, large image
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
```

### .dockerignore

```bash
# ✅ Good - Exclude unnecessary files
node_modules
npm-debug.log
.git
.env
.DS_Store
dist
coverage
```

### Environment Variables

```yaml
# ✅ Good - Use secrets
services:
  app:
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - JWT_SECRET=${JWT_SECRET}

# ❌ Bad - Hard-coded secrets
services:
  app:
    environment:
      - DATABASE_URL=postgresql://user:password123@db:5432/mydb
```

### CI/CD Security

```yaml
# ✅ Good - Use GitHub Secrets
- name: Deploy
  env:
    DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
  run: ./deploy.sh

# ❌ Bad - Expose secrets in logs
- name: Deploy
  run: echo "Token: my-secret-token" && ./deploy.sh
```

---

## 🔗 External Resources

### Documentation
- [Docker Docs](https://docs.docker.com/)
- [Kubernetes Docs](https://kubernetes.io/docs/)
- [GitHub Actions Docs](https://docs.github.com/actions)
- [Nginx Docs](https://nginx.org/en/docs/)

### Learning
- [DevOps Roadmap](https://roadmap.sh/devops)
- [Docker Curriculum](https://docker-curriculum.com/)
- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)

---

## 📊 DevOps Stack Example

```
Complete DevOps Stack:
├── Containerization: Docker + Docker Compose
├── Orchestration: Kubernetes (optional)
├── CI/CD: GitHub Actions / GitLab CI
├── Web Server: Nginx
├── SSL: Let's Encrypt
├── Monitoring: Grafana + Prometheus
├── Logging: ELK Stack / Loki
├── Hosting: AWS / DigitalOcean / VPS
└── IaC: Terraform (optional)
```

---

## 🎯 Next Steps

1. **New to DevOps?** → Start với [Docker Best Practices](/devops/docker/dockerfile-best-practices)
2. **Know Docker?** → Learn [Docker Compose](/devops/docker/docker-compose)
3. **Need CI/CD?** → [GitHub Actions](/devops/ci-cd/github-actions)
4. **Production ready?** → [Nginx Config](/devops/server/nginx-config) + [SSL/TLS](/devops/server/ssl-tls-setup)
5. **Scale up?** → [Kubernetes Basics](/devops/kubernetes/k8s-basics)

---

**Ship it! 🚢**

[← Backend](/backend/) | [Tools & Tips →](/tools-tips/)