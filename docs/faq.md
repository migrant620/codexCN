# 常见问题

本文档回答了关于 Codex CLI 的常见问题。

## 一般问题

### 什么是 Codex CLI？

Codex CLI 是来自 OpenAI 的编码代理，可在您的计算机上本地运行。它可以帮助您编写、编辑和分析代码，提供智能建议和自动化任务。

### Codex CLI 与 Codex Web 有什么区别？

- **Codex CLI**: 在您的计算机上本地运行的命令行工具
- **Codex Web**: 基于云的 Web 版本，在 chatgpt.com/codex 上可用

### Codex CLI 是免费的吗？

Codex CLI 本身是免费的，但使用它需要：

- ChatGPT Plus、Pro、Team、Edu 或 Enterprise 订阅，或者
- OpenAI API Key（按使用量计费）

## 安装和设置

### 如何安装 Codex CLI？

您可以通过以下方式安装：

```bash
# 使用 npm
npm install -g @openai/codex

# 使用 Homebrew
brew install codex
```

### 安装后如何验证安装？

运行以下命令：

```bash
codex --version
```

如果看到版本号，说明安装成功。

### 支持哪些操作系统？

Codex CLI 支持：

- macOS（Intel 和 Apple Silicon）
- Linux（x86_64 和 arm64）
- Windows（x86_64）

### 如何更新 Codex CLI？

```bash
# 使用 npm
npm update -g @openai/codex

# 使用 Homebrew
brew update && brew upgrade codex
```

## 身份验证

### 如何登录 Codex CLI？

1. 运行 `codex`
2. 选择 **使用 ChatGPT 登录**
3. 按照提示完成登录流程

### 可以使用 API Key 吗？

是的，您可以使用 OpenAI API Key：

```bash
export OPENAI_API_KEY=your-api-key-here
codex
```

### 登录失败怎么办？

