# Cloud & Infrastructure Security Skill

## Cloud & Infrastructure Security Skill
## 云和基础设施安全技能

This skill ensures cloud infrastructure, CI/CD pipelines, and deployment configurations follow security best practices and comply with industry standards.
此技能确保云基础设施、CI/CD 管道和部署配置遵循安全最佳实践并符合行业标准。

## When to Activate

## When to Activate
## 何时激活

- Deploying applications to cloud platforms (AWS, Vercel, Railway, Cloudflare)
  - 将应用程序部署到云平台（AWS、Vercel、Railway、Cloudflare）
- Configuring IAM roles and permissions
  - 配置 IAM 角色和权限
- Setting up CI/CD pipelines
  - 设置 CI/CD 管道
- Implementing infrastructure as code (Terraform, CloudFormation)
  - 实现基础设施即代码（Terraform、CloudFormation）
- Configuring logging and monitoring
  - 配置日志记录和监控
- Managing secrets in cloud environments
  - 在云环境中管理密钥
- Setting up CDN and edge security
  - 设置 CDN 和边缘安全
- Implementing disaster recovery and backup strategies
  - 实现灾难恢复和备份策略

## Cloud Security Checklist

## Cloud Security Checklist
## 云安全检查清单

### 1. IAM & Access Control

### 1. IAM & Access Control
### 1. IAM 和访问控制

#### Principle of Least Privilege

#### Principle of Least Privilege
#### 最小权限原则

```yaml
# CORRECT: Minimal permissions
# 正确：最小权限
iam_role:
  permissions:
    - s3:GetObject  # Only read access
    - s3:ListBucket
  resources:
    - arn:aws:s3:::my-bucket/*  # Specific bucket only

# WRONG: Overly broad permissions
# 错误：过于宽泛的权限
iam_role:
  permissions:
    - s3:*  # All S3 actions
  resources:
    - "*"  # All resources
```

#### Multi-Factor Authentication (MFA)

#### Multi-Factor Authentication (MFA)
#### 多因素认证（MFA）

```bash
# ALWAYS enable MFA for root/admin accounts
# 始终为 root/管理员账户启用 MFA
aws iam enable-mfa-device \
  --user-name admin \
  --serial-number arn:aws:iam::123456789:mfa/admin \
  --authentication-code1 123456 \
  --authentication-code2 789012
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] No root account usage in production
  - [ ] 生产环境中不使用 root 账户
- [ ] MFA enabled for all privileged accounts
  - [ ] 为所有特权账户启用 MFA
- [ ] Service accounts use roles, not long-lived credentials
  - [ ] 服务账户使用角色而非长期凭证
- [ ] IAM policies follow least privilege
  - [ ] IAM 策略遵循最小权限原则
- [ ] Regular access reviews conducted
  - [ ] 定期进行访问审查
- [ ] Unused credentials rotated or removed
  - [ ] 未使用的凭证已轮换或移除

### 2. Secrets Management

### 2. Secrets Management
### 2. 密钥管理

#### Cloud Secrets Managers

#### Cloud Secrets Managers
#### 云密钥管理器

```typescript
// CORRECT: Use cloud secrets manager
// 正确：使用云密钥管理器
import { SecretsManager } from '@aws-sdk/client-secrets-manager';

const client = new SecretsManager({ region: 'us-east-1' });
const secret = await client.getSecretValue({ SecretId: 'prod/api-key' });
const apiKey = JSON.parse(secret.SecretString).key;

// WRONG: Hardcoded or in environment variables only
// 错误：硬编码或仅在环境变量中
const apiKey = process.env.API_KEY; // Not rotated, not audited
```

#### Secrets Rotation

#### Secrets Rotation
#### 密钥轮换

```bash
# Set up automatic rotation for database credentials
# 为数据库凭证设置自动轮换
aws secretsmanager rotate-secret \
  --secret-id prod/db-password \
  --rotation-lambda-arn arn:aws:lambda:region:account:function:rotate \
  --rotation-rules AutomaticallyAfterDays=30
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] All secrets stored in cloud secrets manager (AWS Secrets Manager, Vercel Secrets)
  - [ ] 所有密钥存储在云密钥管理器中（AWS Secrets Manager、Vercel Secrets）
