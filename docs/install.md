# 安装和构建

本文档介绍了如何安装和构建 Codex CLI。

## 系统要求

### 最低要求

- **操作系统**:
  - macOS 10.15 或更高版本
  - Linux（Ubuntu 18.04+ 或其他主流发行版）
  - Windows 10 或更高版本

- **硬件**:
  - 至少 4GB RAM
  - 至少 2GB 可用磁盘空间
  - 64 位处理器

### 推荐配置

- **操作系统**:
  - macOS 11.0 或更高版本
  - Linux（Ubuntu 20.04+ 或其他主流发行版）
  - Windows 11

- **硬件**:
  - 8GB 或更多 RAM
  - 5GB 或更多可用磁盘空间
  - 现代 64 位处理器

### 开发环境要求

如果您计划从源代码构建：

- **Node.js**: >= 22
- **pnpm**: >= 9.0.0
- **Rust**: >= 1.70
- **Git**: >= 2.30

## 安装方法

### 方法 1: 包管理器（推荐）

#### 使用 npm

```bash
# 全局安装
npm install -g @openai/codex

# 验证安装
codex --version
```

#### 使用 Homebrew（macOS）

```bash
# 安装
brew install codex

# 验证安装
codex --version
```

#### 使用 yarn

```bash
# 全局安装
yarn global add @openai/codex

# 验证安装
codex --version
```

### 方法 2: 直接下载二进制文件

