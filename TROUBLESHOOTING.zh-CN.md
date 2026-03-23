# Troubleshooting Guide
# 故障排除指南

Common issues and solutions for Everything Claude Code (ECC) plugin.
Everything Claude Code (ECC) 插件的常见问题和解决方案。

## Table of Contents
## 目录

- [Memory & Context Issues](#memory--context-issues)
- [Agent Harness Failures](#agent-harness-failures)
- [Hook & Workflow Errors](#hook--workflow-errors)
- [Installation & Setup](#installation--setup)
- [Performance Issues](#performance-issues)
- [Common Error Messages](#common-error-messages)
- [Getting Help](#getting-help)
- [内存和上下文问题](#内存和上下文问题)
- [Agent Harness 故障](#agent-harness-故障)
- [Hook 和工作流错误](#hook-和工作流错误)
- [安装和设置](#安装和设置)
- [性能问题](#性能问题)
- [常见错误消息](#常见错误消息)
- [获取帮助](#获取帮助)

---

## Memory & Context Issues
## 内存和上下文问题

### Context Window Overflow
### 上下文窗口溢出

**Symptom:** "Context too long" errors or incomplete responses
**症状**: "Context too long" 错误或不完整响应

**Causes:**
**原因：**
- Large file uploads exceeding token limits
- 超过 token 限制的大文件上传
- Accumulated conversation history
- 累积的会话历史
- Multiple large tool outputs in single session
- 单个会话中多个大型工具输出

**Solutions:**
**解决方案：**
```bash
# 1. Clear conversation history and start fresh
# Use Claude Code: "New Chat" or Cmd/Ctrl+Shift+N
# 1. 清除会话历史并重新开始
# 使用 Claude Code: "New Chat" 或 Cmd/Ctrl+Shift+N

# 2. Reduce file size before analysis
head -n 100 large-file.log > sample.log
# 2. 分析前减小文件大小
head -n 100 large-file.log > sample.log

# 3. Use streaming for large outputs
head -n 50 large-file.txt
# 3. 大输出使用流式处理
head -n 50 large-file.txt

# 4. Split tasks into smaller chunks
# Instead of: "Analyze all 50 files"
# Use: "Analyze files in src/components/ directory"
# 4. 将任务分成小块
# 不要用: "分析所有 50 个文件"
# 使用: "分析 src/components/ 目录中的文件"
```

### Memory Persistence Failures
### 内存持久化失败

**Symptom:** Agent doesn't remember previous context or observations
**症状**: Agent 不记得之前的上下文或观察

**Causes:**
**原因：**
- Disabled continuous-learning hooks
- 禁用了持续学习 hooks
- Corrupted observation files
- 损坏的观察文件
- Project detection failures
- 项目检测失败

**Solutions:**
**解决方案：**
```bash
# Check if observations are being recorded
ls ~/.claude/homunculus/projects/*/observations.jsonl
# 检查是否正在记录观察
ls ~/.claude/homunculus/projects/*/observations.jsonl

# Find the current project's hash id
python3 - <<'PY'
import json, os
registry_path = os.path.expanduser("~/.claude/homunculus/projects.json")
with open(registry_path) as f:
    registry = json.load(f)
for project_id, meta in registry.items():
    if meta.get("root") == os.getcwd():
        print(project_id)
        break
else:
    raise SystemExit("Project hash not found in ~/.claude/homunculus/projects.json")
PY
# 找到当前项目的哈希 ID
python3 - <<'PY'
import json, os
registry_path = os.path.expanduser("~/.claude/homunculus/projects.json")
with open(registry_path) as f:
    registry = json.load(f)
for project_id, meta in registry.items():
    if meta.get("root") == os.getcwd():
        print(project_id)
        break
else:
    raise SystemExit("Project hash not found in ~/.claude/homunculus/projects.json")
PY

# View recent observations for that project
tail -20 ~/.claude/homunculus/projects/<project-hash>/observations.jsonl
# 查看该项目的最近观察
tail -20 ~/.claude/homunculus/projects/<project-hash>/observations.jsonl

# Back up a corrupted observations file before recreating it
mv ~/.claude/homunculus/projects/<project-hash>/observations.jsonl \
  ~/.claude/homunculus/projects/<project-hash>/observations.jsonl.bak.$(date +%Y%m%d-%H%M%S)
# 在重新创建损坏的观察文件之前先备份
mv ~/.claude/homunculus/projects/<project-hash>/observations.jsonl \
  ~/.claude/homunculus/projects/<project-hash>/observations.jsonl.bak.$(date +%Y%m%d-%H%M%S)

# Verify hooks are enabled
grep -r "observe" ~/.claude/settings.json
# 验证 hooks 已启用
grep -r "observe" ~/.claude/settings.json
```

---

## Agent Harness Failures
## Agent Harness 故障

### Agent Not Found
### Agent 未找到

**Symptom:** "Agent not loaded" or "Unknown agent" errors
**症状**: "Agent not loaded" 或 "Unknown agent" 错误

**Causes:**
**原因：**
- Plugin not installed correctly
- 插件未正确安装
- Agent path misconfiguration
- Agent 路径配置错误
- Marketplace vs manual install mismatch
- Marketplace 与手动安装不匹配

**Solutions:**
**解决方案：**
```bash
# Check plugin installation
ls ~/.claude/plugins/cache/
# 检查插件安装
ls ~/.claude/plugins/cache/

# Verify agent exists (marketplace install)
ls ~/.claude/plugins/cache/*/agents/
# 验证 agent 存在（marketplace 安装）
ls ~/.claude/plugins/cache/*/agents/

# For manual install, agents should be in:
ls ~/.claude/agents/  # Custom agents only
# 对于手动安装，agents 应该在：
ls ~/.claude/agents/  # 仅自定义 agents

# Reload plugin
# Claude Code → Settings → Extensions → Reload
# 重新加载插件
# Claude Code → Settings → Extensions → Reload
```

### Workflow Execution Hangs
### 工作流执行挂起

**Symptom:** Agent starts but never completes
**症状**: Agent 启动但永不完成

**Causes:**
**原因：**
- Infinite loops in agent logic
- Agent 逻辑中的无限循环
- Blocked on user input
- 阻塞在用户输入
- Network timeout waiting for API
- 等待 API 的网络超时

**Solutions:**
**解决方案：**
```bash
# 1. Check for stuck processes
ps aux | grep claude
# 1. 检查卡住的进程
ps aux | grep claude

# 2. Enable debug mode
export CLAUDE_DEBUG=1
# 2. 启用调试模式
export CLAUDE_DEBUG=1

# 3. Set shorter timeouts
export CLAUDE_TIMEOUT=30
# 3. 设置更短的超时
export CLAUDE_TIMEOUT=30

# 4. Check network connectivity
curl -I https://api.anthropic.com
# 4. 检查网络连接
curl -I https://api.anthropic.com
```

### Tool Use Errors
### 工具使用错误

**Symptom:** "Tool execution failed" or permission denied
**症状**: "Tool execution failed" 或权限被拒绝

**Causes:**
**原因：**
- Missing dependencies (npm, python, etc.)
- 缺少依赖（npm, python 等）
- Insufficient file permissions
- 文件权限不足
- Path not found
- 路径未找到

**Solutions:**
**解决方案：**
```bash
# Verify required tools are installed
which node python3 npm git
# 验证所需工具已安装
which node python3 npm git

# Fix permissions on hook scripts
chmod +x ~/.claude/plugins/cache/*/hooks/*.sh
chmod +x ~/.claude/plugins/cache/*/skills/*/hooks/*.sh
# 修复 hook 脚本权限
chmod +x ~/.claude/plugins/cache/*/hooks/*.sh
chmod +x ~/.claude/plugins/cache/*/skills/*/hooks/*.sh

# Check PATH includes necessary binaries
echo $PATH
# 检查 PATH 包含必要的二进制文件
echo $PATH
```

---

## Hook & Workflow Errors
## Hook 和工作流错误

### Hooks Not Firing
### Hook 不触发

**Symptom:** Pre/post hooks don't execute
**症状**: 前后 hooks 不执行

**Causes:**
**原因：**
- Hooks not registered in settings.json
- Hooks 未在 settings.json 中注册
- Invalid hook syntax
- Hook 语法无效
- Hook script not executable
- Hook 脚本不可执行

**Solutions:**
**解决方案：**
```bash
# Check hooks are registered
grep -A 10 '"hooks"' ~/.claude/settings.json
# 检查 hooks 是否已注册
grep -A 10 '"hooks"' ~/.claude/settings.json

# Verify hook files exist and are executable
ls -la ~/.claude/plugins/cache/*/hooks/
# 验证 hook 文件存在且可执行
ls -la ~/.claude/plugins/cache/*/hooks/

# Test hook manually
bash ~/.claude/plugins/cache/*/hooks/pre-bash.sh <<< '{"command":"echo test"}'
# 手动测试 hook
bash ~/.claude/plugins/cache/*/hooks/pre-bash.sh <<< '{"command":"echo test"}'

# Re-register hooks (if using plugin)
# Disable and re-enable plugin in Claude Code settings
# 重新注册 hooks（如果使用插件）
# 在 Claude Code 设置中禁用并重新启用插件
```

### Python/Node Version Mismatches
### Python/Node 版本不匹配

**Symptom:** "python3 not found" or "node: command not found"
**症状**: "python3 not found" 或 "node: command not found"

**Causes:**
**原因：**
- Missing Python/Node installation
- 缺少 Python/Node 安装
- PATH not configured
- PATH 未配置
- Wrong Python version (Windows)
- Python 版本错误（Windows）

**Solutions:**
**解决方案：**
```bash
# Install Python 3 (if missing)
# macOS: brew install python3
# Ubuntu: sudo apt install python3
# Windows: Download from python.org
# 安装 Python 3（如果缺少）
# macOS: brew install python3
# Ubuntu: sudo apt install python3
# Windows: 从 python.org 下载

# Install Node.js (if missing)
# macOS: brew install node
# Ubuntu: sudo apt install nodejs npm
# Windows: Download from nodejs.org
# 安装 Node.js（如果缺少）
# macOS: brew install node
# Ubuntu: sudo apt install nodejs npm
# Windows: 从 nodejs.org 下载

# Verify installations
python3 --version
node --version
npm --version
# 验证安装
python3 --version
node --version
npm --version

# Windows: Ensure python (not python3) works
python --version
# Windows: 确保 python（不是 python3）可用
python --version
```

### Dev Server Blocker False Positives
### Dev Server 阻止误报

**Symptom:** Hook blocks legitimate commands mentioning "dev"
**症状**: Hook 阻止包含 "dev" 的合法命令

**Causes:**
**原因：**
- Heredoc content triggering pattern match
- Heredoc 内容触发模式匹配
- Non-dev commands with "dev" in arguments
- 参数中包含 "dev" 的非开发命令

**Solutions:**
**解决方案：**
```bash
# This is fixed in v1.8.0+ (PR #371)
# Upgrade plugin to latest version
# 这在 v1.8.0+ 中已修复（PR #371）
# 升级到最新版本的插件

# Workaround: Wrap dev servers in tmux
tmux new-session -d -s dev "npm run dev"
tmux attach -t dev
# 变通方案：将开发服务器包装在 tmux 中
tmux new-session -d -s dev "npm run dev"
tmux attach -t dev

# Disable hook temporarily if needed
# Edit ~/.claude/settings.json and remove pre-bash hook
# 如需要可临时禁用 hook
# 编辑 ~/.claude/settings.json 并移除 pre-bash hook
```

---

## Installation & Setup
## 安装和设置

### Plugin Not Loading
### 插件未加载

**Symptom:** Plugin features unavailable after install
**症状**: 安装后插件功能不可用

**Causes:**
**原因：**
- Marketplace cache not updated
- Marketplace 缓存未更新
- Claude Code version incompatibility
- Claude Code 版本不兼容
- Corrupted plugin files
- 插件文件损坏

**Solutions:**
**解决方案：**
```bash
# Inspect the plugin cache before changing it
ls -la ~/.claude/plugins/cache/
# 在更改缓存前检查插件缓存
ls -la ~/.claude/plugins/cache/

# Back up the plugin cache instead of deleting it in place
mv ~/.claude/plugins/cache ~/.claude/plugins/cache.backup.$(date +%Y%m%d-%H%M%S)
mkdir -p ~/.claude/plugins/cache
# 不要就地删除，而是备份插件缓存
mv ~/.claude/plugins/cache ~/.claude/plugins/cache.backup.$(date +%Y%m%d-%H%M%S)
mkdir -p ~/.claude/plugins/cache

# Reinstall from marketplace
# Claude Code → Extensions → Everything Claude Code → Uninstall
# Then reinstall from marketplace
# 从 marketplace 重新安装
# Claude Code → Extensions → Everything Claude Code → Uninstall
# 然后从 marketplace 重新安装

# Check Claude Code version
claude --version
# Requires Claude Code 2.0+
# 检查 Claude Code 版本
claude --version
# 需要 Claude Code 2.0+

# Manual install (if marketplace fails)
git clone https://github.com/affaan-m/everything-claude-code.git
cp -r everything-claude-code ~/.claude/plugins/ecc
# 手动安装（如果 marketplace 失败）
git clone https://github.com/affaan-m/everything-claude-code.git
cp -r everything-claude-code ~/.claude/plugins/ecc
```

### Package Manager Detection Fails
### 包管理器检测失败

**Symptom:** Wrong package manager used (npm instead of pnpm)
**症状**: 使用的包管理器错误（npm 而不是 pnpm）

**Causes:**
**原因：**
- No lock file present
- 没有 lock 文件
- CLAUDE_PACKAGE_MANAGER not set
- 未设置 CLAUDE_PACKAGE_MANAGER
- Multiple lock files confusing detection
- 多个 lock 文件混淆检测

**Solutions:**
**解决方案：**
```bash
# Set preferred package manager globally
export CLAUDE_PACKAGE_MANAGER=pnpm
# Add to ~/.bashrc or ~/.zshrc
# 全局设置首选包管理器
export CLAUDE_PACKAGE_MANAGER=pnpm
# 添加到 ~/.bashrc 或 ~/.zshrc

# Or set per-project
echo '{"packageManager": "pnpm"}' > .claude/package-manager.json
# 或按项目设置
echo '{"packageManager": "pnpm"}' > .claude/package-manager.json

# Or use package.json field
npm pkg set packageManager="pnpm@8.15.0"
# 或使用 package.json 字段
npm pkg set packageManager="pnpm@8.15.0"

# Warning: removing lock files can change installed dependency versions.
# Commit or back up the lock file first, then run a fresh install and re-run CI.
# Only do this when intentionally switching package managers.
rm package-lock.json  # If using pnpm/yarn/bun
# 警告：删除 lock 文件可能会更改已安装的依赖版本。
# 先提交或备份 lock 文件，然后运行全新安装并重新运行 CI。
# 只有在有意切换包管理器时才执行此操作。
rm package-lock.json  # 如果使用 pnpm/yarn/bun
```

---

## Performance Issues
## 性能问题

### Slow Response Times
### 响应时间慢

**Symptom:** Agent takes 30+ seconds to respond
**症状**: Agent 需要 30+ 秒响应

**Causes:**
**原因：**
- Large observation files
- 大型观察文件
- Too many active hooks
- 太多活动 hooks
- Network latency to API
- 到 API 的网络延迟

**Solutions:**
**解决方案：**
```bash
# Archive large observations instead of deleting them
archive_dir="$HOME/.claude/homunculus/archive/$(date +%Y%m%d)"
mkdir -p "$archive_dir"
find ~/.claude/homunculus/projects -name "observations.jsonl" -size +10M -exec sh -c '
  for file do
    base=$(basename "$(dirname "$file")")
    gzip -c "$file" > "'"$archive_dir"'/${base}-observations.jsonl.gz"
    : > "$file"
  done
' sh {} +
# 归档大型观察而不是删除它们
archive_dir="$HOME/.claude/homunculus/archive/$(date +%Y%m%d)"
mkdir -p "$archive_dir"
find ~/.claude/homunculus/projects -name "observations.jsonl" -size +10M -exec sh -c '
  for file do
    base=$(basename "$(dirname "$file")")
    gzip -c "$file" > "'"$archive_dir"'/${base}-observations.jsonl.gz"
    : > "$file"
  done
' sh {} +

# Disable unused hooks temporarily
# Edit ~/.claude/settings.json
# 临时禁用未使用的 hooks
# 编辑 ~/.claude/settings.json

# Keep active observation files small
# Large archives should live under ~/.claude/homunculus/archive/
# 保持活动观察文件较小
# 大归档应存放在 ~/.claude/homunculus/archive/
```

### High CPU Usage
### CPU 使用率高

**Symptom:** Claude Code consuming 100% CPU
**症状**: Claude Code 消耗 100% CPU

**Causes:**
**原因：**
- Infinite observation loops
- 无限观察循环
- File watching on large directories
- 大目录上的文件监视
- Memory leaks in hooks
- Hooks 中的内存泄漏

**Solutions:**
**解决方案：**
```bash
# Check for runaway processes
top -o cpu | grep claude
# 检查失控进程
top -o cpu | grep claude

# Disable continuous learning temporarily
touch ~/.claude/homunculus/disabled
# 临时禁用持续学习
touch ~/.claude/homunculus/disabled

# Restart Claude Code
# Cmd/Ctrl+Q then reopen
# 重启 Claude Code
# Cmd/Ctrl+Q 然后重新打开

# Check observation file size
du -sh ~/.claude/homunculus/*/
# 检查观察文件大小
du -sh ~/.claude/homunculus/*/
```

---

## Common Error Messages
## 常见错误消息

### "EACCES: permission denied"
### "EACCES: permission denied"

```bash
# Fix hook permissions
find ~/.claude/plugins -name "*.sh" -exec chmod +x {} \;
# 修复 hook 权限
find ~/.claude/plugins -name "*.sh" -exec chmod +x {} \;

# Fix observation directory permissions
chmod -R u+rwX,go+rX ~/.claude/homunculus
# 修复观察目录权限
chmod -R u+rwX,go+rX ~/.claude/homunculus
```

### "MODULE_NOT_FOUND"
### "MODULE_NOT_FOUND"

```bash
# Install plugin dependencies
cd ~/.claude/plugins/cache/everything-claude-code
npm install
# 安装插件依赖
cd ~/.claude/plugins/cache/everything-claude-code
npm install

# Or for manual install
cd ~/.claude/plugins/ecc
npm install
# 或手动安装
cd ~/.claude/plugins/ecc
npm install
```

### "spawn UNKNOWN"
### "spawn UNKNOWN"

```bash
# Windows-specific: Ensure scripts use correct line endings
# Convert CRLF to LF
find ~/.claude/plugins -name "*.sh" -exec dos2unix {} \;
# Windows 特定：确保脚本使用正确的行尾
# 将 CRLF 转换为 LF
find ~/.claude/plugins -name "*.sh" -exec dos2unix {} \;

# Or install dos2unix
# macOS: brew install dos2unix
# Ubuntu: sudo apt install dos2unix
# 或安装 dos2unix
# macOS: brew install dos2unix
# Ubuntu: sudo apt install dos2unix
```

---

## Getting Help
## 获取帮助

If you're still experiencing issues:
如果您仍然遇到问题：

1. **Check GitHub Issues**: [github.com/affaan-m/everything-claude-code/issues](https://github.com/affaan-m/everything-claude-code/issues)
1. **检查 GitHub Issues**: [github.com/affaan-m/everything-claude-code/issues](https://github.com/affaan-m/everything-claude-code/issues)
2. **Enable Debug Logging**:
2. **启用调试日志**:
   ```bash
   export CLAUDE_DEBUG=1
   export CLAUDE_LOG_LEVEL=debug
   export CLAUDE_DEBUG=1
   export CLAUDE_LOG_LEVEL=debug
   ```
3. **Collect Diagnostic Info**:
3. **收集诊断信息**:
   ```bash
   claude --version
   node --version
   python3 --version
   echo $CLAUDE_PACKAGE_MANAGER
   ls -la ~/.claude/plugins/cache/
   claude --version
   node --version
   python3 --version
   echo $CLAUDE_PACKAGE_MANAGER
   ls -la ~/.claude/plugins/cache/
   ```
4. **Open an Issue**: Include debug logs, error messages, and diagnostic info
4. **Opening an Issue**: 包含调试日志、错误消息和诊断信息

---

## Related Documentation
## 相关文档

- [README.md](./README.md) - Installation and features
- [CONTRIBUTING.md](./CONTRIBUTING.md) - Development guidelines
- [docs/](./docs/) - Detailed documentation
- [examples/](./examples/) - Usage examples
- [README.md](./README.md) - 安装和功能
- [CONTRIBUTING.md](./CONTRIBUTING.md) - 开发指南
- [docs/](./docs/) - 详细文档
- [examples/](./examples/) - 使用示例
