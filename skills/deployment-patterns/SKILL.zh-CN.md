---
name: deployment-patterns
description: Deployment workflows, CI/CD pipeline patterns, Docker containerization, health checks, rollback strategies, and production readiness checklists for web applications.
description zh-CN: Web 应用程序的部署工作流、CI/CD 流水线模式、Docker 容器化、健康检查、回滚策略和生产就绪检查清单。
origin: ECC
---

# Deployment Patterns

## Deployment Patterns
## 部署模式

Production deployment workflows and CI/CD best practices.
生产部署工作流和 CI/CD 最佳实践。

## When to Activate

## When to Activate
## 何时激活

- Setting up CI/CD pipelines
  设置 CI/CD 流水线
- Dockerizing an application
  将应用程序 Docker 化
- Planning deployment strategy (blue-green, canary, rolling)
  规划部署策略（蓝绿、金丝雀、滚动）
- Implementing health checks and readiness probes
  实现健康检查和就绪探针
- Preparing for a production release
  准备生产发布
- Configuring environment-specific settings
  配置环境特定设置

## Deployment Strategies

## Deployment Strategies
## 部署策略

### Rolling Deployment (Default)

### Rolling Deployment (Default)
### 滚动部署（默认）

Replace instances gradually — old and new versions run simultaneously during rollout.
逐步替换实例——在发布期间，新旧版本同时运行。

```
Instance 1: v1 → v2  (update first)
Instance 2: v1        (still running v1)
Instance 3: v1        (still running v1)

Instance 1: v2
Instance 2: v1 → v2  (update second)
Instance 3: v1

Instance 1: v2
Instance 2: v2
Instance 3: v1 → v2  (update last)
```

**Pros:** Zero downtime, gradual rollout
**优点：** 零停机时间，逐步发布
**Cons:** Two versions run simultaneously — requires backward-compatible changes
**缺点：** 两个版本同时运行——需要向后兼容的更改
**Use when:** Standard deployments, backward-compatible changes
**使用场景：** 标准部署、向后兼容的更改

### Blue-Green Deployment

### Blue-Green Deployment
### 蓝绿部署

Run two identical environments. Switch traffic atomically.
运行两个相同环境。原子性地切换流量。

```
Blue  (v1) ← traffic
Green (v2)   idle, running new version

# After verification:
Blue  (v1)   idle (becomes standby)
Green (v2) ← traffic
```

**Pros:** Instant rollback (switch back to blue), clean cutover
**优点：** 即时回滚（切换回蓝色），干净切换
**Cons:** Requires 2x infrastructure during deployment
**缺点：** 部署期间需要 2 倍基础设施
**Use when:** Critical services, zero-tolerance for issues
**使用场景：** 关键服务、零容忍问题

### Canary Deployment

### Canary Deployment
### 金丝雀部署

Route a small percentage of traffic to the new version first.
首先将一小部分流量路由到新版本。

```
v1: 95% of traffic
v2:  5% of traffic  (canary)

# If metrics look good:
v1: 50% of traffic
v2: 50% of traffic

# Final:
v2: 100% of traffic
```

**Pros:** Catches issues with real traffic before full rollout
**优点：** 在完全发布前用真实流量捕获问题
**Cons:** Requires traffic splitting infrastructure, monitoring
**缺点：** 需要流量分割基础设施和监控
**Use when:** High-traffic services, risky changes, feature flags
**使用场景：** 高流量服务、风险更改、功能开关

## Docker

## Docker
## Docker

### Multi-Stage Dockerfile (Node.js)

### Multi-Stage Dockerfile (Node.js)
### 多阶段 Dockerfile（Node.js）

```dockerfile
# Stage 1: Install dependencies
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --production=false

# Stage 2: Build
FROM node:22-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build
RUN npm prune --production

# Stage 3: Production image
FROM node:22-alpine AS runner
WORKDIR /app

RUN addgroup -g 1001 -S appgroup && adduser -S appuser -u 1001
USER appuser

COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/package.json ./

ENV NODE_ENV=production
EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

CMD ["node", "dist/server.js"]
```

### Multi-Stage Dockerfile (Go)

### Multi-Stage Dockerfile (Go)
### 多阶段 Dockerfile（Go）

```dockerfile
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /server ./cmd/server

FROM alpine:3.19 AS runner
RUN apk --no-cache add ca-certificates
RUN adduser -D -u 1001 appuser
USER appuser

COPY --from=builder /server /server

EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:8080/health || exit 1
CMD ["/server"]
```

### Multi-Stage Dockerfile (Python/Django)

### Multi-Stage Dockerfile (Python/Django)
### 多阶段 Dockerfile（Python/Django）

