---
description: "检查并安装 wechat-article-to-markdown 工具；已安装则跳过"
allowed-tools:
  - Bash(command -v:*)
  - Bash(uv tool install:*)
  - Bash(pipx install:*)
---

# Setup WeChat Article Export Dependencies

## Goal

确保 `wechat-article-to-markdown` 可用；若已安装则直接结束。

## Workflow

1. 检查命令是否存在：
   - `command -v wechat-article-to-markdown`
2. 若已安装：
   - 输出“已安装，无需重复安装”。
   - 结束流程。
3. 若未安装，检测安装工具：
   - `command -v uv`
   - `command -v pipx`
4. 安装优先级（固定版本 `50b7e63`，避免不兼容升级）：
   - 有 `uv`：`uv tool install git+https://github.com/jackwener/wechat-article-to-markdown.git@50b7e63`
   - 否则有 `pipx`：`pipx install --force git+https://github.com/jackwener/wechat-article-to-markdown.git@50b7e63`
   - 两者都无：不安装，提示用户先安装 `uv` 或 `pipx`。
5. 输出固定结果字段。

## Output Template

- 预检结果：`wechat-article-to-markdown=<found|missing>`
- 工具检测：`uv=<found|missing>, pipx=<found|missing>`
- 安装方式：`<none|uv|pipx>`
- 安装结果：`<already-installed|success|failed|skipped>`
- 下一步：
  - already-installed/success: 可继续使用 `wechat-article-export` 相关命令
  - skipped: 请先安装 `uv` 或 `pipx` 后重试