您可以从 [GitHub Releases](https://github.com/openai/codex/releases/latest) 下载适合您平台的二进制文件。

#### 选择正确的二进制文件

- **macOS**:
  - Apple Silicon (M1/M2): `codex-aarch64-apple-darwin.tar.gz`
  - Intel: `codex-x86_64-apple-darwin.tar.gz`

- **Linux**:
  - x86_64: `codex-x86_64-unknown-linux-musl.tar.gz`
  - ARM64: `codex-aarch64-unknown-linux-musl.tar.gz`

- **Windows**:
  - x86_64: `codex-x86_64-pc-windows-msvc.zip`

#### 安装步骤

1. 下载适合您平台的二进制文件
2. 解压文件：
   ```bash
   # macOS/Linux
   tar -xzf codex-*-*.tar.gz
   
   # Windows
   unzip codex-*-*.zip
   ```
3. 将二进制文件移动到 PATH 中的目录：
   ```bash
   # macOS/Linux
   sudo mv codex-* /usr/local/bin/codex
   chmod +x /usr/local/bin/codex
   
   # Windows
   # 将 codex.exe 移动到 PATH 中的目录，如 C:\Program Files\codex
   ```
4. 验证安装：
   ```bash
   codex --version
   ```

### 方法 3: 使用 DotSlash

DotSlash 是一个用于管理二进制文件的工具。

#### 安装 DotSlash

```bash
# 使用 npm
npm install -g @dotslash/cli

# 使用 Homebrew
brew install dotslash
```

#### 使用 DotSlash 安装 Codex

```bash
dotslash install openai/codex
```

### 方法 4: 从源代码构建

如果您需要从源代码构建，请按照以下步骤操作。

## 从源代码构建

### 准备环境

#### 安装 Node.js 和 pnpm

```bash
# 使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22

# 安装 pnpm
npm install -g pnpm@9
```

#### 安装 Rust

```bash
# 使用 rustup（推荐）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env

# 验证安装
rustc --version
cargo --version
```

#### 安装其他依赖

##### macOS

```bash
# 使用 Homebrew
brew install git openssl

# 设置 OpenSSL 环境变量
export OPENSSL_ROOT_DIR=$(brew --prefix openssl)
```

##### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install -y \
    build-essential \
    curl \
    git \
    libssl-dev \
    pkg-config
```

##### Windows

1. 安装 [Git for Windows](https://git-scm.com/download/win)
2. 安装 [Visual Studio Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
3. 安装 [OpenSSL](https://slproweb.com/products/Win32OpenSSL.html)

### 克隆仓库

```bash
git clone https://github.com/openai/codex.git
cd codex
```

### 安装依赖

```bash
# 安装 Node.js 依赖
pnpm install

# 安装 Rust 依赖
cargo build
```

### 构建项目

#### 构建整个项目

```bash
# 构建所有组件
pnpm build
```

#### 构建特定组件

```bash
# 构建 CLI
pnpm build:cli

# 构建 Rust 组件
pnpm build:rust
```

### 运行测试

```bash
# 运行所有测试
pnpm test

# 运行特定测试
pnpm test:unit
pnpm test:integration
```

### 生成二进制文件

```bash
# 生成发布版本
pnpm build:release

# 二进制文件位置
# macOS/Linux: target/release/codex
# Windows: target/release/codex.exe
```

## 验证安装

### 检查版本

```bash
codex --version
```

### 运行诊断

```bash
codex --doctor
```

这将检查：

- 系统兼容性
- 依赖项安装
- 网络连接
- 权限设置

### 测试基本功能

```bash
# 启动交互式会话
codex

# 测试非交互模式
codex --non-interactive "Hello, Codex!"

# 测试配置验证
codex --validate-config
```

## 升级

### 使用包管理器升级

```bash
# npm
npm update -g @openai/codex

# Homebrew
brew update && brew upgrade codex

# yarn
yarn global upgrade @openai/codex
```

### 从二进制文件升级

1. 下载最新版本的二进制文件
2. 替换现有的二进制文件：
   ```bash
   # macOS/Linux
   sudo cp new-codex /usr/local/bin/codex
   
   # Windows
   # 替换现有的 codex.exe
   ```
3. 验证新版本：
   ```bash
   codex --version
   ```

### 从源代码升级

```bash
cd /path/to/codex
git pull origin main
pnpm install
pnpm build
```

## 卸载

### 使用包管理器卸载

```bash
# npm
npm uninstall -g @openai/codex

# Homebrew
brew uninstall codex

# yarn
yarn global remove @openai/codex
```

### 手动卸载

1. 删除二进制文件：
   ```bash
   # macOS/Linux
   sudo rm /usr/local/bin/codex
   
   # Windows
   # 删除 codex.exe
   ```
2. 删除配置和缓存：
   ```bash
   # macOS/Linux
   rm -rf ~/.codex
   
   # Windows
   # 删除 %USERPROFILE%\.codex
   ```

## 故障排除

### 安装失败

#### 权限问题

```bash
# macOS/Linux
sudo chown -R $(whoami) /usr/local/bin

# Windows
# 以管理员身份运行命令提示符
```

#### 网络问题

```bash
# 设置代理
export HTTP_PROXY=http://proxy.example.com:8080
export HTTPS_PROXY=https://proxy.example.com:8080

# 使用镜像
npm config set registry https://registry.npmmirror.com
```

### 构建失败

#### Rust 编译错误

```bash
# 更新 Rust
rustup update

# 清理构建缓存
cargo clean

# 检查依赖
cargo check
```

#### Node.js 构建错误

```bash
# 清理 node_modules
rm -rf node_modules
pnpm install

# 清理缓存
pnpm store prune
```

### 运行时问题

#### 命令未找到

```bash
# 检查 PATH
echo $PATH

# 确保二进制文件在 PATH 中
which codex
```

#### 权限错误

```bash
# macOS/Linux
chmod +x /usr/local/bin/codex

# Windows
# 检查文件权限
```

## 系统特定说明

### macOS 特殊说明

#### M1/M2 芯片支持

Codex CLI 完全支持 Apple Silicon：

```bash
# 下载正确的版本
codex-aarch64-apple-darwin.tar.gz

# 或使用 Rosetta 2（如果需要）
softwareupdate --install-rosetta --agree-to-license
```

#### 权限设置

macOS 可能需要额外的权限：

```bash
# 允许从终端下载的应用
xattr -dr com.apple.quarantine /usr/local/bin/codex
```

### Linux 特殊说明

#### 发行版兼容性

Codex CLI 支持大多数主流 Linux 发行版：

- Ubuntu 18.04+
- Debian 10+
- CentOS 7+
- Fedora 28+
- Arch Linux

#### 依赖项安装

```bash
# Ubuntu/Debian
sudo apt install build-essential curl git libssl-dev pkg-config

# CentOS/RHEL/Fedora
sudo yum groupinstall "Development Tools"
sudo yum install curl git openssl-devel pkgconfig

# Arch Linux
sudo pacman -Syu base-devel curl git openssl pkgconf
```

### Windows 特殊说明

#### Windows Subsystem for Linux (WSL)

Codex CLI 可以在 WSL 中运行：

```bash
# 在 WSL 中安装
wsl
curl -fsSL https://github.com/openai/codex/releases/latest/download/codex-x86_64-unknown-linux-musl.tar.gz | tar -xz
sudo mv codex /usr/local/bin/
```

#### PowerShell 集成

您可以在 PowerShell 中使用 Codex CLI：

```powershell
# 添加到 PATH
[System.Environment]::SetEnvironmentVariable("PATH", $Env:PATH + ";C:\Program Files\codex", "User")

# 重新加载 PATH
$Env:PATH = [System.Environment]::GetEnvironmentVariable("PATH", "User") + ";" + [System.Environment]::GetEnvironmentVariable("PATH", "Machine")
```

## 下一步

安装完成后，您可以：

1. 阅读[快速开始指南](./getting-started.md)
2. 配置[身份验证](./authentication.md)
3. 探索[配置选项](./config.md)
4. 了解[高级功能](./advanced.md)

如果您遇到问题，请查看[常见问题](./faq.md)或[贡献指南](./contributing.md)。