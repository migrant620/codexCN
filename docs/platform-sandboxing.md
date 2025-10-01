# 平台沙盒

本文档介绍了 Codex CLI 在不同平台上的沙盒实现和安全机制。

## 概述

Codex CLI 使用平台特定的沙盒技术来确保代码执行的安全性。不同的操作系统提供不同的沙盒机制，Codex CLI 会自动选择最适合的方案。

## 支持的平台

### macOS

#### 沙盒技术

在 macOS 上，Codex CLI 使用以下沙盒技术：

1. **App Sandbox**
   - 原生 macOS 沙盒机制
   - 提供文件系统、网络和系统资源的隔离
   - 需要适当的权限配置

2. **Docker 容器**
   - 提供更强的隔离性
   - 支持自定义环境
   - 适合复杂的项目

#### 配置

```toml
[sandbox]
# macOS 特定配置
platform = "macos"
type = "app_sandbox"  # 或 "docker"

# App Sandbox 权限
[sandbox.macos]
# 文件访问权限
file_access = "selected"  # "none", "selected", "all"

# 网络访问权限
network_access = "outbound"  # "none", "outbound", "all"

# 系统资源限制
resource_limits = {
    memory = "512MB",
    cpu = "50%"
}
```

#### 权限设置

macOS App Sandbox 需要明确的权限声明：

```xml
<!-- Entitlements.plist -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.files.user-selected.read-only</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
</dict>
</plist>
```

### Linux

#### 沙盒技术

Linux 平台支持多种沙盒技术：

1. **命名空间隔离**
   - 使用 Linux 命名空间
   - 轻量级且高效
   - 无需额外依赖

2. **Docker 容器**
   - 完整的容器化解决方案
   - 提供最强的隔离性
   - 支持 Docker 生态系统

3. **Firejail**
   - 专门的安全沙盒工具
   - 配置灵活
   - 低资源占用

#### 配置

```toml
[sandbox]
# Linux 特定配置
platform = "linux"
type = "namespaces"  # "docker", "firejail"

# 命名空间配置
[sandbox.linux.namespaces]
# 启用的命名空间
enabled = ["pid", "mount", "network", "uts"]

# 挂载点
mounts = [
    { type = "proc", source = "proc", target = "/proc" },
    { type = "tmpfs", source = "tmpfs", target = "/tmp" }
]

# 网络配置
network = {
    interface = "lo",
    disable_ipv6 = true
}
```

#### Firejail 配置

```toml
[sandbox]
platform = "linux"
type = "firejail"

[sandbox.linux.firejail]
# Firejail 选项
private_bin = true
private_etc = true
private_tmp = true
seccomp = true
caps_drop = "all"
```

### Windows

#### 沙盒技术

Windows 平台的沙盒实现：

1. **Windows Sandbox**
   - 原生 Windows 沙盒功能
   - 完整的操作系统隔离
   - 需要 Windows 10/11 Pro 或 Enterprise

2. **Docker Desktop**
   - 使用 WSL2 后端
   - 与 Linux 容器兼容
   - 适合开发环境

3. **AppContainer**
   - 轻量级应用隔离
   - 集成度高
   - 适合生产环境

#### 配置

```toml
[sandbox]
# Windows 特定配置
platform = "windows"
type = "windows_sandbox"  # "docker", "appcontainer"

# Windows Sandbox 配置
[sandbox.windows.sandbox]
# 内存限制
memory = "4GB"

# CPU 使用限制
cpu_count = 2

# 网络配置
networking = "disable"  # "disable", "enable"

# 映射文件夹
mapped_folders = [
    { host = "C:\\Users\\user\\project", sandbox = "C:\\project", readOnly = false }
]
```

#### AppContainer 配置

```toml
[sandbox]
platform = "windows"
type = "appcontainer"

[sandbox.windows.appcontainer]
# 容器名称
name = "CodexSandbox"

# 能力
capabilities = [
    "internetClient",
    "documentsLibrary",
    "picturesLibrary"
]

# 资源限制
resource_quotas = {
    memory = "512MB",
    cpu = "50%"
}
```

## 通用沙盒功能

### 文件系统隔离

所有平台都提供文件系统隔离：

```toml
[sandbox.filesystem]
# 只读目录
read_only_dirs = [
    "/usr/bin",
    "/usr/lib",
    "/System"
]

# 读写目录
read_write_dirs = [
    "/tmp",
    "/workspace"
]

# 禁止访问的目录
blocked_dirs = [
    "/etc",
    "/var",
    "/home"
]

# 临时文件系统
tmpfs = [
    "/tmp",
    "/var/tmp"
]
```

### 网络隔离

网络访问控制：

```toml
[sandbox.network]
# 启用网络
enabled = true

# 网络类型
type = "outbound"  # "none", "outbound", "all"

# 允许的主机
allowed_hosts = [
    "api.openai.com",
    "github.com"
]

# 禁止的主机
blocked_hosts = [
    "malicious.com",
    "suspicious.net"
]

# 端口限制
allowed_ports = [443, 80]
blocked_ports = [22, 23]
```

### 系统资源限制

资源使用限制：

```toml
[sandbox.resources]
# 内存限制
memory = {
    limit = "512MB",
    swap = "256MB"
}

# CPU 限制
cpu = {
    quota = "50%",
    period = "100ms"
}

# 存储限制
storage = {
    size = "1GB",
    inodes = 10000
}

# 进程限制
processes = {
    max_count = 100,
    max_threads = 500
}

# 文件描述符限制
file_descriptors = {
    soft_limit = 1024,
    hard_limit = 4096
}
```

