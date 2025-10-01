# 配置

Codex CLI 支持丰富的配置选项，允许您自定义其行为。配置存储在 `~/.codex/config.toml` 文件中。

## 配置文件位置

配置文件位于：

- **macOS/Linux**: `~/.codex/config.toml`
- **Windows**: `%USERPROFILE%\.codex\config.toml`

## 基本配置

### 身份验证配置

```toml
[auth]
# 强制使用特定的身份验证方法
method = "chatgpt"  # 或 "api_key"

# API Key（如果使用 API Key 身份验证）
api_key = "your-api-key-here"
```

### 模型配置

```toml
[model]
# 使用的模型
name = "gpt-4"

# 温度参数（0.0 到 2.0）
temperature = 0.7

# 最大令牌数
max_tokens = 4000
```

### CLI 配置

```toml
[cli]
# 默认提示
default_prompt = "帮助我改进这段代码"

# 是否自动批准操作
auto_approve = false

# 详细输出
verbose = false
```

## 高级配置

### 沙盒配置

```toml
[sandbox]
# 启用沙盒
enabled = true

# 沙盒类型（"docker", "none"）
type = "docker"

# Docker 镜像
image = "codex-sandbox:latest"
```

### MCP 配置

```toml
[mcp_servers]
# 启用 MCP 服务器
enabled = true

# 服务器配置
[[mcp_servers.servers]]
name = "filesystem"
command = "npx"
args = ["@modelcontextprotocol/server-filesystem", "/path/to/project"]

[[mcp_servers.servers]]
name = "git"
command = "npx"
args = ["@modelcontextprotocol/server-git", "/path/to/project"]
```

### 代理配置

```toml
[proxy]
# HTTP 代理
http = "http://proxy.example.com:8080"

# HTTPS 代理
https = "https://proxy.example.com:8080"

# 不使用代理的主机
no_proxy = "localhost,127.0.0.1"
```

## 环境变量

您可以使用环境变量覆盖配置文件中的设置：

```bash
# 身份验证
export OPENAI_API_KEY="your-api-key-here"

# 模型设置
export CODEX_MODEL="gpt-4"
export CODEX_TEMPERATURE="0.7"

# 代理设置
export HTTP_PROXY="http://proxy.example.com:8080"
export HTTPS_PROXY="https://proxy.example.com:8080"
export NO_PROXY="localhost,127.0.0.1"
```

## 命令行选项

您可以使用命令行选项临时覆盖配置：

```bash
# 使用特定的模型
codex --model gpt-4

# 设置温度
codex --temperature 0.5

# 启用详细输出
codex --verbose

# 使用特定的 API Key
codex --api-key your-api-key-here
```

## 配置示例

### 完整配置示例

```toml
[auth]
method = "chatgpt"

[model]
name = "gpt-4"
temperature = 0.7
max_tokens = 4000

[cli]
default_prompt = "帮助我改进这段代码"
auto_approve = false
verbose = false

[sandbox]
enabled = true
type = "docker"
image = "codex-sandbox:latest"

[mcp_servers]
enabled = true

[[mcp_servers.servers]]
name = "filesystem"
command = "npx"
args = ["@modelcontextprotocol/server-filesystem", "/path/to/project"]

[[mcp_servers.servers]]
name = "git"
command = "npx"
args = ["@modelcontextprotocol/server-git", "/path/to/project"]

[proxy]
http = "http://proxy.example.com:8080"
https = "https://proxy.example.com:8080"
no_proxy = "localhost,127.0.0.1"
```

### 开发环境配置

```toml
[model]
name = "gpt-4"
temperature = 0.3  # 更低的温度以获得更一致的输出

[cli]
verbose = true
auto_approve = true  # 在开发环境中自动批准

[sandbox]
enabled = false  # 在开发环境中禁用沙盒以提高性能
```

### 生产环境配置

```toml
[model]
name = "gpt-4"
temperature = 0.7

[cli]
auto_approve = false  # 在生产环境中需要手动批准

[sandbox]
enabled = true
type = "docker"
```

## 配置验证

您可以使用以下命令验证您的配置：

```bash
codex --validate-config
```

这将检查配置文件的语法和有效性，并报告任何错误。

## 重置配置

如果您需要重置配置，可以删除配置文件：

```bash
# macOS/Linux
rm ~/.codex/config.toml

# Windows
del %USERPROFILE%\.codex\config.toml
```

下次运行 `codex` 时，将使用默认设置创建新的配置文件。

## 配置最佳实践

1. **版本控制**: 不要将配置文件提交到版本控制系统
2. **安全性**: 不要在配置文件中存储敏感信息
3. **环境特定**: 为不同的环境（开发、测试、生产）使用不同的配置
4. **文档**: 为您的团队记录配置选项和最佳实践
5. **备份**: 定期备份重要的配置文件

## 故障排除

### 配置文件问题

如果配置文件有问题：

1. 检查 TOML 语法
2. 使用 `codex --validate-config` 验证配置
3. 查看错误消息以了解具体问题
4. 临时删除配置文件以使用默认设置

### 环境变量问题

如果环境变量不起作用：

1. 确保变量已正确导出
2. 检查变量名称是否正确
3. 重启终端或重新加载配置
4. 使用 `env | grep CODEX` 检查环境变量

## 下一步

配置完成后，您可以：

- 探索[高级功能](./advanced.md)
- 了解[沙盒功能](./sandbox.md)
- 查看[身份验证选项](./authentication.md)

如果您遇到问题，请查看[常见问题](./faq.md)。