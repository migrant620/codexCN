# 贡献指南

感谢您对 Codex 项目的兴趣！我们欢迎各种形式的贡献。本指南将帮助您开始为项目做出贡献。

## 如何贡献

### 报告 Bug

如果您发现了 bug，请通过以下步骤报告：

1. 检查[现有问题](https://github.com/migrant620/codexCN/issues)以确保 bug 尚未被报告
2. 创建新问题并使用[错误报告模板](https://github.com/migrant620/codexCN/issues/new?template=2-bug-report.yml)
3. 提供详细的信息，包括：
   - 复现步骤
   - 期望行为
   - 实际行为
   - 环境信息（操作系统、版本等）

### 功能请求

如果您有功能建议，请：

1. 检查[现有问题](https://github.com/migrant620/codexCN/issues)以确保功能尚未被请求
2. 创建新问题并使用[功能请求模板](https://github.com/migrant620/codexCN/issues/new?template=4-feature-request.yml)
3. 详细描述您的功能请求，包括：
   - 功能描述
   - 用例
   - 预期收益

### 代码贡献

我们欢迎代码贡献！以下是贡献代码的步骤：

#### 1. 准备环境

```bash
# 克隆仓库
git clone https://github.com/migrant620/codexCN.git
cd codexCN

# 安装依赖
pnpm install

# 构建项目
pnpm build
```

#### 2. 创建分支

```bash
# 创建新分支
git checkout -b feature/your-feature-name

# 或修复分支
git checkout -b fix/your-fix-name
```

#### 3. 进行更改

- 遵循项目的代码风格
- 添加必要的测试
- 更新文档

#### 4. 提交更改

```bash
# 添加更改
git add .

# 提交更改
git commit -m "feat: 添加新功能"

# 或修复
git commit -m "fix: 修复 bug"
```

#### 5. 推送分支

```bash
git push origin feature/your-feature-name
```

#### 6. 创建 Pull Request

1. 访问 [GitHub](https://github.com/migrant620/codexCN)
2. 点击 "New pull request"
3. 选择您的分支
4. 填写 PR 模板
5. 提交 PR

### 文档贡献

文档改进非常重要！您可以通过以下方式贡献：

1. 修复拼写错误和语法问题
2. 添加缺失的文档
3. 改进现有文档的清晰度
4. 添加示例和教程

## 开发环境设置

### 系统要求

- **Node.js**: >= 22
- **pnpm**: >= 9.0.0
- **Rust**: 最新稳定版（用于 Rust 组件）

### 安装步骤

1. **安装 Node.js 和 pnpm**

```bash
# 使用 nvm 安装 Node.js
nvm install 22
nvm use 22

# 安装 pnpm
npm install -g pnpm
```

2. **安装 Rust**

```bash
# 使用 rustup 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

3. **克隆并设置项目**

```bash
git clone https://github.com/migrant620/codexCN.git
cd codex
pnpm install
```

### 构建和测试

```bash
# 构建整个项目
pnpm build

# 运行测试
pnpm test

# 运行 lint
pnpm lint

# 运行格式检查
pnpm format
```

## 代码标准

### 代码风格

我们使用以下工具来保持代码质量：

- **ESLint**: JavaScript/TypeScript 代码检查
- **Prettier**: 代码格式化
- **rustfmt**: Rust 代码格式化
- **clippy**: Rust 代码检查

### 提交消息格式

我们使用[约定式提交](https://www.conventionalcommits.org/)格式：

```
<类型>[可选的作用域]: <描述>

[可选的正文]

[可选的脚注]
```

#### 类型

- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更改
- `style`: 代码格式化（不影响代码运行的更改）
- `refactor`: 重构（既不是新功能也不是修复 bug）
- `perf`: 性能优化
- `test`: 添加缺失的测试
- `chore`: 构建过程或辅助工具的变动

#### 示例

```
feat: 添加用户认证功能

添加基于 JWT 的用户认证系统，包括：
- 登录和注册端点
- 令牌验证中间件
- 密码重置功能

Closes #123
```

### 测试要求

- 所有新功能都必须有测试
- 测试覆盖率应保持在 80% 以上
- 使用描述性的测试名称
- 遵循 AAA 模式（Arrange-Act-Assert）

## Pull Request 指南

### PR 模板

我们使用 PR 模板来确保 PR 包含所有必要信息。请填写模板的所有部分：

1. **更改描述**: 详细描述您的更改
2. **动机**: 解释为什么需要这些更改
3. **测试方法**: 说明您如何测试这些更改
4. **检查清单**: 确认您已完成所有必要步骤

### PR 检查清单

在提交 PR 之前，请确保：

- [ ] 代码遵循项目风格
- [ ] 所有测试都通过
- [ ] 添加了必要的测试
- [ ] 更新了相关文档
- [ ] 提交消息遵循约定式格式
- [ ] PR 描述清晰完整

### PR 审查流程

1. **自动检查**: CI 系统会自动运行测试和检查
2. **同行审查**: 至少需要一名维护者审查
3. **批准**: 维护者批准后，PR 可以合并
4. **合并**: PR 通常由维护者合并

## 社区指南

### 行为准则

我们承诺为所有人提供友好、安全和包容的环境。请：

- 尊重所有参与者
- 接受建设性批评
- 关注问题而不是人
- 遵循项目行为准则

### 沟通渠道

- **GitHub Issues**: 用于 bug 报告和功能请求
- **GitHub Discussions**: 用于一般讨论和问答
- **Discord**: 用于实时聊天和社区支持

### 获得帮助

如果您需要帮助：

1. 查看[文档](./getting-started.md)
3. 搜索[现有问题](https://github.com/migrant620/codexCN/issues)
4. 在 [GitHub Discussions](https://github.com/migrant620/codexCN/discussions) 中提问
4. 加入我们的 [Discord 服务器](https://discord.gg/codex)

## 发布流程

### 版本控制

我们使用[语义化版本](https://semver.org/)：

- **主版本号**: 不兼容的 API 更改
- **次版本号**: 向后兼容的功能新增
- **修订号**: 向后兼容的问题修复

### 发布步骤

1. 更新版本号
2. 更新 CHANGELOG.md
3. 创建发布分支
4. 运行完整测试套件
5. 创建发布标签
6. 构建和发布二进制文件
7. 发布到 npm

### 发布说明

每个版本都应包含：

- 版本号和发布日期
- 更改摘要
- 新功能列表
- Bug 修复列表
- 升级说明（如果需要）

## 资源

### 有用链接

- [项目主页](https://github.com/openai/codex)
- [文档](./getting-started.md)
- [问题跟踪器](https://github.com/openai/codex/issues)
- [讨论区](https://github.com/openai/codex/discussions)
- [行为准则](./CODE_OF_CONDUCT.md)

### 开发工具

- **VS Code**: 推荐的 IDE
- **Git**: 版本控制
- **GitHub**: 代码托管和协作
- **GitHub Actions**: CI/CD

### 学习资源

- [约定式提交](https://www.conventionalcommits.org/)
- [语义化版本](https://semver.org/)
- [GitHub Flow](https://docs.github.com/en/get-started/quickstart/github-flow)

## 致谢

感谢所有贡献者的努力！您的贡献使 Codex 变得更好。

特别感谢：

- 核心团队成员
- 文档贡献者
- 测试者和反馈提供者
- 社区支持者

---

再次感谢您对 Codex 项目的贡献！我们期待与您合作。