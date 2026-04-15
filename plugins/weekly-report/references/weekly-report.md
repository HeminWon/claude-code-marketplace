---
description: 自动生成工作周报，统计当前用户最近 7 天在所有分支的提交记录
allowed-tools: Bash(git config:*), Bash(git log:*), Bash(git rev-parse:*), Bash(date:*)
---

> 说明：`Bash(date:*)` 仅用于生成周报展示字段（ISO 周标识、展示时间范围），不用于提交筛选。

## 执行步骤

### 1. 获取 Git 用户信息
- 使用 `git config user.email` 获取当前用户邮箱（用于匹配提交记录）
- 使用 `git config user.name` 获取当前用户名（用于周报显示）

### 2. 计算展示用时间范围
用于展示周报头部信息（不参与提交筛选）：
- ISO 周标识：`date "+%G-W%V"`
- 展示时间范围：最近 7 天的起止日期（如 `YYYY-MM-DD ~ YYYY-MM-DD`）
- 若 `date -v` 不可用，使用当前环境兼容的 `date` 方案替代

### 3. 收集 Git 提交记录（唯一筛选依据）
使用 `git log --author="<用户邮箱>" --since="7 days ago" --pretty=format:"%h|%ad|%s" --date=short --all`

说明：最近 7 天的提交筛选仅依赖 `--since="7 days ago"`；第 2 步的日期仅用于展示。

### 4. 分析和整理提交数据
- 按日期分组提交记录
- 解析提交的 gitmoji 和类型（feat, fix, docs, refactor, test, style, perf 等）
- 识别重点功能（语义判断）：
  - 优先收录 `feat`；
  - `fix` 中若语义上属于高影响问题（如 crash、regression、安全、生产故障）则收录；
  - 重点条目控制在 1~3 条，避免冗长。
- 动态统计所有出现的提交类型及其数量（只显示本周实际存在的类型）

### 5. 生成并显示周报
按以下格式在终端显示周报（emoji 可选）：

```markdown
# 工作周报 (YYYY-Www)

**时间范围:** YYYY-MM-DD ~ YYYY-MM-DD / **作者:** <用户名> (<用户邮箱>)

## 📅 工作时间线
### YYYY-MM-DD (星期X)
- ✨ feat: 功能描述
- 🐛 fix: 修复描述

## 🎯 本周重点
1. **重点功能** - 需求点及价值说明

## 📊 统计信息
- 总提交数: X
- 按类型统计 (动态显示)
```

## 注意事项

- 确保在 git 仓库中执行
- 时间线按日期倒序排列（最新在上）
- 如无提交记录则提示用户