- [ ] Automatic rotation enabled for database credentials
  - [ ] 为数据库凭证启用自动轮换
- [ ] API keys rotated at least quarterly
  - [ ] API 密钥至少每季度轮换一次
- [ ] No secrets in code, logs, or error messages
  - [ ] 代码、日志或错误消息中无密钥
- [ ] Audit logging enabled for secret access
  - [ ] 为密钥访问启用审计日志

### 3. Network Security

### 3. Network Security
### 3. 网络安全

#### VPC and Firewall Configuration

#### VPC and Firewall Configuration
#### VPC 和防火墙配置

```terraform
# CORRECT: Restricted security group
# 正确：受限的安全组
resource "aws_security_group" "app" {
  name = "app-sg"

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16"]  # Internal VPC only
  }

  egress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Only HTTPS outbound
  }
}

# WRONG: Open to the internet
# 错误：向互联网开放
resource "aws_security_group" "bad" {
  ingress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # All ports, all IPs!
  }
}
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] Database not publicly accessible
  - [ ] 数据库不可公开访问
- [ ] SSH/RDP ports restricted to VPN/bastion only
  - [ ] SSH/RDP 端口仅限 VPN/bastion 访问
- [ ] Security groups follow least privilege
  - [ ] 安全组遵循最小权限原则
- [ ] Network ACLs configured
  - [ ] 已配置网络 ACL
- [ ] VPC flow logs enabled
  - [ ] 已启用 VPC 流日志

### 4. Logging & Monitoring

### 4. Logging & Monitoring
### 4. 日志记录和监控

#### CloudWatch/Logging Configuration

#### CloudWatch/Logging Configuration
#### CloudWatch/日志配置

```typescript
// CORRECT: Comprehensive logging
// 正确：全面的日志记录
import { CloudWatchLogsClient, CreateLogStreamCommand } from '@aws-sdk/client-cloudwatch-logs';

const logSecurityEvent = async (event: SecurityEvent) => {
  await cloudwatch.putLogEvents({
    logGroupName: '/aws/security/events',
    logStreamName: 'authentication',
    logEvents: [{
      timestamp: Date.now(),
      message: JSON.stringify({
        type: event.type,
        userId: event.userId,
        ip: event.ip,
        result: event.result,
        // Never log sensitive data
        // 永不记录敏感数据
      })
    }]
  });
};
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] CloudWatch/logging enabled for all services
  - [ ] 为所有服务启用 CloudWatch/日志记录
- [ ] Failed authentication attempts logged
  - [ ] 记录失败的认证尝试
- [ ] Admin actions audited
  - [ ] 审计管理员操作
- [ ] Log retention configured (90+ days for compliance)
  - [ ] 配置日志保留期限（合规需要 90 天以上）
- [ ] Alerts configured for suspicious activity
  - [ ] 为可疑活动配置警报
- [ ] Logs centralized and tamper-proof
  - [ ] 日志集中且防篡改

### 5. CI/CD Pipeline Security

### 5. CI/CD Pipeline Security
### 5. CI/CD 管道安全

#### Secure Pipeline Configuration

#### Secure Pipeline Configuration
#### 安全管道配置

```yaml
# CORRECT: Secure GitHub Actions workflow
# 正确：安全的 GitHub Actions 工作流
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read  # Minimal permissions

    steps:
      - uses: actions/checkout@v4

      # Scan for secrets
      # 扫描密钥
      - name: Secret scanning
        uses: trufflesecurity/trufflehog@main

      # Dependency audit
      # 依赖审计
      - name: Audit dependencies
        run: npm audit --audit-level=high

      # Use OIDC, not long-lived tokens
      # 使用 OIDC，不使用长期令牌
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789:role/GitHubActionsRole
          aws-region: us-east-1
```

