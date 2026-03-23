---
name: docker-patterns
description: Docker and Docker Compose patterns for local development, container security, networking, volume strategies, and multi-service orchestration.
description zh-CN: 用于本地开发、容器安全、网络、卷策略和多服务编排的 Docker 和 Docker Compose 模式。
origin: ECC
---

# Docker Patterns

## Docker Patterns
## Docker 模式

Docker and Docker Compose best practices for containerized development.
容器化开发的 Docker 和 Docker Compose 最佳实践。

## When to Activate

## When to Activate
## 何时激活

- Setting up Docker Compose for local development
  为本地开发设置 Docker Compose
- Designing multi-container architectures
  设计多容器架构
- Troubleshooting container networking or volume issues
  排查容器网络或卷问题
- Reviewing Dockerfiles for security and size
  审查 Dockerfile 的安全性和大小
- Migrating from local dev to containerized workflow
  从本地开发迁移到容器化工作流

## Docker Compose for Local Development

## Docker Compose for Local Development
## Docker Compose 用于本地开发

### Standard Web App Stack

### Standard Web App Stack
### 标准 Web 应用栈

```yaml
# docker-compose.yml
services:
  app:
    build:
      context: .
      target: dev                     # Use dev stage of multi-stage Dockerfile
    ports:
      - "3000:3000"
    volumes:
      - .:/app                        # Bind mount for hot reload
      - /app/node_modules             # Anonymous volume -- preserves container deps
    environment:
      - DATABASE_URL=postgres://postgres:postgres@db:5432/app_dev
      - REDIS_URL=redis://redis:6379/0
      - NODE_ENV=development
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    command: npm run dev

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app_dev
    volumes:
      - pgdata:/var/lib/postgresql/data
      - ./scripts/init-db.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redisdata:/data

  mailpit:                            # Local email testing
    image: axllent/mailpit
    ports:
      - "8025:8025"                   # Web UI
      - "1025:1025"                   # SMTP

volumes:
  pgdata:
  redisdata:
```

### Development vs Production Dockerfile

### Development vs Production Dockerfile
### 开发与生产 Dockerfile

```dockerfile
# Stage: dependencies
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# Stage: dev (hot reload, debug tools)
FROM node:22-alpine AS dev
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]

# Stage: build
FROM node:22-alpine AS build
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build && npm prune --production

# Stage: production (minimal image)
FROM node:22-alpine AS production
WORKDIR /app
RUN addgroup -g 1001 -S appgroup && adduser -S appuser -u 1001
USER appuser
COPY --from=build --chown=appuser:appgroup /app/dist ./dist
COPY --from=build --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=build --chown=appuser:appgroup /app/package.json ./
ENV NODE_ENV=production
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```

### Override Files

### Override Files
### 覆盖文件

```yaml
# docker-compose.override.yml (auto-loaded, dev-only settings)
services:
  app:
    environment:
      - DEBUG=app:*
      - LOG_LEVEL=debug
    ports:
      - "9229:9229"                   # Node.js debugger

# docker-compose.prod.yml (explicit for production)
services:
  app:
    build:
      target: production
    restart: always
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
```

```bash
# Development (auto-loads override)
# 开发（自动加载覆盖）
docker compose up

# Production
# 生产
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

## Networking

## Networking
## 网络

### Service Discovery

### Service Discovery
### 服务发现

Services in the same Compose network resolve by service name:
同一 Compose 网络中的服务通过服务名解析：

```
# From "app" container:
# 从 "app" 容器：
postgres://postgres:postgres@db:5432/app_dev    # "db" resolves to the db container
redis://redis:6379/0                             # "redis" resolves to the redis container
```

### Custom Networks

### Custom Networks
### 自定义网络

```yaml
services:
  frontend:
    networks:
      - frontend-net

  api:
    networks:
      - frontend-net
      - backend-net

  db:
    networks:
      - backend-net              # Only reachable from api, not frontend

networks:
  frontend-net:
  backend-net:
```

### Exposing Only What's Needed

### Exposing Only What's Needed
### 仅暴露所需内容

```yaml
services:
  db:
    ports:
      - "127.0.0.1:5432:5432"   # Only accessible from host, not network
    # Omit ports entirely in production -- accessible only within Docker network
```

## Volume Strategies

## Volume Strategies
## 卷策略

```yaml
volumes:
  # Named volume: persists across container restarts, managed by Docker
  # 命名卷：跨容器重启持久化，由 Docker 管理
  pgdata:

  # Bind mount: maps host directory into container (for development)
  # 绑定挂载：将主机目录映射到容器（用于开发）
  # - ./src:/app/src

  # Anonymous volume: preserves container-generated content from bind mount override
  # 匿名卷：保护容器生成的内容免受绑定挂载覆盖
  # - /app/node_modules
