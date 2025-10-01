<h1 align="center">OpenAI Codex CLI</h1>
<p align="center">在您的终端中运行的轻量级编码代理</p>

<p align="center"><code>npm i -g @openai/codex</code></p>

> [!IMPORTANT]
> 这是 Codex CLI 的_旧版_ TypeScript 实现的文档。它已被_Rust_实现所取代。有关详细信息，请参阅 [Codex 存储库根目录中的 README](https://github.com/openai/codex/blob/main/README.md)。

![Codex demo GIF using: codex "explain this codebase to me"](../.github/demo.gif)

---

<details>
<summary><strong>目录</strong></summary>

<!-- Begin ToC -->

- [实验性技术免责声明](#实验性技术免责声明)
- [快速开始](#快速开始)
- [为什么选择 Codex](#为什么选择-codex)
- [安全模型和权限](#安全模型和权限)
  - [平台沙箱详细信息](#平台沙箱详细信息)
- [系统要求](#系统要求)
- [CLI 参考](#cli-参考)
- [内存和项目文档](#内存和项目文档)
- [非交互式/CI 模式](#非交互式ci-模式)
- [跟踪/详细日志记录](#跟踪详细日志记录)
- [实用示例](#实用示例)
- [安装](#安装)
- [配置指南](#配置指南)
  - [基本配置参数](#基本配置参数)
  - [自定义 AI 提供商配置](#自定义-ai-提供商配置)
  - [历史记录配置](#历史记录配置)
  - [配置示例](#配置示例)
  - [完整配置示例](#完整配置示例)
  - [自定义指令](#自定义指令)
  - [环境变量设置](#环境变量设置)
- [常见问题](#常见问题)
- [零数据保留 (ZDR) 使用](#零数据保留-zdr-使用)
- [Codex 开源基金](#codex-开源基金)
- [贡献](#贡献)
  - [开发工作流程](#开发工作流程)
  - [使用 Husky 的 Git 钩子](#使用-husky-的-git-钩子)
  - [调试](#调试)
  - [编写高影响力的代码更改](#编写高影响力的代码更改)
  - [打开拉取请求](#打开拉取请求)
  - [审查流程](#审查流程)
  - [社区价值观](#社区价值观)
  - [获取帮助](#获取帮助)
  - [贡献者许可协议 (CLA)](#贡献者许可协议-cla)
    - [快速修复](#快速修复)
  - [发布 `codex`](#发布-codex)
  - [替代构建选项](#替代构建选项)
    - [Nix flake 开发](#nix-flake-开发)
- [安全和负责任的 AI](#安全和负责任的-ai)
- [许可证](#许可证)

<!-- End ToC -->

</details>

---

## 实验性技术免责声明

Codex CLI 是一个正在积极开发中的实验性项目。它尚未稳定，可能包含错误、不完整的功能或 undergo 破坏性更改。我们正在与社区一起公开构建它，欢迎：

- 错误报告
- 功能请求
- 拉取请求
- 良好的氛围

请通过提交问题或提交 PR 来帮助我们改进（有关如何贡献的信息，请参见下面的部分）！

## 快速开始

全局安装：

```shell
npm install -g @openai/codex
```

接下来，将您的 OpenAI API 密钥设置为环境变量：

```shell
export OPENAI_API_KEY="your-api-key-here"
```

> **注意：**此命令仅为当前终端会话设置密钥。您可以将 `export` 行添加到 shell 的配置文件中（例如 `~/.zshrc`），但我们建议为会话设置。**提示：**您也可以将 API 密钥放在项目根目录的 `.env` 文件中：
>
> ```env
> OPENAI_API_KEY=your-api-key-here
> ```
>
> CLI 将自动从 `.env` 加载变量（通过 `dotenv/config`）。

<details>
<summary><strong>使用 <code>--provider</code> 来使用其他模型</strong></summary>

> Codex 还允许您使用支持 OpenAI Chat Completions API 的其他提供商。您可以在配置文件中设置提供商，或使用 `--provider` 标志。`--provider` 的可能选项是：
>
> - openai（默认）
> - openrouter
> - azure
> - gemini
> - ollama
> - mistral
> - deepseek
> - xai
> - groq
> - arceeai
> - 任何其他与 OpenAI API 兼容的提供商
>
> 如果您使用 OpenAI 以外的提供商，您需要在配置文件中设置提供商的 API 密钥，或在环境变量中设置：
>
> ```shell
> export <provider>_API_KEY="your-api-key-here"
> ```
>
> 如果您使用上面未列出的提供商，您还必须设置提供商的基本 URL：
>
> ```shell
> export <provider>_BASE_URL="https://your-provider-api-base-url"
> ```

</details>
<br />

以交互方式运行：

```shell
codex
```

或者，以提示作为输入运行（并可选择在 `Full Auto` 模式下）：

```shell
codex "explain this codebase to me"
```

```shell
codex --approval-mode full-auto "create the fanciest todo-list app"
```

就是这样 - Codex 将搭建一个文件，在沙箱中运行它，安装任何缺失的依赖项，并显示实时结果。批准更改后，它们将被提交到您的工作目录。

---

## 为什么选择 Codex？

Codex CLI 专为已经**生活在终端中**并希望获得 ChatGPT 级推理能力**加上**实际运行代码、操作文件和迭代能力的开发人员而构建 - 所有这些都在版本控制之下。简而言之，它是理解并执行您的存储库的_聊天驱动开发_。

- **零设置** - 带上您的 OpenAI API 密钥即可！
- **完全自动批准，同时安全 + 安全**通过运行禁用网络和目录沙箱
- **多模态** - 传递屏幕截图或图表以实现功能 ✨

而且它是**完全开源的**，因此您可以查看并贡献其发展方式！

---

## 安全模型和权限

Codex 允许您决定代理接收多少自主权以及通过 `--approval-mode` 标志（或交互式入门提示）的自动批准策略：

| 模式 | 代理可以在不询问的情况下执行的操作 | 仍需要批准 |
| ------------------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **建议** <br>（默认） | <li>读取存储库中的任何文件 | <li>**所有**文件写入/补丁<li> **任何**任意 shell 命令（除了读取文件） |
| **自动编辑** | <li>读取**并**对文件应用补丁写入 | <li>**所有** shell 命令 |
| **完全自动** | <li>读取/写入文件 <li> 执行 shell 命令（网络禁用，写入仅限于您的工作目录） | - |

在**完全自动**模式下，每个命令都在**禁用网络**的情况下运行，并限制在当前工作目录（加上临时文件）中以实现深度防御。如果您在目录**未被** Git 跟踪的情况下在**自动编辑**或**完全自动**模式下启动，Codex 还会显示警告/确认，因此您始终有一个安全网。

即将推出：一旦我们对额外的保护措施有信心，您将能够将特定命令列入白名单以在网络启用的情况下自动执行。

### 平台沙箱详细信息

Codex 使用的强化机制取决于您的操作系统：

- **macOS 12+** - 命令用 **Apple Seatbelt**（`sandbox-exec`）包装。

  - 除了一个小的可写根目录集（`$PWD`、`$TMPDIR`、`~/.codex` 等）之外，所有内容都放在只读监狱中。
  - 出站网络_默认完全阻塞_ - 即使子进程尝试 `curl` 某个地方也会失败。

- **Linux** - 默认情况下没有沙箱。
  我们建议使用 Docker 进行沙箱，Codex 在**最小容器映像**中启动自身，并在同一路径下_读写_挂载您的存储库。自定义 `iptables`/`ipset` 防火墙脚本拒绝所有出口，除了 OpenAI API。这为您提供了确定性的、可重现的运行，而无需在主机上拥有 root 权限。您可以使用 [`run_in_container.sh`](../codex-cli/scripts/run_in_container.sh) 脚本来设置沙箱。

---

## 系统要求

| 要求 | 详细信息 |
| --------------------------- | --------------------------------------------------------------- |
| 操作系统 | macOS 12+、Ubuntu 20.04+/Debian 10+ 或 Windows 11 **通过 WSL2** |
| Node.js | **16 或更新版本**（推荐 Node 20 LTS） |
| Git（可选，推荐） | 2.23+ 用于内置 PR 帮助程序 |
| RAM | 最少 4 GB（推荐 8 GB） |

> 永远不要运行 `sudo npm install -g`；而是修复 npm 权限。

---

## CLI 参考

| 命令 | 用途 | 示例 |
| ------------------------------------ | ----------------------------------- | ------------------------------------ |
| `codex` | 交互式 REPL | `codex` |
| `codex "..."` | 交互式 REPL 的初始提示 | `codex "fix lint errors"` |
| `codex -q "..."` | 非交互式"静音模式" | `codex -q --json "explain utils.ts"` |
| `codex completion <bash\|zsh\|fish>` | 打印 shell 完成脚本 | `codex completion bash` |

关键标志：`--model/-m`、`--approval-mode/-a`、`--quiet/-q` 和 `--notify`。

---

## 内存和项目文档

您可以使用 `AGENTS.md` 文件为 Codex 提供额外的指令和指导。Codex 在以下位置查找 `AGENTS.md` 文件，并自上而下合并它们：

1. `~/.codex/AGENTS.md` - 个人全局指导
2. 存储库根目录的 `AGENTS.md` - 共享项目说明
3. 当前工作目录中的 `AGENTS.md` - 子文件夹/功能详细信息

使用 `--no-project-doc` 或环境变量 `CODEX_DISABLE_PROJECT_DOC=1` 禁用加载这些文件。

---

## 非交互式/CI 模式

在管道中无头运行 Codex。示例 GitHub Action 步骤：

```yaml
- name: Update changelog via Codex
  run: |
    npm install -g @openai/codex
    export OPENAI_API_KEY="${{ secrets.OPENAI_KEY }}"
    codex -a auto-edit --quiet "update CHANGELOG for next release"
```

设置 `CODEX_QUIET_MODE=1` 以消除交互式 UI 噪音。

## 跟踪/详细日志记录

设置环境变量 `DEBUG=true` 打印完整的 API 请求和响应详细信息：

```shell
DEBUG=true codex
```

---

## 实用示例

以下是几个您可以复制粘贴的小例子。用您自己的任务替换引号中的文本。有关更多提示和使用模式，请参阅[提示指南](https://github.com/openai/codex/blob/main/codex-cli/examples/prompting_guide.md)。

| ✨ | 您输入的内容 | 发生的情况 |
| --- | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1 | `codex "Refactor the Dashboard component to React Hooks"` | Codex 重写类组件，运行 `npm test`，并显示差异。 |
| 2 | `codex "Generate SQL migrations for adding a users table"` | 推断您的 ORM，创建迁移文件，并在沙箱数据库中运行它们。 |
| 3 | `codex "Write unit tests for utils/date.ts"` | 生成测试，执行它们，并迭代直到它们通过。 |
| 4 | `codex "Bulk-rename *.jpeg -> *.jpg with git mv"` | 安全地重命名文件并更新导入/使用。 |
| 5 | `codex "Explain what this regex does: ^(?=.*[A-Z]).{8,}$"` | 输出逐步的人工解释。 |
| 6 | `codex "Carefully review this repo, and propose 3 high impact well-scoped PRs"` | 在当前代码库中建议有影响力的 PR。 |
| 7 | `codex "Look for vulnerabilities and create a security review report"` | 查找并解释安全漏洞。 |

---

## 安装

<details open>
<summary><strong>从 npm 安装（推荐）</strong></summary>

```bash
npm install -g @openai/codex
# 或者
yarn global add @openai/codex
# 或者
bun install -g @openai/codex
# 或者
pnpm add -g @openai/codex
```

</details>

<details>
<summary><strong>从源代码构建</strong></summary>

```bash
# 克隆存储库并导航到 CLI 包
git clone https://github.com/openai/codex.git
cd codex/codex-cli

# 启用 corepack
corepack enable

# 安装依赖项并构建
pnpm install
pnpm build

# 仅限 Linux：下载预构建的沙箱二进制文件（需要 gh 和 zstd）。
./scripts/install_native_deps.sh

# 获取用法和选项
node ./dist/cli.js --help

# 直接运行本地构建的 CLI
node ./dist/cli.js

# 或者为了方便全局链接命令
pnpm link
```

</details>

---

## 配置指南

Codex 配置文件可以放在 `~/.codex/` 目录中，支持 YAML 和 JSON 格式。

### 基本配置参数

| 参数 | 类型 | 默认值 | 描述 | 可用选项 |
| ------------------- | ------- | ---------- | -------------------------------- | ---------------------------------------------------------------------------------------------- |
| `model` | string | `o4-mini` | 要使用的 AI 模型 | 任何支持 OpenAI API 的模型名称 |
| `approvalMode` | string | `suggest` | AI 助手的权限模式 | `suggest`（仅建议）<br>`auto-edit`（自动编辑）<br>`full-auto`（完全自动） |
| `fullAutoErrorMode` | string | `ask-user` | 完全自动模式下的错误处理 | `ask-user`（提示用户输入）<br>`ignore-and-continue`（忽略并继续） |
| `notify` | boolean | `true` | 启用桌面通知 | `true`/`false` |

### 自定义 AI 提供商配置

在 `providers` 对象中，您可以配置多个 AI 服务提供商。每个提供商需要以下参数：

| 参数 | 类型 | 描述 | 示例 |
| --------- | ------ | --------------------------------------- | ----------------------------- |
| `name` | string | 提供商的显示名称 | `"OpenAI"` |
| `baseURL` | string | API 服务 URL | `"https://api.openai.com/v1"` |
| `envKey` | string | 环境变量名称（用于 API 密钥） | `"OPENAI_API_KEY"` |

### 历史记录配置

在 `history` 对象中，您可以配置对话历史记录设置：

| 参数 | 类型 | 描述 | 示例值 |
| ------------------- | ------- | ------------------------------------------------------ | ------------- |
| `maxSize` | number | 要保存的历史记录条目最大数量 | `1000` |
| `saveHistory` | boolean | 是否保存历史记录 | `true` |
| `sensitivePatterns` | array | 历史记录中要过滤的敏感信息模式 | `[]` |

### 配置示例

1. YAML 格式（保存为 `~/.codex/config.yaml`）：

```yaml
model: o4-mini
approvalMode: suggest
fullAutoErrorMode: ask-user
notify: true
```

2. JSON 格式（保存为 `~/.codex/config.json`）：

```json
{
  "model": "o4-mini",
  "approvalMode": "suggest",
  "fullAutoErrorMode": "ask-user",
  "notify": true
}
```

### 完整配置示例

以下是带有多个自定义提供商的 `config.json` 的全面示例：

```json
{
  "model": "o4-mini",
  "provider": "openai",
  "providers": {
    "openai": {
      "name": "OpenAI",
      "baseURL": "https://api.openai.com/v1",
      "envKey": "OPENAI_API_KEY"
    },
    "azure": {
      "name": "AzureOpenAI",
      "baseURL": "https://YOUR_PROJECT_NAME.openai.azure.com/openai",
      "envKey": "AZURE_OPENAI_API_KEY"
    },
    "openrouter": {
      "name": "OpenRouter",
      "baseURL": "https://openrouter.ai/api/v1",
      "envKey": "OPENROUTER_API_KEY"
    },
    "gemini": {
      "name": "Gemini",
      "baseURL": "https://generativelanguage.googleapis.com/v1beta/openai",
      "envKey": "GEMINI_API_KEY"
    },
    "ollama": {
      "name": "Ollama",
      "baseURL": "http://localhost:11434/v1",
      "envKey": "OLLAMA_API_KEY"
    },
    "mistral": {
      "name": "Mistral",
      "baseURL": "https://api.mistral.ai/v1",
      "envKey": "MISTRAL_API_KEY"
    },
    "deepseek": {
      "name": "DeepSeek",
      "baseURL": "https://api.deepseek.com",
      "envKey": "DEEPSEEK_API_KEY"
    },
    "xai": {
      "name": "xAI",
      "baseURL": "https://api.x.ai/v1",
      "envKey": "XAI_API_KEY"
    },
    "groq": {
      "name": "Groq",
      "baseURL": "https://api.groq.com/openai/v1",
      "envKey": "GROQ_API_KEY"
    },
    "arceeai": {
      "name": "ArceeAI",
      "baseURL": "https://conductor.arcee.ai/v1",
      "envKey": "ARCEEAI_API_KEY"
    }
  },
  "history": {
    "maxSize": 1000,
    "saveHistory": true,
    "sensitivePatterns": []
  }
}
```

### 自定义指令

您可以创建一个 `~/.codex/AGENTS.md` 文件来为代理定义自定义指导：

```markdown
- 始终用表情符号回应
- 仅在明确要求时使用 git 命令
```

### 环境变量设置

对于每个 AI 提供商，您需要在环境变量中设置相应的 API 密钥。例如：

```bash
# OpenAI
export OPENAI_API_KEY="your-api-key-here"

# Azure OpenAI
export AZURE_OPENAI_API_KEY="your-azure-api-key-here"
export AZURE_OPENAI_API_VERSION="2025-04-01-preview" (可选)

# OpenRouter
export OPENROUTER_API_KEY="your-openrouter-key-here"

# 其他提供商类似
```

---

## 常见问题

<details>
<summary>OpenAI 在 2021 年发布了一个名为 Codex 的模型 - 这有关系吗？</summary>

2021 年，OpenAI 发布了 Codex，这是一个旨在从自然语言提示生成代码的 AI 系统。原始的 Codex 模型已于 2023 年 3 月弃用，与 CLI 工具是分开的。

</details>

<details>
<summary>支持哪些模型？</summary>

任何 [Responses API](https://platform.openai.com/docs/api-reference/responses) 中可用的模型。默认是 `o4-mini`，但传递 `--model gpt-4.1` 或在配置文件中设置 `model: gpt-4.1` 来覆盖。

</details>
<details>
<summary>为什么 <code>o3</code> 或 <code>o4-mini</code> 对我不起作用？</summary>

您的 [API 账户可能需要验证](https://help.openai.com/en/articles/10910291-api-organization-verification) 才能开始流式传输响应并查看来自 API 的思维链摘要。如果您仍然遇到问题，请告诉我们！

</details>

<details>
<summary>如何阻止 Codex 编辑我的文件？</summary>

Codex 在沙箱中运行模型生成的命令。如果建议的命令或文件更改看起来不正确，您可以简单地输入 **n** 来拒绝命令或给模型反馈。

</details>
<details>
<summary>它在 Windows 上工作吗？</summary>

不直接。它需要 [Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/install) - Codex 在 macOS 和 Linux 上使用 Node 20+ 进行定期测试，也支持 Node 16。

</details>

---

## 零数据保留 (ZDR) 使用

Codex CLI **确实**支持启用了 [零数据保留 (ZDR)](https://platform.openai.com/docs/guides/your-data#zero-data-retention) 的 OpenAI 组织。如果您的 OpenAI 组织启用了零数据保留，但您仍然遇到如下错误：

```
OpenAI rejected the request. Error details: Status: 400, Code: unsupported_parameter, Type: invalid_request_error, Message: 400 Previous response cannot be used for this organization due to Zero Data Retention.
```

您可能需要使用以下命令升级到更新版本：`npm i -g @openai/codex@latest`

---

## Codex 开源基金

我们很高兴推出一个**100 万美元倡议**，支持使用 Codex CLI 和其他 OpenAI 模型的开源项目。

- 奖励高达 **25,000 美元** 的 API 积分。
- 申请**按滚动方式**审核。

**感兴趣？[在此申请](https://openai.com/form/codex-open-source-fund/)。**

---

## 贡献

这个项目正在积极开发中，代码可能会发生相当大的变化。一旦完成，我们将更新此消息！

更广泛地说，我们欢迎贡献 - 无论您是打开第一个拉取请求还是经验丰富的维护者。同时，我们关心可靠性和长期可维护性，因此合并代码的门槛故意**很高**。下面的指南详细说明了"高质量"在实践中意味着什么，应该使整个过程透明和友好。

### 开发工作流程

- 从 `main` 创建一个_主题分支_ - 例如 `feat/interactive-prompt`。
- 保持您的更改集中。多个不相关的修复应该作为单独的 PR 打开。
- 在开发期间使用 `pnpm test:watch` 以获得超快速反馈。
- 我们使用 **Vitest** 进行单元测试，**ESLint** + **Prettier** 进行样式，**TypeScript** 进行类型检查。
- 在推送之前，运行完整的测试/类型/lint 套件：

### 使用 Husky 的 Git 钩子

这个项目使用 [Husky](https://typicode.github.io/husky/) 来强制执行代码质量检查：

- **预提交钩子**：在提交之前自动运行 lint-staged 以格式化和 lint 文件
- **预推送钩子**：在推送到远程之前运行测试和类型检查

这些钩子有助于维护代码质量并防止推送测试失败的代码。更多详细信息，请参阅 [HUSKY.md](./HUSKY.md)。

```bash
pnpm test && pnpm run lint && pnpm run typecheck
```

- 如果您**尚未**签署贡献者许可协议 (CLA)，请添加包含确切文本的 PR 评论

  ```text
  我已阅读 CLA 文档并在此签署 CLA
  ```

  一旦所有作者签署，CLA-Assistant 机器人将将 PR 状态变为绿色。

```bash
# 监视模式（测试在更改时重新运行）
pnpm test:watch

# 不发出文件的类型检查
pnpm typecheck

# 自动修复 lint + prettier 问题
pnpm lint:fix
pnpm format:fix
```

### 调试

要使用可视化调试器调试 CLI，请在 `codex-cli` 文件夹中执行以下操作：

- 运行 `pnpm run build` 构建 CLI，这将在 `dist` 文件夹中的 `cli.js` 旁边生成 `cli.js.map`。
- 使用 `node --inspect-brk ./dist/cli.js` 运行 CLI 然后程序等待调试器附加后再继续。选项：
  - 在 VS Code 中，从命令面板中选择 **Debug: Attach to Node Process** 并在下拉菜单中选择调试端口 `9229` 的选项（可能是第一个选项）
  - 在 Chrome 中转到 <chrome://inspect> 并找到 **localhost:9229** 并点击 **trace**

### 编写高影响力的代码更改

1. **从问题开始。** 打开一个新问题或评论现有讨论，以便我们可以在编写代码之前就解决方案达成一致。
2. **添加或更新测试。** 每个新功能或错误修复都应该带有测试覆盖，在更改之前失败，之后通过。不需要 100% 覆盖率，但力求有意义的断言。
3. **记录行为。** 如果您的更改影响用户可见的行为，请更新 README、内联帮助（`codex --help`）或相关示例项目。
4. **保持提交原子化。** 每个提交都应该编译，测试应该通过。这使得审查和潜在的回滚更容易。

### 打开拉取请求

- 填写 PR 模板（或包含类似信息）- **什么？为什么？如何？**
- 在本地运行**所有**检查（`npm test && npm run lint && npm run typecheck`）。本来可以在本地捕获的 CI 故障会减慢过程。
- 确保您的分支与 `main` 保持最新，并且您已解决合并冲突。
- 仅当您认为 PR 处于可合并状态时才将其标记为**准备审查**。

### 审查流程

1. 将分配一名维护者作为主要审查者。
2. 我们可能会要求更改 - 请不要个人化。我们重视工作，但我们也重视一致性和长期可维护性。
3. 当达成共识认为 PR 达到标准时，维护者将进行压缩合并。

### 社区价值观

- **友善和包容。** 尊重他人；我们遵循[贡献者契约](https://www.contributor-covenant.org/)。
- **假设善意。** 书面交流很困难 - 宁可慷慨。
- **教与学。** 如果您发现令人困惑的事情，请打开问题或 PR 进行改进。

### 获取帮助

如果您在设置项目时遇到问题，希望对想法获得反馈，或者只是想说_你好_ - 请打开讨论或跳入相关问题。我们很乐意提供帮助。

只要我们共同努力，就可以使 Codex CLI 成为一个令人难以置信的工具。**快乐黑客！** :rocket:

### 贡献者许可协议 (CLA)

所有贡献者**必须**接受 CLA。这个过程很简单：

1. 打开您的拉取请求。
2. 粘贴以下评论（如果您以前签署过，请回复 `recheck`）：

   ```text
   我已阅读 CLA 文档并在此签署 CLA
   ```

3. CLA-Assistant 机器人将您的签名记录在存储库中，并将状态检查标记为已通过。

不需要特殊的 Git 命令、电子邮件附件或提交页脚。

#### 快速修复

| 场景 | 命令 |
| ----------------- | ------------------------------------------------ |
| 修改最后一次提交 | `git commit --amend -s --no-edit && git push -f` |

**DCO 检查**会阻止合并，直到 PR 中的每个提交都带有页脚（使用压缩只是一个）。

### 发布 `codex`

要发布新版本的 CLI，您首先需要暂存 npm 包。`codex-cli/scripts/` 中的帮助脚本完成所有繁重的工作。在 `codex-cli` 文件夹中运行：

```bash
# 经典的 JS 实现，包括用于 Linux 沙箱的小型原生二进制文件。
pnpm stage-release

# 可选地指定临时目录以在运行之间重用。
RELEASE_DIR=$(mktemp -d)
pnpm stage-release --tmp "$RELEASE_DIR"

# "胖"包，另外捆绑了用于 Linux 的原生 Rust CLI 二进制文件。
# 最终用户可以通过在运行时设置 CODEX_RUST=1 来选择加入。
pnpm stage-release --native
```

转到发布暂存的文件夹并验证它是否按预期工作。如果是，从临时文件夹运行以下命令：

```
cd "$RELEASE_DIR"
npm publish
```

### 替代构建选项

#### Nix flake 开发

先决条件：Nix >= 2.4 启用了 flakes（`~/.config/nix/nix.conf` 中的 `experimental-features = nix-command flakes`）。

进入 Nix 开发 shell：

```bash
# 根据您想要使用的实现使用任一命令
nix develop .#codex-cli # 用于进入 codex-cli 特定 shell
nix develop .#codex-rs # 用于进入 codex-rs 特定 shell
```

这个 shell 包括 Node.js，安装依赖项，构建 CLI，并提供 `codex` 命令别名。

直接构建并运行 CLI：

```bash
# 根据您想要使用的实现使用任一命令
nix build .#codex-cli # 用于构建 codex-cli
nix build .#codex-rs # 用于构建 codex-rs
./result/bin/codex --help
```

通过 flake 应用运行 CLI：

```bash
# 根据您想要使用的实现使用任一命令
nix run .#codex-cli # 用于运行 codex-cli
nix run .#codex-rs # 用于运行 codex-rs
```

将 direnv 与 flakes 一起使用

如果您安装了 direnv，您可以使用以下 `.envrc` 在 `cd` 进入项目目录时自动进入 Nix shell：

```bash
cd codex-rs
echo "use flake ../flake.nix#codex-cli" >> .envrc && direnv allow
cd codex-cli
echo "use flake ../flake.nix#codex-rs" >> .envrc && direnv allow
```

---

## 安全和负责任的 AI

您是否发现了漏洞或对模型输出有担忧？请发送电子邮件至 **security@openai.com**，我们将及时回应。

---

## 许可证

此存储库在 [Apache-2.0 许可证](LICENSE) 下获得许可。