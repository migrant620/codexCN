# 沙盒和批准

本文档介绍了 Codex CLI 的沙盒功能和批准机制，这些功能确保代码执行的安全性。

## 概述

Codex CLI 使用沙盒环境来安全地执行代码和命令。沙盒限制了可能有害的操作，并提供了批准机制，让您可以控制哪些操作可以执行。

## 沙盒类型

Codex CLI 支持多种沙盒类型：

### 1. Docker 沙盒（推荐）

Docker 沙盒提供了最强的隔离性，在容器中执行所有操作。

#### 配置 Docker 沙盒

```toml
[sandbox]
enabled = true
type = "docker"
image = "codex-sandbox:latest"
```

#### 自定义 Docker 镜像

您可以创建自定义 Docker 镜像：

```dockerfile
# Dockerfile
FROM ubuntu:22.04

# 安装基本工具
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    nodejs \
    npm \
    git \
    curl \
    wget

# 安装额外的语言运行时
RUN pip install pandas numpy requests
RUN npm install -g typescript

# 设置工作目录
WORKDIR /workspace

# 设置用户
USER user
```

构建并使用自定义镜像：

```bash
docker build -t my-codex-sandbox .
```

```toml
[sandbox]
enabled = true
type = "docker"
image = "my-codex-sandbox:latest"
```

### 2. Linux 命名空间沙盒

在支持的 Linux 系统上，可以使用命名空间沙盒，它提供比 Docker 更轻量级的隔离。

#### 配置命名空间沙盒

```toml
[sandbox]
enabled = true
type = "namespace"
```

#### 要求

- Linux 内核 3.10+
- 用户命名空间支持
- 安装 `unshare` 命令

### 3. 无沙盒模式

在受信任的环境中，可以禁用沙盒以获得更好的性能。

#### 配置无沙盒模式

```toml
[sandbox]
enabled = false
```

## 批准机制

Codex CLI 在执行潜在危险操作之前会请求您的批准。

### 需要批准的操作

以下操作类型需要批准：

1. **文件系统操作**
   - 创建/删除文件
   - 修改重要文件
   - 访问敏感目录

2. **网络操作**
   - 外部网络请求
   - 端口监听
   - 网络扫描

3. **系统操作**
   - 执行系统命令
   - 安装软件包
   - 修改系统配置

4. **代码执行**
   - 运行用户代码
   - 执行脚本
   - 编译代码

### 批准流程

1. **检测危险操作**
   - Codex 检测到潜在危险操作
   - 分析操作的风险级别

2. **显示批准提示**
   - 显示操作的详细信息
   - 包括风险级别和影响

3. **用户决策**
   - 用户可以选择批准、拒绝或修改
   - 可以设置记住选择

4. **执行或跳过**
   - 如果批准，执行操作
   - 如果拒绝，跳过操作

### 批准示例

```
⚠️  需要批准的操作

操作: 删除文件
文件: /path/to/important/file.txt
风险级别: 高

原因: 此操作将永久删除文件

选择:
[y] 是的，执行此操作
[n] 不，跳过此操作
[m] 修改操作
[a] 全部批准（当前会话）
[d] 全部拒绝（当前会话）

您的选择: 
```

## 配置选项

### 全局沙盒配置

```toml
[sandbox]
# 启用沙盒
enabled = true

# 沙盒类型
type = "docker"  # "docker", "namespace", "none"

# Docker 镜像
image = "codex-sandbox:latest"

# 超时时间（秒）
timeout = 300

# 工作目录
work_dir = "/workspace"

# 环境变量
environment = { "PYTHONPATH" = "/workspace" }
```

### 批准配置

```toml
[approvals]
# 启用批准机制
enabled = true

# 自动批准低风险操作
auto_approve_low_risk = false

# 记住批准决定
remember_decisions = true

# 批准超时（秒）
timeout = 60

# 默认风险级别阈值
risk_threshold = "medium"  # "low", "medium", "high"
```

### 操作特定配置

```toml
[[approvals.rules]]
operation = "file_delete"
risk_level = "high"
auto_approve = false

[[approvals.rules]]
operation = "network_request"
risk_level = "medium"
auto_approve = false
```

## 安全最佳实践

### 1. 使用适当的沙盒类型

- **生产环境**: 使用 Docker 沙盒
- **开发环境**: 可以使用命名空间沙盒
- **受信任环境**: 可以禁用沙盒，但要谨慎

### 2. 定期更新沙盒镜像

```bash
# 更新 Docker 镜像
docker pull codex-sandbox:latest
```

### 3. 限制资源使用

```toml
[sandbox]
# 内存限制（MB）
memory_limit = 512

# CPU 限制
cpu_limit = 1.0

# 磁盘空间限制（MB）
disk_limit = 1024
```

### 4. 监控沙盒活动

```toml
[monitoring]
# 启用监控
enabled = true

# 日志级别
log_level = "info"

# 审计日志
audit_log = "/path/to/audit.log"

# 告警阈值
alert_threshold = 10
```

## 故障排除

### 常见问题

1. **Docker 沙盒启动失败**
   - 检查 Docker 是否已安装并运行
   - 验证镜像是否存在
   - 检查权限设置

2. **命名空间沙盒不可用**
   - 验证内核版本
   - 检查用户命名空间支持
   - 确认 `unshare` 命令可用

3. **批准提示不显示**
   - 检查批准是否已禁用
   - 验证配置文件语法
   - 查看日志文件

### 调试技巧

1. **启用详细日志**
   ```bash
   codex --verbose --log-level debug
   ```

2. **测试沙盒连接**
   ```bash
   codex --test-sandbox
   ```

3. **检查配置**
   ```bash
   codex --validate-config
   ```

4. **手动测试 Docker**
   ```bash
   docker run --rm codex-sandbox:latest echo "test"
   ```

## 高级用法

### 自定义沙盒脚本

您可以创建自定义沙盒脚本：

```bash
#!/bin/bash
# custom-sandbox.sh

# 设置限制
ulimit -t 300  # CPU 时间限制
ulimit -f 100  # 文件大小限制（MB）

# 设置环境
export PATH="/usr/local/bin:$PATH"

# 执行命令
exec "$@"
```

然后在配置中使用：

```toml
[sandbox]
enabled = true
type = "custom"
command = "/path/to/custom-sandbox.sh"
```

### 多阶段批准

对于复杂操作，可以配置多阶段批准：

```toml
[multi_stage_approvals]
enabled = true

[[multi_stage_approvals.stages]]
name = "planning"
description = "规划和分析阶段"
required = true

[[multi_stage_approvals.stages]]
name = "execution"
description = "执行阶段"
required = true
```

### 条件批准

基于上下文的条件批准：

```toml
[conditional_approvals]
# 基于文件路径
[[conditional_approvals.file_rules]]
pattern = "/tmp/*"
risk_level = "low"
auto_approve = true

# 基于命令
[[conditional_approvals.command_rules]]
pattern = "git status"
risk_level = "low"
auto_approve = true
```

## 下一步

了解沙盒功能后，您可以：

- 探索[高级功能](./advanced.md)
- 查看[配置选项](./config.md)
- 了解[身份验证](./authentication.md)

如果您遇到问题，请查看[常见问题](./faq.md)。