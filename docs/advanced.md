# 高级功能

本文档介绍了 Codex CLI 的高级功能和用法。

## 非交互式 / CI 模式

Codex CLI 可以在非交互式模式下运行，适合在 CI/CD 管道中使用。

### 基本用法

```bash
codex --non-interactive "您的提示"
```

### 从文件读取提示

```bash
codex --non-interactive -f prompt.txt
```

### 使用管道输入

```bash
echo "您的提示" | codex --non-interactive
```

### CI/CD 示例

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
    - name: Run Code Review
      run: |
        codex --non-interactive "审查这个 PR 中的代码更改" > review.md
    - name: Comment Review
      uses: actions/github-script@v3
      with:
        script: |
          const fs = require('fs');
          const review = fs.readFileSync('review.md', 'utf8');
          github.issues.createComment({
            issue_number: context.issue.number,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: review
          })
```

## 跟踪 / 详细日志

启用详细日志以帮助调试和了解 Codex 的内部工作。

### 启用详细日志

```bash
codex --verbose
```

### 设置日志级别

```bash
# 设置日志级别为 debug
codex --log-level debug

# 设置日志级别为 trace
codex --log-level trace
```

### 日志配置

在 `~/.codex/config.toml` 中配置日志：

```toml
[logging]
# 日志级别（trace, debug, info, warn, error）
level = "info"

# 日志文件路径
file = "/path/to/logfile.log"

# 是否在控制台输出日志
console = true

# 日志格式
format = "json"  # 或 "text"
```

### 日志分析

您可以使用标准工具分析日志文件：

```bash
# 查看错误
grep "ERROR" /path/to/logfile.log

# 统计 API 调用
grep "API Request" /path/to/logfile.log | wc -l

# 查看性能指标
grep "duration" /path/to/logfile.log
```

## 模型上下文协议 (MCP)

Codex CLI 支持 MCP（Model Context Protocol），允许您扩展 Codex 的功能。

### 什么是 MCP？

MCP 是一个标准化协议，允许 AI 模型与外部工具和服务交互。通过 MCP，Codex 可以：

- 访问文件系统
- 与 Git 仓库交互
- 查询数据库
- 调用外部 API

### 配置 MCP 服务器

在 `~/.codex/config.toml` 中添加 MCP 服务器：

```toml
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

[[mcp_servers.servers]]
name = "postgres"
command = "npx"
args = ["@modelcontextprotocol/server-postgres", "postgresql://user:pass@localhost/db"]
```

### 可用的 MCP 服务器

#### 文件系统服务器

```toml
[[mcp_servers.servers]]
name = "filesystem"
command = "npx"
args = ["@modelcontextprotocol/server-filesystem", "/path/to/project"]
```

允许 Codex 读写项目文件。

#### Git 服务器

```toml
[[mcp_servers.servers]]
name = "git"
command = "npx"
args = ["@modelcontextprotocol/server-git", "/path/to/project"]
```

允许 Codex 与 Git 仓库交互，包括提交、分支操作等。

#### 数据库服务器

```toml
[[mcp_servers.servers]]
name = "postgres"
command = "npx"
args = ["@modelcontextprotocol/server-postgres", "postgresql://user:pass@localhost/db"]
```

允许 Codex 查询和操作数据库。

#### Web 搜索服务器

```toml
[[mcp_servers.servers]]
name = "brave-search"
command = "npx"
args = ["@modelcontextprotocol/server-brave-search"]
```

允许 Codex 进行网络搜索。

### 创建自定义 MCP 服务器

您可以创建自己的 MCP 服务器。以下是一个简单的示例：

```javascript
// my-server.js
const { Server } = require('@modelcontextprotocol/sdk/server');
const { StdioServerTransport } = require('@modelcontextprotocol/sdk/server/stdio');