```dockerfile
FROM python:3.12-slim AS builder
WORKDIR /app
RUN pip install --no-cache-dir uv
COPY requirements.txt .
RUN uv pip install --system --no-cache -r requirements.txt

FROM python:3.12-slim AS runner
WORKDIR /app

RUN useradd -r -u 1001 appuser
USER appuser

COPY --from=builder /usr/local/lib/python3.12/site-packages /usr/local/lib/python3.12/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin
COPY . .

ENV PYTHONUNBUFFERED=1
EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=3s CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health/')" || exit 1
CMD ["gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "4"]
```

### Docker Best Practices

### Docker Best Practices
### Docker 最佳实践

```
# GOOD practices
# 好的做法
- Use specific version tags (node:22-alpine, not node:latest)
  使用特定版本标签（node:22-alpine，而非 node:latest）
- Multi-stage builds to minimize image size
  多阶段构建以最小化镜像大小
- Run as non-root user
  以非 root 用户运行
- Copy dependency files first (layer caching)
  首先复制依赖文件（层缓存）
- Use .dockerignore to exclude node_modules, .git, tests
  使用 .dockerignore 排除 node_modules、.git、tests
- Add HEALTHCHECK instruction
  添加 HEALTHCHECK 指令
- Set resource limits in docker-compose or k8s
  在 docker-compose 或 k8s 中设置资源限制

# BAD practices
# 坏的做泫
- Running as root
  以 root 运行
- Using :latest tags
  使用 :latest 标签
- Copying entire repo in one COPY layer
  在一个 COPY 层中复制整个仓库
- Installing dev dependencies in production image
  在生产镜像中安装开发依赖
- Storing secrets in image (use env vars or secrets manager)
  在镜像中存储密钥（使用环境变量或密钥管理器）
```

## CI/CD Pipeline

## CI/CD Pipeline
## CI/CD 流水线

### GitHub Actions (Standard Pipeline)

### GitHub Actions (Standard Pipeline)
### GitHub Actions（标准流水线）

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm test -- --coverage
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: coverage
          path: coverage/

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy to production
        run: |
          # Platform-specific deployment command
          # 平台特定的部署命令
          # Railway: railway up
          # Vercel: vercel --prod
          # K8s: kubectl set image deployment/app app=ghcr.io/${{ github.repository }}:${{ github.sha }}
          echo "Deploying ${{ github.sha }}"
```

### Pipeline Stages

### Pipeline Stages
### 流水线阶段

```
PR opened:
  lint → typecheck → unit tests → integration tests → preview deploy
PR 打开时：
  lint → typecheck → 单元测试 → 集成测试 → 预览部署

Merged to main:
  lint → typecheck → unit tests → integration tests → build image → deploy staging → smoke tests → deploy production
合并到 main：
  lint → typecheck → 单元测试 → 集成测试 → 构建镜像 → 部署到预演 → 冒烟测试 → 部署到生产
```

## Health Checks

## Health Checks
## 健康检查

### Health Check Endpoint

### Health Check Endpoint
### 健康检查端点

```typescript
// Simple health check
// 简单健康检查
app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok" });
});

// Detailed health check (for internal monitoring)
// 详细健康检查（用于内部监控）
app.get("/health/detailed", async (req, res) => {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    externalApi: await checkExternalApi(),
  };

  const allHealthy = Object.values(checks).every(c => c.status === "ok");

  res.status(allHealthy ? 200 : 503).json({
    status: allHealthy ? "ok" : "degraded",
    timestamp: new Date().toISOString(),
    version: process.env.APP_VERSION || "unknown",
    uptime: process.uptime(),
    checks,
  });
});

async function checkDatabase(): Promise<HealthCheck> {
  try {
    await db.query("SELECT 1");
    return { status: "ok", latency_ms: 2 };
  } catch (err) {
    return { status: "error", message: "Database unreachable" };
  }
}
```

### Kubernetes Probes

### Kubernetes Probes
### Kubernetes 探针

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 3000
  initialDelaySeconds: 10
  periodSeconds: 30
  failureThreshold: 3

readinessProbe:
  httpGet:
    path: /health
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 10
  failureThreshold: 2

startupProbe:
  httpGet:
    path: /health
    port: 3000
  initialDelaySeconds: 0
  periodSeconds: 5
  failureThreshold: 30    # 30 * 5s = 150s max startup time
```

## Environment Configuration

## Environment Configuration
## 环境配置

### Twelve-Factor App Pattern

### Twelve-Factor App Pattern
### 十二要素应用模式

