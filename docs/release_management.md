# 发布管理

本文档介绍了 Codex CLI 的发布流程、版本管理和相关策略。

## 概述

Codex CLI 采用语义化版本控制和结构化的发布流程，确保软件质量和用户体验。

## 版本控制

### 语义化版本

Codex CLI 遵循 [语义化版本 2.0.0](https://semver.org/) 规范：

- **主版本号**：不兼容的 API 更改
- **次版本号**：向后兼容的功能新增
- **修订号**：向后兼容的问题修复

版本格式：`主版本号.次版本号.修订号`

示例：
- `1.0.0` - 初始稳定版本
- `1.1.0` - 添加新功能，向后兼容
- `1.1.1` - 修复问题，向后兼容
- `2.0.0` - 重大更新，可能不兼容

### 版本类型

#### 稳定版本

- 格式：`X.Y.Z`
- 适合生产环境
- 经过充分测试
- 提供长期支持

#### 发布候选版本

- 格式：`X.Y.Z-rc.N`
- 接近稳定版本
- 需要社区测试
- 可能包含最终版本的预览

#### 测试版本

- 格式：`X.Y.Z-beta.N` 或 `X.Y.Z-alpha.N`
- 早期功能预览
- 可能不稳定
- 适合开发者和测试者

#### 开发版本

- 格式：`X.Y.Z-dev.N` 或基于 Git 提交
- 最新开发进展
- 不保证稳定性
- 仅用于开发测试

## 发布流程

### 1. 发布规划

#### 发布周期

- **主要版本**：每 6-12 个月
- **次要版本**：每 1-3 个月
- **修订版本**：根据需要，通常每周

#### 发布检查清单

- [ ] 功能开发完成
- [ ] 单元测试通过
- [ ] 集成测试通过
- [ ] 文档更新
- [ ] 变更日志更新
- [ ] 安全审查
- [ ] 性能测试
- [ ] 兼容性测试

### 2. 分支管理

#### Git 分支策略

```
main                    # 主分支，始终可发布
├── release/v1.0.0      # 发布准备分支
├── feature/new-auth    # 功能开发分支
├── hotfix/security-fix # 紧急修复分支
└── develop             # 开发集成分支
```

#### 分支保护

- **main 分支**：需要 PR 审查和 CI 通过
- **release 分支**：限制直接推送
- **hotfix 分支**：紧急情况下允许直接推送

### 3. 发布准备

#### 版本号更新

```bash
# 更新版本号
npm version patch   # 修订版本
npm version minor   # 次要版本
npm version major   # 主要版本
```

#### 变更日志更新

```markdown
## [1.2.0] - 2024-01-15

### 新增
- 添加多语言支持 (#123)
- 实现批量文件处理 (#124)

### 修复
- 修复内存泄漏问题 (#120)
- 改进错误处理 (#121)

### 变更
- 更新依赖项到最新版本 (#122)
```

#### 构建测试

```bash
# 运行完整测试套件
npm run test:all

# 构建所有平台二进制文件
npm run build:all

# 验证构建结果
npm run verify:build
```

### 4. 发布执行

#### 创建发布标签

```bash
# 创建并推送标签
git tag -a v1.2.0 -m "Version 1.2.0"
git push origin v1.2.0
```

#### 构建发布包

```bash
# 构建发布包
npm run package

# 验证包完整性
npm run verify:package
```

#### 发布到仓库

```bash
# 发布到 npm
npm publish

# 发布到 GitHub Releases
gh release create v1.2.0 --title "Version 1.2.0" --notes "Release notes"
```

### 5. 发布后任务

#### 通知用户

- 更新网站和文档
- 发送发布通知邮件
- 在社区平台发布公告
- 更新社交媒体

#### 监控反馈

- 监控问题报告
- 收集用户反馈
- 跟踪使用指标
- 分析错误报告

#### 维护更新

- 修复发现的问题
- 准备下一个版本
- 更新文档和示例
- 清理旧版本

## 变更管理

### 变更日志格式

使用 [Keep a Changelog](https://keepachangelog.com/) 格式：

```markdown
# 变更日志

本项目的重要变更记录。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
本项目遵循 [语义化版本](https://semver.org/spec/v2.0.0.html)。

## [未发布]

### 新增
- 正在开发的功能

### 变更
- 计划中的改进

## [1.2.0] - 2024-01-15

### 新增
- 添加多语言支持 (#123)
- 实现批量文件处理 (#124)

### 修复
- 修复内存泄漏问题 (#120)
- 改进错误处理 (#121)

### 变更
- 更新依赖项到最新版本 (#122)

## [1.1.0] - 2023-12-01

### 新增
- 初始版本功能
- 基础 API 实现
```

### 变更分类

#### 新增 (Added)
- 新功能
- 新 API
- 新配置选项

#### 变更 (Changed)
- 现有功能的改进
- 配置变更
- 性能优化

#### 废弃 (Deprecated)
- 计划移除的功能
- 不推荐使用的 API

#### 移除 (Removed)
- 已废弃的功能
- 删除的 API

#### 修复 (Fixed)
- Bug 修复
- 安全问题修复
- 稳定性改进

#### 安全 (Security)
- 安全漏洞修复
- 安全增强
- 权限改进

## 质量保证

### 自动化测试

#### 单元测试

```bash
# 运行单元测试
npm run test:unit

# 生成覆盖率报告
npm run test:coverage

# 监控模式运行测试
npm run test:watch
```

#### 集成测试

```bash
# 运行集成测试
npm run test:integration

# 端到端测试
npm run test:e2e

# 性能测试
npm run test:performance
```

#### 兼容性测试

```bash
# 跨平台测试
npm run test:cross-platform

# 向后兼容性测试
npm run test:compatibility

# 依赖项兼容性测试
npm run test:dependencies
```

### 代码质量

#### 静态分析

```bash
# 代码风格检查
npm run lint

# 安全扫描
npm run audit

# 复杂度分析
npm run complexity
```

#### 动态分析

```bash
# 内存分析
npm run profile:memory

# CPU 性能分析
npm run profile:cpu

# 网络请求分析
npm run profile:network
```

## 发布自动化

### CI/CD 流水线

#### GitHub Actions 配置

```yaml
name: Release
on:
  push:
    tags:
      - 'v*'

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node-version: [18, 20]
    
    steps:
    - uses: actions/checkout@v4
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run tests
      run: npm test
    
    - name: Build
      run: npm run build

  release:
    needs: test
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: 20
        registry-url: 'https://registry.npmjs.org'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build packages
      run: npm run build:all
    
    - name: Publish to npm
      run: npm publish
      env:
        NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
    
    - name: Create GitHub Release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.ref }}
        release_name: Release ${{ github.ref }}
        draft: false
        prerelease: false
```

#### 自动化脚本

```bash
#!/bin/bash
# release.sh - 自动化发布脚本

set -e

# 检查当前分支
current_branch=$(git branch --show-current)
if [ "$current_branch" != "main" ]; then
    echo "Error: Must be on main branch"
    exit 1
fi

# 检查工作目录状态
if [ -n "$(git status --porcelain)" ]; then
    echo "Error: Working directory is not clean"
    exit 1
fi

# 获取版本号
version=$1
if [ -z "$version" ]; then
    echo "Usage: $0 <version>"
    exit 1
fi

# 更新版本号
npm version "$version"

# 运行测试
npm test

# 构建项目
npm run build

# 创建标签
git tag -a "v$version" -m "Version $version"

# 推送更改
git push origin main
git push origin "v$version"

# 发布到 npm
npm publish

echo "Release $version completed successfully"
```

## 紧急发布流程

### 热修复流程

#### 1. 问题评估

```bash
# 评估问题严重性
severity=$(./scripts/assess-severity.sh $issue_id)

if [ "$severity" = "critical" ]; then
    echo "启动紧急发布流程"
fi
```

#### 2. 创建热修复分支

```bash
# 从最新标签创建分支
latest_tag=$(git describe --tags --abbrev=0)
git checkout -b "hotfix/$issue_id" "$latest_tag"
```

#### 3. 修复和测试

```bash
# 应用修复
git apply "fixes/$issue_id.patch"

# 运行关键测试
npm run test:critical

# 验证修复
npm run verify:fix
```

#### 4. 发布热修复

```bash
# 更新版本号（修订版本）
npm version patch

# 创建热修复标签
git tag -a "v$(npm pkg get version)" -m "Hotfix: $issue_id"

# 推送修复
git push origin main
git push origin --tags

# 发布热修复版本
npm publish
```

#### 5. 合并到主分支

```bash
# 切换到主分支
git checkout main

# 合并热修复
git merge "hotfix/$issue_id"

# 删除热修复分支
git branch -d "hotfix/$issue_id"
```

## 版本兼容性

### 向后兼容性策略

#### API 兼容性

- 公共 API 保持向后兼容
- 废弃的 API 至少保留一个主要版本
- 提供迁移指南和工具

#### 配置兼容性

- 配置文件格式保持兼容
- 新增配置项提供默认值
- 废弃配置项发出警告

#### 数据格式兼容性

- 数据文件格式保持兼容
- 提供数据迁移工具
- 支持多版本数据格式

### 兼容性测试

```bash
# 测试不同版本兼容性
npm run test:compatibility -- --from-version=1.0.0 --to-version=1.2.0

# 测试配置兼容性
npm run test:config-compatibility

# 测试数据格式兼容性
npm run test:data-compatibility
```

## 发布通知

### 通知渠道

#### 邮件通知

```markdown
主题: [Codex CLI] 版本 1.2.0 发布

正文:
尊敬的用户，

Codex CLI 版本 1.2.0 现已发布！

主要更新：
- 添加多语言支持
- 实现批量文件处理
- 修复内存泄漏问题

详细信息请查看：
- 变更日志: https://github.com/openai/codex/releases/tag/v1.2.0
- 升级指南: https://docs.codex.com/upgrade/1.2.0

如有问题，请通过 GitHub Issues 反馈。

谢谢！
Codex CLI 团队
```

#### 社交媒体

```markdown
🎉 Codex CLI v1.2.0 发布！

✨ 新功能：
- 多语言支持
- 批量文件处理
- 性能优化

🐛 修复：
- 内存泄漏问题
- 错误处理改进

📥 立即更新: npm install -g @openai/codex
📖 变更日志: https://github.com/openai/codex/releases/tag/v1.2.0

#CodexCLI #Release #DeveloperTools
```

#### 内部通知

```markdown
## 发布通知 - v1.2.0

**发布时间**: 2024-01-15 14:30 UTC
**发布负责人**: @release-manager
**版本类型**: 次要版本

### 发布内容
- 功能: 多语言支持、批量文件处理
- 修复: 内存泄漏、错误处理
- 变更: 依赖项更新

### 测试结果
- ✅ 单元测试: 100% 通过
- ✅ 集成测试: 100% 通过
- ✅ 性能测试: 通过
- ✅ 安全测试: 通过

### 发布状态
- ✅ npm 发布: 完成
- ✅ GitHub Release: 完成
- ✅ 文档更新: 完成
- ✅ 通知发送: 完成

### 监控事项
- 用户反馈跟踪
- 错误率监控
- 性能指标观察
```

## 版本生命周期

### 版本支持策略

#### 长期支持 (LTS) 版本

- 支持期限：12 个月
- 安全更新：定期提供
- Bug 修复：关键问题
- 功能更新：不提供

#### 当前稳定版本

- 支持期限：6 个月
- 所有更新：提供
- 功能更新：包括新功能

#### 旧版本

- 支持期限：3 个月
- 仅安全更新：关键问题
- 建议升级：到新版本

### 版本弃用计划

```markdown
## 版本弃用时间表

### 版本 1.0.x
- **弃用日期**: 2024-03-01
- **停止支持**: 2024-06-01
- **推荐升级**: 1.2.0 或更高版本

### 版本 1.1.x
- **弃用日期**: 2024-06-01
- **停止支持**: 2024-09-01
- **推荐升级**: 1.2.0 或更高版本

### 版本 1.2.x
- **当前版本**: 2024-01-15
- **预计弃用**: 2024-12-01
- **预计停止支持**: 2025-03-01
```

## 监控和指标

### 发布指标

#### 下载统计

```bash
# 获取 npm 下载统计
npm view @openai/codex downloads

# 按版本统计下载量
npm view @openai/codex downloads --version=1.2.0
```

#### 错误监控

```bash
# 监控错误率
npm run monitor:errors

# 按版本分析错误
npm run monitor:errors -- --version=1.2.0
```

#### 性能指标

```bash
# 监控性能指标
npm run monitor:performance

# 比较版本性能
npm run monitor:performance -- --compare=1.1.0
```

### 用户反馈

#### 问题跟踪

```bash
# 分析新版本问题
npm run analyze:issues -- --since=2024-01-15

# 统计问题类型
npm run analyze:issues -- --type=bug -- --version=1.2.0
```

#### 满意度调查

```markdown
## 用户满意度调查

### 版本 1.2.0 反馈

**总体满意度**: 4.2/5.0

**功能评价**:
- 多语言支持: 4.5/5.0
- 批量处理: 4.0/5.0
- 性能改进: 4.1/5.0

**问题反馈**:
- 文档需要改进 (3 个反馈)
- 某些功能不稳定 (2 个反馈)
- 希望添加更多示例 (1 个反馈)
```

## 最佳实践

### 发布准备

1. **充分测试**: 确保所有测试通过
2. **文档更新**: 更新所有相关文档
3. **兼容性检查**: 验证向后兼容性
4. **性能验证**: 确保性能没有退化

### 发布执行

1. **自动化流程**: 尽可能自动化发布过程
2. **逐步发布**: 考虑分批发布到不同用户组
3. **监控密切**: 发布后密切监控系统状态
4. **快速响应**: 准备快速响应发现的问题

### 发布后跟进

1. **收集反馈**: 主动收集用户反馈
2. **分析数据**: 分析发布数据和指标
3. **持续改进**: 基于反馈改进发布流程
4. **文档更新**: 更新发布相关的文档

## 下一步

了解发布管理后，您可以：

- 查看[贡献指南](./contributing.md)
- 了解[版本历史](https://github.com/openai/codex/releases)
- 探索[配置选项](./config.md)

如果您需要帮助，请查看[常见问题](./faq.md)或[贡献指南](./contributing.md)。