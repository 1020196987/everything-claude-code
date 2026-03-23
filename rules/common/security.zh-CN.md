---
name: Security
description: Security guidelines and mandatory security checks
description: 安全指南和强制安全检查
---

# Security Guidelines
# 安全指南

## Mandatory Security Checks
## 强制安全检查

Before ANY commit:
在提交任何代码前：

- [ ] No hardcoded secrets (API keys, passwords, tokens)
  [ ] 无硬编码密钥（API 密钥、密码、令牌）
- [ ] All user inputs validated
  [ ] 所有用户输入已验证
- [ ] SQL injection prevention (parameterized queries)
  [ ] SQL 注入防护（参数化查询）
- [ ] XSS prevention (sanitized HTML)
  [ ] XSS 防护（清理 HTML）
- [ ] CSRF protection enabled
  [ ] CSRF 防护已启用
- [ ] Authentication/authorization verified
  [ ] 身份验证/授权已验证
- [ ] Rate limiting on all endpoints
  [ ] 所有端点有速率限制
- [ ] Error messages don't leak sensitive data
  [ ] 错误消息不泄露敏感数据

## Secret Management
## 密钥管理

- NEVER hardcode secrets in source code
  永不在源代码中硬编码密钥
- ALWAYS use environment variables or a secret manager
  始终使用环境变量或密钥管理器
- Validate that required secrets are present at startup
  验证启动时所需的密钥是否存在
- Rotate any secrets that may have been exposed
  轮换任何可能已暴露的密钥

## Security Response Protocol
## 安全响应协议

If security issue found:
如果发现安全问题：

1. STOP immediately
   立即停止
2. Use **security-reviewer** agent
   使用 **security-reviewer** agent
3. Fix CRITICAL issues before continuing
   修复 CRITICAL 问题后再继续
4. Rotate any exposed secrets
   轮换任何已暴露的密钥
5. Review entire codebase for similar issues
   审查整个代码库中是否存在类似问题