#### Supply Chain Security

#### Supply Chain Security
#### 供应链安全

```json
// package.json - Use lock files and integrity checks
// package.json - 使用锁文件和完整性检查
{
  "scripts": {
    "install": "npm ci",  // Use ci for reproducible builds
    "audit": "npm audit --audit-level=moderate",
    "check": "npm outdated"
  }
}
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] OIDC used instead of long-lived credentials
  - [ ] 使用 OIDC 而非长期凭证
- [ ] Secrets scanning in pipeline
  - [ ] 在管道中进行密钥扫描
- [ ] Dependency vulnerability scanning
  - [ ] 依赖漏洞扫描
- [ ] Container image scanning (if applicable)
  - [ ] 容器镜像扫描（如适用）
- [ ] Branch protection rules enforced
  - [ ] 强制执行分支保护规则
- [ ] Code review required before merge
  - [ ] 合并前需要代码审查
- [ ] Signed commits enforced
  - [ ] 强制执行签名提交

### 6. Cloudflare & CDN Security

### 6. Cloudflare & CDN Security
### 6. Cloudflare 和 CDN 安全

#### Cloudflare Security Configuration

#### Cloudflare Security Configuration
#### Cloudflare 安全配置

```typescript
// CORRECT: Cloudflare Workers with security headers
// 正确：带有安全头的 Cloudflare Workers
export default {
  async fetch(request: Request): Promise<Response> {
    const response = await fetch(request);

    // Add security headers
    // 添加安全头
    const headers = new Headers(response.headers);
    headers.set('X-Frame-Options', 'DENY');
    headers.set('X-Content-Type-Options', 'nosniff');
    headers.set('Referrer-Policy', 'strict-origin-when-cross-origin');
    headers.set('Permissions-Policy', 'geolocation=(), microphone=()');

    return new Response(response.body, {
      status: response.status,
      headers
    });
  }
};
```

#### WAF Rules

#### WAF Rules
#### WAF 规则

```bash
# Enable Cloudflare WAF managed rules
# 启用 Cloudflare WAF 托管规则
# - OWASP Core Ruleset
# - Cloudflare Managed Ruleset
# - Rate limiting rules
# - Bot protection
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] WAF enabled with OWASP rules
  - [ ] 启用带 OWASP 规则的 WAF
- [ ] Rate limiting configured
  - [ ] 已配置速率限制
- [ ] Bot protection active
  - [ ] 机器人保护已激活
- [ ] DDoS protection enabled
  - [ ] 已启用 DDoS 防护
- [ ] Security headers configured
  - [ ] 已配置安全头
- [ ] SSL/TLS strict mode enabled
  - [ ] 已启用 SSL/TLS 严格模式

### 7. Backup & Disaster Recovery

### 7. Backup & Disaster Recovery
### 7. 备份和灾难恢复

#### Automated Backups

#### Automated Backups
#### 自动备份

```terraform
# CORRECT: Automated RDS backups
# 正确：自动 RDS 备份
resource "aws_db_instance" "main" {
  allocated_storage     = 20
  engine               = "postgres"

  backup_retention_period = 30  # 30 days retention
  backup_window          = "03:00-04:00"
  maintenance_window     = "mon:04:00-mon:05:00"

  enabled_cloudwatch_logs_exports = ["postgresql"]

  deletion_protection = true  # Prevent accidental deletion
}
```

#### Verification Steps

#### Verification Steps
#### 验证步骤

- [ ] Automated daily backups configured
  - [ ] 已配置每日自动备份
- [ ] Backup retention meets compliance requirements
  - [ ] 备份保留符合合规要求
- [ ] Point-in-time recovery enabled
  - [ ] 已启用时间点恢复
- [ ] Backup testing performed quarterly
  - [ ] 每季度进行备份测试
