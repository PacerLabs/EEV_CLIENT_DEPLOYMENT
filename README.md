# EEV Client Deployment

A comprehensive deployment configuration for the EEV (Enterprise Environment Virtualization) platform, providing both Docker Compose and Kubernetes deployment options for a full-stack application ecosystem.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Services](#services)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Deployment Options](#deployment-options)
  - [Docker Compose](#docker-compose-deployment)
  - [Kubernetes](#kubernetes-deployment)
- [Configuration](#configuration)
- [Environment Variables](#environment-variables)
- [Ports and Endpoints](#ports-and-endpoints)
- [Health Checks](#health-checks)
- [Volumes and Persistence](#volumes-and-persistence)
- [Troubleshooting](#troubleshooting)
- [Maintenance](#maintenance)

## 🌟 Overview

EEV Client Deployment orchestrates a microservices-based architecture with the following components:

- **Backend API** (Java/Spring Boot)
- **AI/ML Service** (Python)
- **Frontend Application** (Next.js)
- **Database** (PostgreSQL)
- **Cache Layer** (Redis)

This repository provides production-ready deployment configurations with health checks, proper dependency management, and scalable infrastructure patterns.

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Sapphire Nexus                          │
│                  (Next.js Frontend - :3010)                 │
└────────────────────────┬────────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         │                               │
┌────────▼──────────┐          ┌────────▼──────────┐
│  Crimson Oracle   │          │  Emerald Engine   │
│ (Java Backend)    │          │ (Python AI/ML)    │
│     :8086         │          │     :8000         │
└────────┬──────────┘          └────────┬──────────┘
         │                               │
         │                               │
┌────────▼──────────┐          ┌────────▼──────────┐
│   Titan Vault     │          │   Nebula Cache    │
│  (PostgreSQL)     │          │     (Redis)       │
│     :5432         │          │     :6379         │
└───────────────────┘          └───────────────────┘
```

## 🚀 Services

### Titan Vault (PostgreSQL Database)

- **Image**: `postgres:15-alpine`
- **Port**: `5432`
- **Purpose**: Primary data persistence layer
- **Features**: Automated health checks, persistent volumes

### Crimson Oracle (Backend API)

- **Technology**: Java/Spring Boot
- **Port**: `8086`
- **Purpose**: Core business logic and API endpoints
- **Features**:
  - Flyway database migrations
  - JWT authentication
  - RESTful API with Swagger/OpenAPI documentation
  - Spring Data JPA with Hibernate

### Nebula Cache (Redis)

- **Image**: `redis:7-alpine`
- **Port**: `6379`
- **Purpose**: Caching and session management
- **Features**: Persistent data volumes, health monitoring

### Emerald Engine (AI/ML Service)

- **Technology**: Python
- **Port**: `8000`
- **Purpose**: AI and machine learning operations
- **Features**:
  - OpenAI integration
  - Gemini API support
  - Pinecone vector database integration
  - Email service (Resend API)

### Sapphire Nexus (Frontend)

- **Technology**: Next.js
- **Port**: `3010`
- **Purpose**: User interface and client application
- **Features**: Server-side rendering, API integration

## 📦 Prerequisites

### For Docker Compose Deployment:

- Docker Engine 20.10+
- Docker Compose 2.0+
- 4GB RAM minimum (8GB recommended)
- 10GB free disk space

### For Kubernetes Deployment:

- Kubernetes cluster 1.21+
- kubectl configured
- Kustomize (built into kubectl 1.14+)
- 8GB RAM minimum
- 20GB free disk space

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd EEV_CLIENT_DEPLOYMENT
```

### 2. Configure Environment Variables

```bash
cp .env.example .env
# Edit .env with your configuration
nano .env
```

### 3. Start with Docker Compose

```bash
docker-compose up -d
```

### 4. Verify Deployment

```bash
docker-compose ps
```

## 🐳 Docker Compose Deployment

### Start All Services

```bash
docker-compose up -d
```

### View Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f crimson-oracle
```

### Stop Services

```bash
docker-compose down
```

### Stop and Remove Volumes

```bash
docker-compose down -v
```

### Rebuild Images

```bash
docker-compose up -d --build
```

## ☸️ Kubernetes Deployment

### Deploy with Kustomize

```bash
# Apply all configurations
kubectl apply -k k8s/

# Verify deployment
kubectl get all -n eev

# Check pod status
kubectl get pods -n eev

# View logs
kubectl logs -n eev -l app=crimson-oracle -f
```

### Update Deployment

```bash
# Apply changes
kubectl apply -k k8s/

# Restart a deployment
kubectl rollout restart deployment/crimson-oracle -n eev
```

### Delete Deployment

```bash
kubectl delete -k k8s/
```

## ⚙️ Configuration

### Kubernetes Resources

The `k8s/` directory contains:

- `01-namespace.yml` - Namespace, ConfigMap, and Secrets
- `03-titan-vault.yml` - PostgreSQL deployment
- `04-nebula-cache.yml` - Redis deployment
- `05-crimson-oracle.yml` - Backend API deployment
- `06-emerald-engine.yml` - AI/ML service deployment
- `07-sapphire-nexus.yml` - Frontend deployment
- `kustomization.yml` - Kustomize configuration

### Service Dependencies

The services start in the following order to ensure proper initialization:

1. **Titan Vault** (PostgreSQL) - Must be healthy first
2. **Nebula Cache** (Redis) - Independent startup
3. **Crimson Oracle** (Backend) - Depends on Titan Vault
4. **Emerald Engine** (AI Service) - Depends on Nebula Cache
5. **Sapphire Nexus** (Frontend) - Depends on Crimson Oracle and Emerald Engine

## 🔐 Environment Variables

A comprehensive `.env.example` file is provided with all required and optional variables. The file is organized into logical sections:

### PART 1: Docker & Deployment Configuration

| Variable             | Default                                         | Purpose                      |
| -------------------- | ----------------------------------------------- | ---------------------------- |
| `DOCKER_REGISTRY`    | `docker.io`                                     | Docker registry URL          |
| `DOCKER_USERNAME`    | `your_docker_username`                          | Docker registry username     |
| `JAVA_IMAGE_NAME`    | `crimson-oracle`                                | Java backend image name      |
| `JAVA_IMAGE_TAG`     | `latest`                                        | Java backend image tag       |
| `PYTHON_IMAGE_NAME`  | `emerald-engine`                                | Python AI service image name |
| `PYTHON_IMAGE_TAG`   | `latest`                                        | Python AI service image tag  |
| `PYTHON_REPO_URL`    | `https://github.com/PacerLabs/Eev-Build-AI.git` | Python repository URL        |
| `PYTHON_REPO_BRANCH` | `main`                                          | Python repository branch     |
| `NEXTJS_IMAGE_NAME`  | `sapphire-nexus`                                | Next.js frontend image name  |
| `NEXTJS_IMAGE_TAG`   | `latest`                                        | Next.js frontend image tag   |

### PART 2: Database Configuration (Titan Vault - PostgreSQL)

| Variable            | Default                                     | Purpose                      | Required |
| ------------------- | ------------------------------------------- | ---------------------------- | -------- |
| `DATABASE_URL`      | `jdbc:postgresql://titan-vault:5432/eev_db` | PostgreSQL connection string | ✓        |
| `DATABASE_USERNAME` | `postgres`                                  | PostgreSQL username          | ✓        |
| `DATABASE_PASSWORD` | Complex hex/binary/special chars            | PostgreSQL password (preset) | ✓        |

### PART 3: Spring Boot / Java Configuration

| Variable                                       | Default                                   | Purpose                                |
| ---------------------------------------------- | ----------------------------------------- | -------------------------------------- |
| `SPRING_DATASOURCE_DRIVER_CLASS_NAME`          | `org.postgresql.Driver`                   | JDBC driver class                      |
| `SPRING_JPA_PROPERTIES_HIBERNATE_DIALECT`      | `org.hibernate.dialect.PostgreSQLDialect` | Hibernate dialect                      |
| `SPRING_JPA_HIBERNATE_DDL_AUTO`                | `validate`                                | DDL auto mode (validate/update/create) |
| `SPRING_JPA_SHOW_SQL`                          | `false`                                   | Log SQL statements                     |
| `SPRING_JPA_PROPERTIES_HIBERNATE_FORMAT_SQL`   | `true`                                    | Format SQL output                      |
| `SPRING_FLYWAY_ENABLED`                        | `true`                                    | Enable Flyway migrations               |
| `SPRING_FLYWAY_LOCATIONS`                      | `classpath:db/migration`                  | Migration scripts location             |
| `SPRING_FLYWAY_BASELINE_ON_MIGRATE`            | `false`                                   | Baseline on migrate                    |
| `SPRING_FLYWAY_VALIDATE_ON_MIGRATE`            | `true`                                    | Validate on migrate                    |
| `SPRING_MAIN_ALLOW_BEAN_DEFINITION_OVERRIDING` | `false`                                   | Allow bean overriding                  |
| `SPRINGDOC_DEFAULT_PRODUCES_MEDIA_TYPE`        | `application/json`                        | Default media type                     |

### PART 4: Security & Authentication

| Variable              | Default                  | Purpose                        | Required |
| --------------------- | ------------------------ | ------------------------------ | -------- |
| `JWT_SECRET_TOKEN`    | 512-bit hex key          | JWT signing secret (preset)    | ✓        |
| `JWT_EXPIRATION_TIME` | `3600000`                | JWT expiration in milliseconds | ✓        |
| `API_KEY`             | `your_api_key`           | General API key                | ✓        |
| `ENDPOINT_AUTH_KEY`   | `your_endpoint_auth_key` | Endpoint authentication key    | ✓        |

### PART 5: Backend Service Configuration (Crimson Oracle)

| Variable                   | Default                      | Purpose              |
| -------------------------- | ---------------------------- | -------------------- |
| `SERVER_PORT`              | `8086`                       | Backend service port |
| `SERVER_URL`               | `http://crimson-oracle:8086` | Backend service URL  |
| `LICENSE_SERVICE_BASE_URL` | `http://crimson-oracle:8086` | License service URL  |

### PART 6: Frontend Service Configuration (Sapphire Nexus)

| Variable                 | Default                        | Purpose              | Required |
| ------------------------ | ------------------------------ | -------------------- | -------- |
| `SERVICE_FQDN_APP`       | `http://sapphire-nexus:3010`   | Frontend FQDN        | ✓        |
| `SERVICE_URL_APP`        | `http://sapphire-nexus:3010`   | Frontend URL         | ✓        |
| `NEXT_PUBLIC_BASE_URI`   | `http://localhost:8000/api/v1` | Public API base URI  | ✓        |
| `NEXT_PUBLIC_SERVER_URI` | `http://localhost:8086/api/v1` | Public server URI    | ✓        |
| `NEXT_PUBLIC_OG_IMAGE`   | (empty)                        | Open Graph image URL | ✗        |

### PART 7: AI/ML Service Configuration (Emerald Engine)

| Variable                | Default                      | Purpose                   |
| ----------------------- | ---------------------------- | ------------------------- |
| `SERVICE_FQDN_API`      | `http://emerald-engine:8000` | AI service FQDN           |
| `SERVICE_URL_API`       | `http://emerald-engine:8000` | AI service URL            |
| `AI_URL`                | `http://emerald-engine:8000` | AI service URL            |
| `NIXPACKS_NODE_VERSION` | `18`                         | Node.js version for build |

### PART 8: External AI/ML API Keys (Required for AI Features)

| Variable                | Default                  | Purpose                      | Required |
| ----------------------- | ------------------------ | ---------------------------- | -------- |
| `OPENAI_API_KEY`        | `your_openai_api_key`    | OpenAI API key               | ✓        |
| `OPENAI_EMBEDDING_NAME` | `text-embedding-3-small` | OpenAI embedding model       | ✓        |
| `GEMINI_API_KEY`        | `your_gemini_api_key`    | Google Gemini API key        | ✓        |
| `PINECONE_API_KEY`      | `your_pinecone_api_key`  | Pinecone vector DB API key   | ✓        |
| `PINECONE_CLOUD`        | `aws`                    | Pinecone cloud provider      | ✓        |
| `PINECONE_REGION`       | `us-east-1`              | Pinecone region              | ✓        |
| `RESEND_API`            | `your_resend_api_key`    | Resend email service API key | ✓        |

### PART 9: Cache Configuration (Nebula Cache - Redis)

| Variable    | Default                     | Purpose                 |
| ----------- | --------------------------- | ----------------------- |
| `MEMORY_DB` | `redis://nebula-cache:6379` | Redis connection string |

## 📋 Setup Instructions

### 1. Create `.env` File from Template

```bash
cp .env.example .env
```

### 2. Update Required Variables

The following variables **MUST** be updated with your actual values:

**Critical Security Variables:**

- `DOCKER_USERNAME` - Your Docker registry credentials
- `DATABASE_PASSWORD` - Modify the preset if desired
- `JWT_SECRET_TOKEN` - Change in production
- `API_KEY` - Your API key
- `ENDPOINT_AUTH_KEY` - Your authentication key

**External API Keys (Required for AI Features):**

- `OPENAI_API_KEY` - Obtain from OptimusAILabs
- `GEMINI_API_KEY` - Obtain from OptimusAILabs
- `PINECONE_API_KEY` - Obtain from OptimusAILabs
- `RESEND_API` - Obtain from OptimusAILabs

**Frontend Configuration (Update for your environment):**

- `NEXT_PUBLIC_BASE_URI` - Update to your actual API endpoint
- `NEXT_PUBLIC_SERVER_URI` - Update to your actual server endpoint
- `NEXT_PUBLIC_OG_IMAGE` - Update to your domain's image URL

### 3. Verify Configuration

```bash
# Check all required variables are set
grep "your_" .env  # Should return empty if all values updated
```

### 4. Quick Reference

**Preset Values (Usually no changes needed):**

- Database connection URLs
- Service hostnames (crimson-oracle, emerald-engine, sapphire-nexus, titan-vault, nebula-cache)
- Spring Boot configuration
- Default ports and configurations

**User Input Required:**

- All Docker registry credentials
- All external API keys
- Database password (optional modification)
- Service URLs for your deployment environment

## 🌐 Ports and Endpoints

| Service        | Port | Endpoint                              | Description          |
| -------------- | ---- | ------------------------------------- | -------------------- |
| Titan Vault    | 5432 | postgresql://localhost:5432/eev_db    | PostgreSQL Database  |
| Crimson Oracle | 8086 | http://localhost:8086                 | Backend API          |
| Crimson Oracle | 8086 | http://localhost:8086/actuator/health | Health Check         |
| Nebula Cache   | 6379 | redis://localhost:6379                | Redis Cache          |
| Emerald Engine | 8000 | http://localhost:8000                 | AI/ML Service        |
| Emerald Engine | 8000 | http://localhost:8000/health          | Health Check         |
| Sapphire Nexus | 3010 | http://localhost:3010                 | Frontend Application |

## 🏥 Health Checks

All services include health checks to ensure proper startup and operation:

### Titan Vault (PostgreSQL)

- **Command**: `pg_isready -U postgres`
- **Interval**: 10s
- **Timeout**: 5s
- **Retries**: 5

### Crimson Oracle (Backend)

- **Endpoint**: http://localhost:8086/actuator/health
- **Interval**: 30s
- **Timeout**: 3s
- **Retries**: 3
- **Start Period**: 60s

### Nebula Cache (Redis)

- **Command**: `redis-cli ping`
- **Interval**: 10s
- **Timeout**: 5s
- **Retries**: 5

### Emerald Engine (AI Service)

- **Endpoint**: http://localhost:8000/health
- **Interval**: 30s
- **Timeout**: 3s
- **Retries**: 3
- **Start Period**: 60s

### Sapphire Nexus (Frontend)

- **Command**: `wget --spider http://localhost:3010`
- **Interval**: 30s
- **Timeout**: 3s
- **Retries**: 3
- **Start Period**: 60s

## 💾 Volumes and Persistence

### Docker Compose Volumes

- `postgres_data`: PostgreSQL data directory
- `redis_data`: Redis persistence data

### Kubernetes Persistent Volumes

Persistent Volume Claims are defined in the respective service manifests for stateful data.

## 🔧 Troubleshooting

### Common Issues

#### Services Won't Start

```bash
# Check service status
docker-compose ps

# View logs
docker-compose logs -f [service-name]

# Check health
docker-compose exec crimson-oracle curl http://localhost:8086/actuator/health
```

#### Database Connection Issues

```bash
# Check PostgreSQL is running
docker-compose exec titan-vault pg_isready -U postgres

# Test connection
docker-compose exec titan-vault psql -U postgres -d eev_db -c "SELECT 1;"
```

#### Port Conflicts

If ports are already in use, modify the port mappings in `docker-compose.yml`:

```yaml
ports:
  - "5433:5432" # Change host port (left side)
```

#### Container Memory Issues

Increase Docker Desktop memory allocation (Preferences > Resources > Memory)

### Kubernetes Troubleshooting

```bash
# Check pod status
kubectl get pods -n eev

# Describe pod for events
kubectl describe pod <pod-name> -n eev

# View logs
kubectl logs -n eev <pod-name> -f

# Check services
kubectl get svc -n eev

# Check configmaps and secrets
kubectl get configmap,secret -n eev
```

## 🛠️ Maintenance

### Backup Database

```bash
# Docker Compose
docker-compose exec titan-vault pg_dump -U postgres eev_db > backup_$(date +%Y%m%d).sql

# Kubernetes
kubectl exec -n eev titan-vault-0 -- pg_dump -U postgres eev_db > backup_$(date +%Y%m%d).sql
```

### Restore Database

```bash
# Docker Compose
cat backup.sql | docker-compose exec -T titan-vault psql -U postgres eev_db

# Kubernetes
cat backup.sql | kubectl exec -i -n eev titan-vault-0 -- psql -U postgres eev_db
```

### Update Images

```bash
# Pull latest images
docker-compose pull

# Restart with new images
docker-compose up -d
```

### Clean Up

```bash
# Remove unused Docker resources
docker system prune -a

# Remove specific volumes
docker volume rm eev_client_deployment_postgres_data
```

## 📝 Notes

- **Security**: Change all default passwords and secret keys in production
- **Scaling**: For Kubernetes, adjust replicas in the deployment manifests
- **Monitoring**: Integrate with your monitoring solution (Prometheus, Grafana, etc.)
- **Logging**: Configure centralized logging for production environments
- **Backups**: Implement automated backup strategies for production data

## 📄 License

**All API Keys and Licenses** must be obtained from **OptimusAILabs**. Contact OptimusAILabs for licensing information and API credentials.

## 📧 Support

For issues and questions:

- Open an issue in the repository
- Contact: engineering@optimusai.ai

---

**Last Updated**: December 15, 2025