```

### Common Patterns

### Common Patterns
### 常见模式

```yaml
services:
  app:
    volumes:
      - .:/app                   # Source code (bind mount for hot reload)
      - /app/node_modules        # Protect container's node_modules from host
      - /app/.next               # Protect build cache

  db:
    volumes:
      - pgdata:/var/lib/postgresql/data          # Persistent data
      - ./scripts/init.sql:/docker-entrypoint-initdb.d/init.sql  # Init scripts
```

## Container Security

## Container Security
## 容器安全

### Dockerfile Hardening

### Dockerfile Hardening
### Dockerfile 加固

```dockerfile
# 1. Use specific tags (never :latest)
# 1. 使用特定标签（永不 :latest）
FROM node:22.12-alpine3.20

# 2. Run as non-root
# 2. 以非 root 运行
RUN addgroup -g 1001 -S app && adduser -S app -u 1001
USER app

# 3. Drop capabilities (in compose)
# 3. 丢弃 capabilities（在 compose 中）
# 4. Read-only root filesystem where possible
# 4. 尽可能使用只读根文件系统
# 5. No secrets in image layers
# 5. 镜像层中无密钥
```

### Compose Security

### Compose Security
### Compose 安全

```yaml
services:
  app:
    security_opt:
      - no-new-privileges:true
    read_only: true
    tmpfs:
      - /tmp
      - /app/.cache
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE          # Only if binding to ports < 1024
```

### Secret Management

### Secret Management
### 密钥管理

```yaml
# GOOD: Use environment variables (injected at runtime)
# 好：使用环境变量（在运行时注入）
services:
  app:
    env_file:
      - .env                     # Never commit .env to git
    environment:
      - API_KEY                  # Inherits from host environment

# GOOD: Docker secrets (Swarm mode)
# 好：Docker secrets（Swarm 模式）
secrets:
  db_password:
    file: ./secrets/db_password.txt

services:
  db:
    secrets:
      - db_password

# BAD: Hardcoded in image
# 坏：硬编码在镜像中
# ENV API_KEY=sk-proj-xxxxx      # NEVER DO THIS
```

## .dockerignore

## .dockerignore
## .dockerignore

```
node_modules
.git
.env
.env.*
dist
coverage
*.log
.next
.cache
docker-compose*.yml
Dockerfile*
README.md
tests/
```

## Debugging

## Debugging
## 调试

### Common Commands

### Common Commands
### 常用命令

```bash
# View logs
# 查看日志
docker compose logs -f app           # Follow app logs
docker compose logs --tail=50 db     # Last 50 lines from db

# Execute commands in running container
# 在运行中的容器中执行命令
docker compose exec app sh           # Shell into app
docker compose exec db psql -U postgres  # Connect to postgres

# Inspect
# 检查
docker compose ps                     # Running services
docker compose top                    # Processes in each container
docker stats                          # Resource usage

# Rebuild
# 重新构建
docker compose up --build             # Rebuild images
docker compose build --no-cache app   # Force full rebuild

# Clean up
# 清理
docker compose down                   # Stop and remove containers
docker compose down -v                # Also remove volumes (DESTRUCTIVE)
docker system prune                   # Remove unused images/containers
```

### Debugging Network Issues

### Debugging Network Issues
### 排查网络问题

```bash
# Check DNS resolution inside container
# 检查容器内的 DNS 解析
docker compose exec app nslookup db

# Check connectivity
# 检查连接
docker compose exec app wget -qO- http://api:3000/health

# Inspect network
# 检查网络
docker network ls
docker network inspect <project>_default
```

## Anti-Patterns

## Anti-Patterns
## 反模式

```
# BAD: Using docker compose in production without orchestration
# Use Kubernetes, ECS, or Docker Swarm for production multi-container workloads
# 坏：在没有编排的情况下在生产中使用 docker compose
# 生产多容器工作负载使用 Kubernetes、ECS 或 Docker Swarm

# BAD: Storing data in containers without volumes
# Containers are ephemeral -- all data lost on restart without volumes
# 坏：容器中存储数据而没有卷
# 容器是临时的——没有卷的情况下重启会丢失所有数据

# BAD: Running as root
# Always create and use a non-root user
# 坏：以 root 运行
# 始终创建并使用非 root 用户

# BAD: Using :latest tag
# Pin to specific versions for reproducible builds
# 坏：使用 :latest 标签
# 固定到特定版本以实现可重现构建

# BAD: One giant container with all services
# Separate concerns: one process per container
# 坏：一个包含所有服务的巨大容器
# 分离关注点：每个容器一个进程

# BAD: Putting secrets in docker-compose.yml
# Use .env files (gitignored) or Docker secrets
# 坏：在 docker-compose.yml 中放置密钥
# 使用 .env 文件（gitignore）或 Docker secrets
```
