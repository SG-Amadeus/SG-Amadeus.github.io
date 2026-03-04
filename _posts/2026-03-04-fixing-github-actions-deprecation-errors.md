---
title: 修复GitHub Actions弃用错误：actions/upload-artifact v3升级指南
description: 本文详细记录如何解决GitHub Actions中"actions/upload-artifact: v3已弃用"的错误，包括问题诊断、版本升级步骤和验证方法。
author: SG-Amadeus
date: 2026-03-04 00:00:00 +0800
categories: [GitHub, DevOps, CI/CD]
tags: [github actions, ci/cd, automation, deployment, jekyll]
---

# 修复GitHub Actions弃用错误：actions/upload-artifact v3升级指南

## 问题背景

今天在部署Jekyll博客时，GitHub Actions工作流突然失败，错误信息显示：

```
Error: This request has been automatically failed because it uses a deprecated version of `actions/upload-artifact: v3`. Learn more: https://github.blog/changelog/2024-04-16-deprecation-notice-v3-of-the-artifact-actions/
```

根据GitHub官方公告，从2025年1月30日起，GitHub Actions客户将无法再使用v3版本的`actions/upload-artifact`或`actions/download-artifact`。虽然v4版本的artifact动作将上传和下载速度提高了98%并包含多项新功能，但需要更新工作流以适配新版本。

## 错误分析

### 弃用时间线
- **2024年4月16日**：GitHub发布v3弃用公告
- **2024年6月30日**：v1和v2版本弃用计划
- **2025年1月30日**：v3版本正式弃用

### 影响范围
- 使用`actions/upload-artifact@v3`的工作流
- 使用`actions/download-artifact@v3`的工作流
- 依赖这些动作的复合动作（如`actions/upload-pages-artifact@v1`）

### 具体错误
我的Jekyll博客部署工作流（`.github/workflows/pages-deploy.yml`）中使用了`actions/upload-pages-artifact@v1`，这个复合动作内部依赖了已弃用的`actions/upload-artifact@v3`。

## 解决方案

### 工作流文件分析
原始工作流文件包含以下动作版本：
```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v3

  - name: Setup Pages
    uses: actions/configure-pages@v1

  - name: Upload site artifact
    uses: actions/upload-pages-artifact@v1

  - name: Deploy to GitHub Pages
    uses: actions/deploy-pages@v1
```

### 需要更新的动作
| 动作名称 | 旧版本 | 新版本 | 更新原因 |
|----------|--------|--------|----------|
| `actions/checkout` | `v3` | `v4` | 更新到最新稳定版本 |
| `actions/configure-pages` | `v1` | `v4` | 修复页面配置动作 |
| `actions/upload-pages-artifact` | `v1` | `v3` | **关键修复**：解决弃用错误 |
| `actions/deploy-pages` | `v1` | `v4` | 更新部署动作 |

## 实施步骤

### 1. 定位工作流文件
首先找到GitHub Actions工作流文件：
```bash
find /path/to/repo -name "*.yml" -o -name "*.yaml" | grep -E "workflows|actions"
# 输出：/path/to/repo/.github/workflows/pages-deploy.yml
```

### 2. 检查文件内容
```bash
cat .github/workflows/pages-deploy.yml
```

### 3. 执行版本更新
使用文本编辑器或命令行工具更新版本号：

```bash
# 方法1：使用sed命令批量替换
sed -i 's/actions\/checkout@v3/actions\/checkout@v4/g' .github/workflows/pages-deploy.yml
sed -i 's/actions\/configure-pages@v1/actions\/configure-pages@v4/g' .github/workflows/pages-deploy.yml
sed -i 's/actions\/upload-pages-artifact@v1/actions\/upload-pages-artifact@v3/g' .github/workflows/pages-deploy.yml
sed -i 's/actions\/deploy-pages@v1/actions\/deploy-pages@v4/g' .github/workflows/pages-deploy.yml

# 方法2：手动编辑文件
```

### 4. 验证更新结果
更新后的工作流文件内容：
```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4

  - name: Setup Pages
    uses: actions/configure-pages@v4

  - name: Upload site artifact
    uses: actions/upload-pages-artifact@v3

  - name: Deploy to GitHub Pages
    uses: actions/deploy-pages@v4
```

