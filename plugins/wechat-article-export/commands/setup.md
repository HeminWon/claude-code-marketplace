---
description: "检查并安装 wechat-article-to-markdown 工具；已安装则跳过"
allowed-tools:
  - Bash(command -v:*)
  - Bash(uv tool install:*)
  - Bash(pipx install:*)
---

# Setup WeChat Article Export Dependencies

## Goal

检查 `wechat-article-to-markdown` 是否已安装；未安装时执行安装。


## Workflow

1. 先检查目标命令是否已安装：
   - `command -v wechat-article-to-markdown`

2. 若已安装：
   - 直接回显“已安装，无需重复安装”。
   - 结束流程，不执行任何安装命令。

3. 若未安装，再检查安装工具：
   - `command -v uv`
   - `command -v pipx`

4. 安装策略（按优先级）：
   - 若存在 `uv`：
     - 执行：`uv tool install --upgrade git+https://github.com/jackwener/wechat-article-to-markdown.git`
   - 否则若存在 `pipx`：
     - 执行：`pipx install --force git+https://github.com/jackwener/wechat-article-to-markdown.git`
   - 否则：
     - 不执行安装，提示用户先安装 `uv` 或 `pipx` 后重试。

5. 输出结果：
   - 回显安装状态、工具检测结果、采用的安装方式（如有）。

## Output Template

- 预检结果：`wechat-article-to-markdown=<found|missing>`
- 工具检测：`uv=<found|missing>, pipx=<found|missing>`
- 安装方式：`<none|uv|pipx>`
- 安装结果：`<already-installed|success|failed|skipped>`
- 下一步：
  - already-installed/success: 可继续使用文章导出相关命令
  - skipped: 请先安装 `uv` 或 `pipx` 后重试
