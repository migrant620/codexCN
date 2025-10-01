# 快速开始

本指南将帮助您开始使用 Codex CLI。

## CLI 使用

### 基本用法

运行 `codex` 命令启动交互式会话：

```bash
codex
```

这将启动一个交互式会话，您可以在其中与 Codex 进行对话。

### 在项目目录中使用

在您的项目目录中运行 `codex`，Codex 将能够访问您的项目文件：

```bash
cd /path/to/your/project
codex
```

## 使用提示作为输入运行

您可以直接在命令行中提供提示：

```bash
codex "帮我修复这个 bug"
```

或者从文件中读取提示：

```bash
codex -f prompt.txt
```

### 使用管道输入

您也可以通过管道提供输入：

```bash
echo "帮我重构这个函数" | codex
```

## 示例提示

以下是一些有用的提示示例：

### 代码生成

```
创建一个 Python 函数来计算斐波那契数列
```

### 代码重构

```
重构这个函数以提高可读性
```

### 调试帮助

```
我的代码出现了这个错误：TypeError: 'NoneType' object is not subscriptable
```

### 文档生成

```
为这个函数生成文档字符串
```

### 测试编写

```
为这个模块编写单元测试
```

## 使用 AGENTS.md 的记忆功能

您可以在项目根目录创建一个 `AGENTS.md` 文件来为 Codex 提供上下文信息。

### AGENTS.md 格式

`AGENTS.md` 文件应该包含项目相关信息，例如：

```markdown
# 项目信息

这是一个使用 React 和 Node.js 的 Web 应用程序。

## 架构

- 前端：React with TypeScript
- 后端：Node.js with Express
- 数据库：PostgreSQL

## 代码风格

- 使用 ESLint 和 Prettier
- 遵循 Airbnb JavaScript 风格指南
- 组件使用函数式组件和 Hooks

## 测试

- 使用 Jest 进行单元测试
- 使用 Cypress 进行端到端测试
```

### 为什么使用 AGENTS.md？

`AGENTS.md` 文件帮助 Codex 理解您的项目上下文，从而提供更准确和相关的建议。当您在项目中使用 Codex 时，它会读取此文件并使用其中的信息来指导其响应。

### 最佳实践

1. **保持更新**：随着项目的发展，定期更新 `AGENTS.md` 文件
2. **具体但简洁**：提供足够的信息，但避免过于冗长
3. **结构化**：使用清晰的标题和列表来组织信息
4. **包含关键信息**：包括技术栈、架构决策、代码风格等

## 配置

Codex CLI 可以通过配置文件进行自定义。有关配置选项的详细信息，请参阅[配置文档](./config.md)。

## 下一步

现在您已经了解了基础知识，请探索以下主题：

- [配置](./config.md) - 了解如何自定义 Codex
- [沙盒和批准](./sandbox.md) - 了解安全功能
- [高级功能](./advanced.md) - 探索高级功能
- [身份验证](./authentication.md) - 设置身份验证

如果您遇到问题，请查看[常见问题](./faq.md)或[贡献指南](./contributing.md)。