### 5. 提交和推送更改
```bash
# 添加修改的文件
git add .github/workflows/pages-deploy.yml

# 提交更改
git commit -m "Update GitHub Actions workflow versions to fix deprecation errors

- actions/checkout@v3 → v4
- actions/configure-pages@v1 → v4
- actions/upload-pages-artifact@v1 → v3
- actions/deploy-pages@v1 → v4

Fix for: 'This request has been automatically failed because it uses a deprecated version of `actions/upload-artifact: v3`'"

# 推送到远程仓库
git push origin main
```

## 验证修复

### 1. 检查GitHub Actions运行状态
- 访问GitHub仓库的 **Actions** 标签
- 查看"Build and Deploy"工作流运行状态
- 确认工作流成功执行，不再显示弃用错误

### 2. 验证网站部署
- 等待几分钟让GitHub Pages完成部署
- 访问你的GitHub Pages网站：`https://<username>.github.io`
- 确认网站内容正常显示

### 3. 检查工作流日志
在成功的Actions运行中，查看关键步骤的日志：
- ✅ Checkout步骤使用v4版本
- ✅ Configure Pages步骤使用v4版本
- ✅ Upload artifact步骤使用v3版本（修复关键）
- ✅ Deploy步骤使用v4版本

## 经验总结

### 关键学习点
1. **定期更新依赖**：GitHub Actions动作版本需要定期检查更新
2. **关注官方公告**：订阅GitHub博客和变更日志，及时了解弃用通知
3. **测试工作流**：在非生产环境测试工作流更新，确保兼容性
4. **版本迁移策略**：制定系统性的版本迁移计划

### v4版本的优势
根据GitHub官方文档，v4版本的artifact动作带来以下改进：
- **性能提升**：上传和下载速度提高98%
- **新增功能**：更好的缓存机制、并行上传支持
- **安全增强**：改进的权限控制和访问管理
- **兼容性**：向后兼容大部分v3工作流配置

### 预防措施
1. **版本锁定策略**：使用特定版本号而非分支引用
2. **定期审查**：每季度审查一次工作流文件中的动作版本
3. **自动化检查**：使用Dependabot或类似工具自动检测过时依赖
4. **文档记录**：维护工作流版本更新日志

## 常见问题解答

### Q1: 为什么`actions/upload-pages-artifact@v1`会导致v3弃用错误？
A1: `actions/upload-pages-artifact@v1`是一个复合动作，它内部依赖`actions/upload-artifact@v3`。虽然你直接使用的是v1，但底层依赖的是已弃用的v3。

### Q2: 更新后是否需要重新配置工作流？
A2: 大多数情况下不需要。v4版本设计为向后兼容，但建议在更新后运行完整的工作流测试。

### Q3: 如果其他动作也出现类似错误怎么办？
A3: 检查工作流中所有动作的版本，特别是：
- `actions/setup-node`
- `actions/setup-python`
- `actions/cache`
- 其他GitHub官方动作

### Q4: 更新版本会影响现有的构建产物吗？
A4: 不会。已上传的构建产物在其保留期内仍可通过UI或REST API访问，不受版本更新的影响。

## 结论

GitHub Actions的版本弃用是一个持续的过程，保持工作流文件更新是维护CI/CD流水线健康的重要部分。通过这次修复，我们不仅解决了当前的构建错误，还建立了一个可持续的版本维护策略。

**关键建议**：建立定期的依赖检查机制，优先使用最新稳定版本，并在非关键分支测试更新后再合并到主分支。这样可以确保在正式弃用日期前完成迁移，避免生产环境的中断。

自动化工具的维护需要与技术发展同步，只有持续更新和学习，才能充分发挥其价值。希望这次经验分享能帮助其他开发者顺利解决类似的GitHub Actions弃用问题。

> **本文档生成说明**：本文章由Claude Code根据实际修复GitHub Actions弃用错误的会话过程自动生成，展示了AI辅助开发在技术问题解决和文档编写中的应用。