const server = new Server(
  {
    name: "my-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// 添加工具
server.setRequestHandler("tools/list", async () => {
  return {
    tools: [
      {
        name: "calculate",
        description: "执行数学计算",
        inputSchema: {
          type: "object",
          properties: {
            expression: {
              type: "string",
              description: "要计算的表达式",
            },
          },
          required: ["expression"],
        },
      },
    ],
  };
});

server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;
  
  if (name === "calculate") {
    try {
      const result = eval(args.expression);
      return {
        content: [
          {
            type: "text",
            text: `结果: ${result}`,
          },
        ],
      };
    } catch (error) {
      return {
        content: [
          {
            type: "text",
            text: `错误: ${error.message}`,
          },
        ],
        isError: true,
      };
    }
  }
  
  throw new Error(`未知工具: ${name}`);
});

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch(console.error);
```

然后在配置中使用：

```toml
[[mcp_servers.servers]]
name = "my-server"
command = "node"
args = ["my-server.js"]
```

## 自定义提示模板

创建自定义提示模板以重复使用常见的提示模式。

### 创建提示模板

在 `~/.codex/templates/` 目录中创建模板文件：

```markdown
# ~/.codex/templates/code-review.md

请审查以下代码，重点关注：

1. 代码质量和可读性
2. 性能优化机会
3. 安全问题
4. 最佳实践遵循

代码：
{{code}}

请提供具体的改进建议。
```

### 使用模板

```bash
codex --template code-review --code "$(cat file.js)"
```

### 模板变量

模板支持以下变量：

- `{{code}}`: 代码内容
- `{{filename}}`: 文件名
- `{{language}}`: 编程语言
- `{{context}}`: 上下文信息

## 批处理模式

使用批处理模式一次处理多个文件或请求。

### 批处理文件

```bash
# 处理目录中的所有文件
codex --batch --directory /path/to/files --template "重构这个文件"

# 使用文件列表
codex --batch --files file1.js,file2.py,file3.java --template "为这个文件添加文档"
```

### 批处理配置

在 `~/.codex/config.toml` 中配置批处理：

```toml
[batch]
# 并发处理数量
concurrency = 4

# 每个请求的超时时间（秒）
timeout = 300

# 输出目录
output_dir = "/path/to/output"

# 输出格式
format = "markdown"  # 或 "json"
```

## 插件系统

Codex CLI 支持插件系统，允许您扩展其功能。

### 安装插件

```bash
codex plugin install my-plugin
```

### 创建插件

创建一个简单的插件：

```javascript
// ~/.codex/plugins/my-plugin.js
module.exports = {
  name: 'my-plugin',
  version: '1.0.0',
  
  // 注册命令
  commands: {
    'hello': async (args, context) => {
      return `Hello, ${args.name || 'World'}!`;
    }
  },
  
  // 注册钩子
  hooks: {
    'before-request': async (request) => {
      console.log('Making request:', request);
      return request;
    },
    'after-response': async (response) => {
      console.log('Received response:', response);
      return response;
    }
  }
};
```

### 使用插件

```bash
codex hello --name "Codex"
```

## 性能优化

优化 Codex CLI 的性能以提高效率。

### 缓存配置

```toml
[cache]
# 启用缓存
enabled = true

# 缓存目录
dir = "/path/to/cache"

# 缓存大小（MB）
max_size = 1024

# 缓存过期时间（小时）
ttl = 24
```

### 性能调优

```toml
[performance]
# 并发请求数
max_concurrent_requests = 3

# 请求超时（秒）
request_timeout = 60

# 流式响应
stream_responses = true

# 压缩请求
compress_requests = true
```

## 故障排除

### 常见问题

1. **MCP 服务器连接失败**
   - 检查服务器是否已安装
   - 验证命令和参数是否正确
   - 查看日志文件了解详细错误

2. **批处理模式卡住**
   - 减少并发数量
   - 增加超时时间
   - 检查文件权限

3. **插件加载失败**
   - 验证插件语法
   - 检查依赖项
   - 查看错误日志

### 调试技巧

1. **启用详细日志**
   ```bash
   codex --verbose --log-level debug
   ```

2. **测试配置**
   ```bash
   codex --validate-config
   ```

3. **检查 MCP 服务器**
   ```bash
   codex --test-mcp-servers
   ```

## 下一步

探索这些高级功能后，您可以：

- 了解[沙盒功能](./sandbox.md)
- 查看[配置选项](./config.md)
- 阅读[贡献指南](./contributing.md)

如果您遇到问题，请查看[常见问题](./faq.md)。