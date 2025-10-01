# 身份验证

本文档介绍了如何设置 Codex CLI 的身份验证。

## 支持的身份验证方法

Codex CLI 支持以下身份验证方法：

1. **ChatGPT 登录**（推荐）
2. **OpenAI API Key**

## ChatGPT 登录（推荐）

使用 ChatGPT 账户登录是使用 Codex 的推荐方式。这允许您使用您的 ChatGPT Plus、Pro、Team、Edu 或 Enterprise 订阅。

### 登录步骤

1. 运行 `codex`
2. 选择 **使用 ChatGPT 登录**
3. 按照提示完成登录流程

### 优势

- 使用您现有的 ChatGPT 订阅
- 无需管理 API Key
- 访问最新的模型和功能

## 使用 OpenAI API Key

如果您更喜欢使用 API Key，或者需要以编程方式访问 Codex，您可以使用 OpenAI API Key。

### 获取 API Key

1. 前往 [OpenAI API 平台](https://platform.openai.com/api-keys)
2. 登录您的账户
3. 创建新的 API Key

### 设置 API Key

您可以通过以下方式之一设置 API Key：

#### 环境变量

```bash
export OPENAI_API_KEY=your-api-key-here
```

#### 配置文件

在 `~/.codex/config.toml` 中添加：

```toml
[auth]
api_key = "your-api-key-here"
```

#### 命令行

```bash
codex --api-key your-api-key-here
```

## 强制使用特定的身份验证方法（高级）

您可以通过配置文件强制使用特定的身份验证方法。

在 `~/.codex/config.toml` 中添加：

```toml
[auth]
method = "api_key"  # 或 "chatgpt"
```

## 在"无头"机器上登录

在无头（无图形界面）机器上，您可以使用以下方法进行身份验证：

### 使用 API Key

这是在无头机器上最简单的方法：

```bash
export OPENAI_API_KEY=your-api-key-here
codex
```

### 使用设备代码

1. 运行 `codex --login-device`
2. 复制显示的代码
3. 在另一台有浏览器的机器上访问显示的 URL
4. 输入代码完成登录

## 按使用量计费（替代方案：使用 OpenAI API Key）

如果您没有 ChatGPT 订阅，您可以使用 OpenAI API Key 进行按使用量计费。

### 设置

1. 获取 OpenAI API Key
2. 按照上面的说明设置 API Key
3. 确保您的账户有足够的余额

### 计费

使用量将根据您的 OpenAI 账户计费。有关定价的详细信息，请参阅 [OpenAI 定价页面](https://openai.com/pricing)。

## 从按使用量计费 API Key 迁移

如果您之前使用 API Key 进行按使用量计费，现在想要迁移到 ChatGPT 登录：

1. 运行 `codex`
2. 选择 **使用 ChatGPT 登录**
3. 完成登录流程
4. （可选）从配置中移除 API Key

## 故障排除

### 登录问题

如果您在登录时遇到问题：

1. 确保您有稳定的网络连接
2. 检查您的 ChatGPT 账户是否有效
3. 尝试清除本地缓存：`rm -rf ~/.codex/cache`
4. 如果问题仍然存在，请在[GitHub 问题](https://github.com/migrant620/codexCN/issues)中报告

### API Key 问题

如果您遇到 API Key 相关问题：

1. 验证 API Key 是否正确
2. 检查您的 OpenAI 账户余额
3. 确保您的 API Key 有必要的权限
4. 检查是否达到了使用限制

## 安全最佳实践

### API Key 安全

- 不要将 API Key 提交到版本控制系统
- 使用环境变量或配置文件存储 API Key
- 定期轮换 API Key
- 如果怀疑泄露，立即撤销 API Key

### 会话安全

- 在共享计算机上使用后注销
- 定期清除缓存：`rm -rf ~/.codex/cache`
- 使用强密码和两步验证保护您的 ChatGPT 账户

## 下一步

设置身份验证后，您可以：

- 探索[配置选项](./config.md)
- 了解[沙盒功能](./sandbox.md)
- 查看[高级功能](./advanced.md)

如果您遇到问题，请查看[常见问题](./faq.md)。