1. 检查网络连接
2. 确认 ChatGPT 账户有效
3. 尝试清除缓存：`rm -rf ~/.codex/cache`
4. 如果问题持续，请查看[GitHub Issues](https://github.com/migrant620/codexCN/issues)

## 使用问题

### 如何开始使用 Codex CLI？

运行 `codex` 启动交互式会话，然后：

1. 输入您的请求
2. Codex 会分析并提供建议
3. 您可以批准、拒绝或修改建议

### Codex CLI 可以访问哪些文件？

默认情况下，Codex CLI 可以访问：

- 当前工作目录及其子目录
- 您明确指定的文件和目录

### 如何限制文件访问？

在 `~/.codex/config.toml` 中配置：

```toml
[access]
# 允许的目录
allowed_dirs = ["/path/to/project"]

# 禁止的目录
blocked_dirs = ["/path/to/sensitive"]

# 允许的文件模式
allowed_patterns = ["*.js", "*.py", "*.md"]
```

### Codex CLI 可以离线工作吗？

不可以，Codex CLI 需要互联网连接来：

- 与 OpenAI API 通信
- 下载模型和资源
- 验证身份

## 性能问题

### Codex CLI 运行缓慢怎么办？

尝试以下优化：

1. **增加并发数**：
   ```toml
   [performance]
   max_concurrent_requests = 5
   ```

2. **启用缓存**：
   ```toml
   [cache]
   enabled = true
   ```

3. **使用更快的模型**：
   ```toml
   [model]
   name = "gpt-3.5-turbo"
   ```

### 内存使用过高怎么办？

1. **限制内存使用**：
   ```toml
   [sandbox]
   memory_limit = 512  # MB
   ```

2. **减少上下文大小**：
   ```toml
   [model]
   max_tokens = 2000
   ```

3. **清理缓存**：
   ```bash
   rm -rf ~/.codex/cache
   ```

## 沙盒和安全

### 什么是沙盒？

沙盒是一个受限的执行环境，用于安全地运行代码和命令。Codex CLI 使用沙盒来防止恶意代码损害您的系统。

### 如何启用沙盒？

在 `~/.codex/config.toml` 中配置：

```toml
[sandbox]
enabled = true
type = "docker"  # 或 "namespace"
```

### 沙盒有哪些限制？

沙盒限制包括：

- 文件系统访问限制
- 网络访问限制
- 系统调用限制
- 资源使用限制

### 如何自定义沙盒？

您可以创建自定义 Docker 镜像：

```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y python3 nodejs git
WORKDIR /workspace
```

然后在配置中使用：

```toml
[sandbox]
image = "your-custom-image"
```

## 错误和故障排除

### 常见错误代码

#### `ECONNREFUSED`

网络连接被拒绝：

```bash
# 解决方案
1. 检查网络连接
2. 检查防火墙设置
3. 尝试使用代理
```

#### `401 Unauthorized`

身份验证失败：

```bash
# 解决方案
1. 检查 API Key
2. 重新登录 ChatGPT
3. 确认订阅有效
```

#### `429 Too Many Requests`

请求过于频繁：

```bash
# 解决方案
1. 减少请求频率
2. 增加请求间隔
3. 升级订阅计划
```

### 如何获取调试信息？

启用详细日志：

```bash
codex --verbose --log-level debug
```

或查看日志文件：

```bash
tail -f ~/.codex/logs/codex.log
```

### 如何重置 Codex CLI？

```bash
# 删除配置和缓存
rm -rf ~/.codex

# 重新运行以重新初始化
codex
```

## 高级功能

### 什么是 MCP？

MCP（Model Context Protocol）是一个协议，允许 Codex CLI 与外部工具和服务交互。通过 MCP，Codex 可以访问文件系统、Git 仓库、数据库等。

### 如何配置 MCP？

在 `~/.codex/config.toml` 中添加：

```toml
[mcp_servers]
enabled = true

[[mcp_servers.servers]]
name = "filesystem"
command = "npx"
args = ["@modelcontextprotocol/server-filesystem", "/path/to/project"]
```

### 可以使用自定义提示模板吗？

是的，在 `~/.codex/templates/` 中创建模板文件：

```markdown
# code-review.md
请审查以下代码：
{{code}}

重点关注：
1. 代码质量
2. 性能优化
3. 安全问题
```

然后使用：

```bash
codex --template code-review --code "$(cat file.js)"
```

### 如何在 CI/CD 中使用？

```yaml
# GitHub Actions 示例
name: Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Setup Codex
      run: |
        curl -fsSL https://github.com/openai/codex/releases/latest/download/codex-linux-amd64 -o codex
        chmod +x codex
        sudo mv codex /usr/local/bin/
    - name: Run Review
      run: codex --non-interactive "审查这个 PR" > review.md
```

## 隐私和数据

### Codex CLI 会存储我的代码吗？

Codex CLI 不会永久存储您的代码，但可能会：

- 临时缓存以提高性能
- 发送代码到 OpenAI API 进行处理
- 在日志中记录部分信息

### 如何启用零数据保留（ZDR）？

在 `~/.codex/config.toml` 中配置：

```toml
[zdr]
enabled = true
```

启用后，您的代码不会被存储或用于模型训练。

### 我的代码安全吗？

Codex CLI 采取多种安全措施：

- 沙盒执行环境
- 加密通信
- 访问控制
- 审计日志

但建议：

- 不要在代码中包含敏感信息
- 使用适当的访问控制
- 定期审查日志

## 集成和扩展

### 可以与 VS Code 集成吗？

是的，您可以：

1. 安装 [Codex VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=openai.codex)
2. 使用终端集成
3. 配置自定义任务

### 可以与其他工具集成吗？

Codex CLI 可以与以下工具集成：

- Git
- Docker
- 各种 IDE
- CI/CD 系统
- 项目管理工具

### 如何创建自定义插件？

创建插件文件：

```javascript
// ~/.codex/plugins/my-plugin.js
module.exports = {
  name: 'my-plugin',
  commands: {
    'hello': async (args) => {
      return `Hello, ${args.name}!`;
    }
  }
};
```

然后使用：

```bash
codex hello --name "World"
```

## 支持和社区

### 如何获取帮助？

1. 查看[文档](./getting-started.md)
2. 搜索[常见问题](https://github.com/migrant620/codexCN/issues)
3. 在 [GitHub Discussions](https://github.com/migrant620/codexCN/discussions) 中提问
4. 加入 [Discord 社区](https://discord.gg/codex)

### 如何报告 Bug？

1. 检查[现有问题](https://github.com/migrant620/codexCN/issues)
2. 创建新问题并使用[错误报告模板](https://github.com/migrant620/codexCN/issues/new?template=2-bug-report.yml)
3. 提供详细信息：复现步骤、期望行为、实际行为

### 如何请求功能？

1. 检查[现有功能请求](https://github.com/migrant620/codexCN/issues)
2. 创建新问题并使用[功能请求模板](https://github.com/migrant620/codexCN/issues/new?template=4-feature-request.yml)
3. 详细描述您的需求

### 如何贡献代码？

1. 阅读[贡献指南](./contributing.md)
2. Fork 仓库
3. 创建功能分支
4. 提交 Pull Request

---

如果您的问题未在此处得到解答，请：

1. 查看[完整文档](./getting-started.md)
2. 搜索[GitHub Issues](https://github.com/migrant620/codexCN/issues)
3. 在社区中提问

感谢您使用 Codex CLI！