# Codex CLI (Rust 实现)

我们将 Codex CLI 作为一个独立的原生可执行文件提供，以确保零依赖安装。

## 安装 Codex

今天，安装 Codex 的最简单方法是通过 `npm`：

```shell
npm i -g @openai/codex
codex
```

您也可以通过 Homebrew（`brew install codex`）安装或直接从我们的 [GitHub 发布页面](https://github.com/openai/codex/releases) 下载特定平台的版本。

## Rust CLI 的新功能

Rust 实现现在是维护的 Codex CLI，并作为默认体验。它包含了许多旧版 TypeScript CLI 从未支持的功能。

### 配置

Codex 支持丰富的配置选项集。请注意，Rust CLI 使用 `config.toml` 而不是 `config.json`。有关详细信息，请参阅 [`docs/config.md`](../docs/config.md)。

### 模型上下文协议支持

Codex CLI 充当 MCP 客户端，可以在启动时连接到 MCP 服务器。有关详细信息，请参阅配置文档中的 [`mcp_servers`](../docs/config.md#mcp_servers) 部分。

它仍然是实验性的，但您也可以通过运行 `codex mcp` 将 Codex 作为 MCP _服务器_启动。使用 [`@modelcontextprotocol/inspector`](https://github.com/modelcontextprotocol/inspector) 来试用：

```shell
npx @modelcontextprotocol/inspector codex mcp
```

### 通知

您可以通过配置一个在代理完成回合时运行的脚本来启用通知。[通知文档](../docs/config.md#notify) 包括一个详细示例，解释了如何通过 macOS 上的 [terminal-notifier](https://github.com/julienXX/terminal-notifier) 获取桌面通知。

### `codex exec` 以编程方式/非交互方式运行 Codex

要非交互式运行 Codex，请运行 `codex exec PROMPT`（您也可以通过 `stdin` 传递提示），Codex 将处理您的任务，直到它认为完成并退出。输出直接打印到终端。您可以设置 `RUST_LOG` 环境变量以查看更多有关正在发生的情况。

### 使用 `@` 进行文件搜索

输入 `@` 会触发工作区根目录的模糊文件名搜索。使用上下箭头在结果中选择，使用 Tab 或 Enter 用选定的路径替换 `@`。您可以使用 Esc 取消搜索。

### Esc–Esc 编辑上一条消息

当聊天编辑器为空时，按 Esc 准备"回溯"模式。再次按 Esc 打开一个突出显示最后一条用户消息的记录预览；重复按 Esc 可以逐步查看更早的用户消息。按 Enter 确认，Codex 将从该点分叉对话，相应地修剪可见记录，并用选定的用户消息预填充编辑器，以便您可以编辑并重新提交。

在记录预览中，页脚在编辑活动时显示 `Esc edit prev` 提示。

### `--cd`/`-C` 标志

有时在运行 Codex 之前 `cd` 到您希望 Codex 用作"工作根目录"的目录并不方便。幸运的是，`codex` 支持 `--cd` 选项，因此您可以指定任何您想要的文件夹。您可以通过双重检查 Codex 在新会话开始时在 TUI 中报告的 **workdir** 来确认 Codex 是否遵守 `--cd`。

### Shell 补全

通过以下方式生成 shell 补全脚本：

```shell
codex completion bash
codex completion zsh
codex completion fish
```

### 试验 Codex 沙箱

要测试在 Codex 提供的沙箱下运行命令时会发生什么，我们在 Codex CLI 中提供以下子命令：

```
# macOS
codex debug seatbelt [--full-auto] [COMMAND]...

# Linux
codex debug landlock [--full-auto] [COMMAND]...
```

### 通过 `--sandbox` 选择沙箱策略

Rust CLI 公开了一个专用的 `--sandbox`（`-s`）标志，让您可以选择沙箱策略，而**不必**使用通用的 `-c/--config` 选项：

```shell
# 使用默认的只读沙箱运行 Codex
codex --sandbox read-only

# 允许代理在当前工作区内写入，同时仍然阻止网络访问
codex --sandbox workspace-write

# 危险！完全禁用沙箱（仅当您已经在容器或其他隔离环境中运行时才这样做）
codex --sandbox danger-full-access
```

相同的设置可以通过顶级 `sandbox_mode = "MODE"` 键保存在 `~/.codex/config.toml` 中，例如 `sandbox_mode = "workspace-write"`。

## 代码组织

此文件夹是 Cargo 工作区的根目录。它包含相当多的实验性代码，但以下是关键的 crate：

- [`core/`](./core) 包含 Codex 的业务逻辑。最终，我们希望这是一个库 crate，对于构建使用 Codex 的其他 Rust/原生应用程序通常有用。
- [`exec/`](./exec) 用于自动化的"无头"CLI。
- [`tui/`](./tui) 启动使用 [Ratatui](https://ratatui.rs/) 构建的全屏 TUI 的 CLI。
- [`cli/`](./cli) CLI 多工具，通过子命令提供上述 CLI。