- [ ] Disaster recovery plan documented
  - [ ] 已记录灾难恢复计划
- [ ] RPO and RTO defined and tested
  - [ ] RPO 和 RTO 已定义并测试

## Pre-Deployment Cloud Security Checklist

## Pre-Deployment Cloud Security Checklist
## 部署前云安全检查清单

Before ANY production cloud deployment:
在任何生产云部署之前：

- [ ] **IAM**: Root account not used, MFA enabled, least privilege policies
  - [ ] **IAM**：不使用 root 账户、启用 MFA、最小权限策略
- [ ] **Secrets**: All secrets in cloud secrets manager with rotation
  - [ ] **密钥**：所有密钥在云密钥管理器中并设置轮换
- [ ] **Network**: Security groups restricted, no public databases
  - [ ] **网络**：安全组受限、无公共数据库
- [ ] **Logging**: CloudWatch/logging enabled with retention
  - [ ] **日志记录**：启用 CloudWatch/日志记录并设置保留
- [ ] **Monitoring**: Alerts configured for anomalies
  - [ ] **监控**：为异常配置警报
- [ ] **CI/CD**: OIDC auth, secrets scanning, dependency audits
  - [ ] **CI/CD**：OIDC 认证、密钥扫描、依赖审计
- [ ] **CDN/WAF**: Cloudflare WAF enabled with OWASP rules
  - [ ] **CDN/WAF**：启用带 OWASP 规则的 Cloudflare WAF
- [ ] **Encryption**: Data encrypted at rest and in transit
  - [ ] **加密**：静态和传输中的数据均已加密
- [ ] **Backups**: Automated backups with tested recovery
  - [ ] **备份**：自动备份并测试恢复
- [ ] **Compliance**: GDPR/HIPAA requirements met (if applicable)
  - [ ] **合规**：满足 GDPR/HIPAA 要求（如适用）
- [ ] **Documentation**: Infrastructure documented, runbooks created
  - [ ] **文档**：记录基础设施、创建运维手册
- [ ] **Incident Response**: Security incident plan in place
  - [ ] **事件响应**：制定安全事件计划

## Common Cloud Security Misconfigurations

## Common Cloud Security Misconfigurations
## 常见云安全错误配置

### S3 Bucket Exposure

### S3 Bucket Exposure
### S3 存储桶暴露

```bash
# WRONG: Public bucket
# 错误：公共存储桶
aws s3api put-bucket-acl --bucket my-bucket --acl public-read

# CORRECT: Private bucket with specific access
# 正确：私有存储桶配特定访问权限
aws s3api put-bucket-acl --bucket my-bucket --acl private
aws s3api put-bucket-policy --bucket my-bucket --policy file://policy.json
```

### RDS Public Access

### RDS Public Access
### RDS 公开访问

```terraform
# WRONG
# 错误
resource "aws_db_instance" "bad" {
  publicly_accessible = true  # NEVER do this!
}

# CORRECT
# 正确
resource "aws_db_instance" "good" {
  publicly_accessible = false
  vpc_security_group_ids = [aws_security_group.db.id]
}
```

## Resources

## Resources
## 资源

- [AWS Security Best Practices](https://aws.amazon.com/security/best-practices/)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)
- [Cloudflare Security Documentation](https://developers.cloudflare.com/security/)
- [OWASP Cloud Security](https://owasp.org/www-project-cloud-security/)
- [Terraform Security Best Practices](https://www.terraform.io/docs/cloud/guides/recommended-practices/)

**Remember**: Cloud misconfigurations are the leading cause of data breaches. A single exposed S3 bucket or overly permissive IAM policy can compromise your entire infrastructure. Always follow the principle of least privilege and defense in depth.
**记住**：云配置错误是数据泄露的主要原因。单个暴露的 S3 存储桶或过于宽松的 IAM 策略可能会危及整个基础设施。始终遵循最小权限原则和纵深防御。