## 安全最佳实践

### 1. 最小权限原则

```toml
[sandbox]
# 仅启用必要的功能
enabled = true

# 使用最严格的沙盒类型
type = "docker"  # 最严格的选项

# 限制访问范围
[sandbox.filesystem]
read_only_dirs = ["/workspace"]
read_write_dirs = ["/tmp"]
blocked_dirs = ["*"]
```

### 2. 网络安全

```toml
[sandbox.network]
# 限制网络访问
enabled = true
type = "outbound"

# 仅允许必要的域名
allowed_hosts = ["api.openai.com"]

# 禁止敏感端口
blocked_ports = [22, 23, 3389]
```

### 3. 资源控制

```toml
[sandbox.resources]
# 设置合理的资源限制
memory = { limit = "512MB" }
cpu = { quota = "50%" }

# 限制进程数量
processes = { max_count = 50 }

# 限制文件描述符
file_descriptors = { soft_limit = 512 }
```

### 4. 审计和监控

```toml
[monitoring]
# 启用审计日志
audit_enabled = true

# 日志级别
log_level = "info"

# 审计事件
audit_events = [
    "file_access",
    "network_connection",
    "process_execution",
    "system_call"
]

# 告警阈值
alert_thresholds = {
    memory_usage = 90,
    cpu_usage = 80,
    network_connections = 100
}
```

## 故障排除

### 常见问题

#### 1. 沙盒启动失败

**症状**: 无法启动沙盒环境

**可能原因**:
- 权限不足
- 依赖缺失
- 配置错误

**解决方案**:
```bash
# 检查权限
codex --check-permissions

# 验证依赖
codex --check-dependencies

# 测试沙盒
codex --test-sandbox
```

#### 2. 文件访问被拒绝

**症状**: 无法访问项目文件

**可能原因**:
- 文件权限配置错误
- 路径映射问题
- 沙盒限制过严

**解决方案**:
```toml
[sandbox.filesystem]
# 添加项目目录
read_write_dirs = ["/path/to/project"]

# 检查路径映射
mapped_folders = [
    { host = "/path/to/project", sandbox = "/workspace", readOnly = false }
]
```

#### 3. 网络连接失败

**症状**: 无法连接到外部服务

**可能原因**:
- 网络配置错误
- 防火墙阻止
- DNS 问题

**解决方案**:
```toml
[sandbox.network]
# 检查网络配置
enabled = true
type = "outbound"

# 添加必要的主机
allowed_hosts = ["api.openai.com", "github.com"]
```

### 调试工具

#### 1. 沙盒诊断

```bash
# 运行沙盒诊断
codex --diagnose-sandbox

# 查看沙盒状态
codex --sandbox-status

# 测试沙盒功能
codex --test-sandbox --verbose
```

#### 2. 日志分析

```bash
# 查看沙盒日志
codex --show-sandbox-logs

# 过滤错误日志
codex --show-sandbox-logs --level error

# 实时监控日志
codex --monitor-sandbox --follow
```

#### 3. 配置验证

```bash
# 验证配置文件
codex --validate-config

# 检查沙盒配置
codex --check-sandbox-config

# 测试特定配置
codex --test-config --section sandbox
```

## 性能优化

### 1. 资源优化

```toml
[sandbox.resources]
# 根据系统资源调整
memory = { limit = "1024MB" }  # 增加内存限制
cpu = { quota = "75%" }        # 增加 CPU 配额

# 优化文件系统
[sandbox.filesystem]
# 使用内存文件系统
tmpfs = ["/tmp", "/var/tmp"]

# 预分配资源
preallocate_resources = true
```

### 2. 缓存优化

```toml
[cache]
# 启用沙盒缓存
enabled = true

# 缓存配置
[cache.sandbox]
# 缓存目录
dir = "~/.codex/cache/sandbox"

# 缓存大小
max_size = "1GB"

# 缓存策略
strategy = "lru"  # "lru", "fifo", "random"

# 缓存过期
ttl = 3600  # 秒
```

### 3. 并发优化

```toml
[performance]
# 并发沙盒实例
max_concurrent_sandboxes = 3

# 沙盒池配置
[sandbox_pool]
enabled = true
min_instances = 1
max_instances = 5
idle_timeout = 300  # 秒
```

## 平台特定注意事项

### macOS 注意事项

1. **权限申请**
   - 首次运行时需要用户授权
   - 可能需要管理员权限
   - 系统偏好设置中的安全设置

2. **性能考虑**
   - App Sandbox 比 Docker 更轻量
   - 适合快速开发和测试
   - 生产环境推荐使用 Docker

### Linux 注意事项

1. **内核要求**
   - 需要支持用户命名空间
   - 建议使用较新的内核版本
   - 某些发行版需要额外配置

2. **安全模块**
   - SELinux 可能影响沙盒功能
   - AppArmor 需要适当配置
   - 防火墙规则需要调整

### Windows 注意事项

1. **版本要求**
   - Windows Sandbox 需要 Pro 或 Enterprise 版本
   - Docker Desktop 需要 WSL2
   - 建议使用 Windows 10/11 最新版本

2. **权限配置**
   - 可能需要管理员权限
   - 防病毒软件可能影响沙盒
   - 需要配置 Windows Defender

## 下一步

了解平台沙盒功能后，您可以：

- 探索[高级功能](./advanced.md)
- 查看[配置选项](./config.md)
- 了解[安全最佳实践](./sandbox.md)

如果您遇到问题，请查看[常见问题](./faq.md)或[贡献指南](./contributing.md)。