```bash
# All config via environment variables — never in code
# 所有配置通过环境变量——永不写在代码中
DATABASE_URL=postgres://user:pass@host:5432/db
REDIS_URL=redis://host:6379/0
API_KEY=${API_KEY}           # injected by secrets manager
LOG_LEVEL=info
PORT=3000

# Environment-specific behavior
# 环境特定行为
NODE_ENV=production          # or staging, development
APP_ENV=production           # explicit app environment
```

### Configuration Validation

### Configuration Validation
### 配置验证

```typescript
import { z } from "zod";

const envSchema = z.object({
  NODE_ENV: z.enum(["development", "staging", "production"]),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  REDIS_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  LOG_LEVEL: z.enum(["debug", "info", "warn", "error"]).default("info"),
});

// Validate at startup — fail fast if config is wrong
// 启动时验证——配置错误时快速失败
export const env = envSchema.parse(process.env);
```

## Rollback Strategy

## Rollback Strategy
## 回滚策略

### Instant Rollback

### Instant Rollback
### 即时回滚

```bash
# Docker/Kubernetes: point to previous image
# Docker/Kubernetes：指向之前的镜像
kubectl rollout undo deployment/app

# Vercel: promote previous deployment
# Vercel：提升之前的部署
vercel rollback

# Railway: redeploy previous commit
# Railway：重新部署之前的提交
railway up --commit <previous-sha>

# Database: rollback migration (if reversible)
# 数据库：回滚迁移（如果可逆）
npx prisma migrate resolve --rolled-back <migration-name>
```

### Rollback Checklist

### Rollback Checklist
### 回滚检查清单

- [ ] Previous image/artifact is available and tagged
  之前的镜像/构件可用并已标记
- [ ] Database migrations are backward-compatible (no destructive changes)
  数据库迁移向后兼容（无破坏性更改）
- [ ] Feature flags can disable new features without deploy
  功能开关可以在不部署的情况下禁用新功能
- [ ] Monitoring alerts configured for error rate spikes
  已配置错误率飙升的监控警报
- [ ] Rollback tested in staging before production release
  在生产发布前在预演环境测试过回滚

## Production Readiness Checklist

## Production Readiness Checklist
## 生产就绪检查清单

Before any production deployment:
在任何生产部署之前：

### Application

### Application
### 应用程序

- [ ] All tests pass (unit, integration, E2E)
  所有测试通过（单元、集成、E2E）
- [ ] No hardcoded secrets in code or config files
  代码或配置文件中没有硬编码的密钥
- [ ] Error handling covers all edge cases
  错误处理覆盖所有边缘情况
- [ ] Logging is structured (JSON) and does not contain PII
  日志是结构化的（JSON）且不包含个人身份信息
- [ ] Health check endpoint returns meaningful status
  健康检查端点返回有意义的状诚

### Infrastructure

### Infrastructure
### 基础设施

- [ ] Docker image builds reproducibly (pinned versions)
  Docker 镜像可重现构建（固定版本）
- [ ] Environment variables documented and validated at startup
  环境变量已记录并在启动时验证
- [ ] Resource limits set (CPU, memory)
  已设置资源限制（CPU、内存）
- [ ] Horizontal scaling configured (min/max instances)
  已配置水平扩展（最小/最大实例数）
- [ ] SSL/TLS enabled on all endpoints
  所有端点启用 SSL/TLS

### Monitoring

### Monitoring
### 监控

- [ ] Application metrics exported (request rate, latency, errors)
  导出应用程序指标（请求率、延迟、错误）
- [ ] Alerts configured for error rate > threshold
  已配置错误率 > 阈值的警报
- [ ] Log aggregation set up (structured logs, searchable)
  已设置日志聚合（结构化日志、可搜索）
- [ ] Uptime monitoring on health endpoint
  健康端点运行时间监控

### Security

### Security
### 安全

- [ ] Dependencies scanned for CVEs
  依赖项已扫描 CVE
- [ ] CORS configured for allowed origins only
  CORS 仅配置允许的来源
- [ ] Rate limiting enabled on public endpoints
  公共端点启用速率限制
- [ ] Authentication and authorization verified
  验证了身份验证和授权
- [ ] Security headers set (CSP, HSTS, X-Frame-Options)
  已设置安全头（CSP、HSTS、X-Frame-Options）

### Operations

### Operations
### 运维

- [ ] Rollback plan documented and tested
  回滚计划已记录并测试
- [ ] Database migration tested against production-sized data
  已针对生产规模数据测试数据库迁移
- [ ] Runbook for common failure scenarios
  常见故障场景的操作手册
- [ ] On-call rotation and escalation path defined
  已定义值班轮换和升级路径
