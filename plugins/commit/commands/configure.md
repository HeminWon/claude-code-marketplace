---
description: "配置当前仓库的 commit message 语言（git local: claude.commit.lang），支持自然语言输入并范化为 locale code"
allowed-tools:
  - Bash(git rev-parse:*)
  - Bash(git config:*)
  - AskUserQuestion
---

# Configure Commit Language

## Goal

为当前 git 仓库设置 commit message 语言优先项：`claude.commit.lang`（local scope）。

## Inputs

- 原始参数：`$ARGUMENTS`
- 支持两类输入：
  - locale code（如 `en`, `fr-CA`, `pt_BR`, `zh-Hant-TW`）
  - 自然语言名称（如 `中文`, `English`, `日本語`, `Português`）
- `auto` / `default` / `自动` 表示重置为自动推断（unset local config）

## Workflow

1. 校验仓库上下文：
   - 执行 `git rev-parse --is-inside-work-tree`
   - 若不在 git 仓库中，提示用户切换到仓库后重试。

2. 读取当前值：
   - 执行 `git config --local --default auto --get claude.commit.lang`
   - 返回 `auto` 代表未设置本地值（正常场景，不是错误）。

3. 解析目标语言：
   - 优先读取 `$ARGUMENTS`。
   - 若 `$ARGUMENTS` 为空，必须先用 `AskUserQuestion` 提供单选列表：
     - `中文（zh-CN）`
     - `English（en）`
     - `日本語（ja）`
     - `한국어（ko）`
     - `Auto（重置为自动推断）`
   - 若用户选择 `Other`，再让用户自由输入目标语言（支持 code 或自然语言）。
   - 将输入范化：
     - trim + collapse spaces
     - `_` 统一替换为 `-`
     - locale 分段规范化：语言段小写、地区段大写、脚本段首字母大写（如 `EN_us` -> `en-US`, `zh-hant-tw` -> `zh-Hant-TW`）
   - 语义映射策略：
     - 若输入已是有效 locale code 形态，直接采用范化结果。
     - 若输入是自然语言，转换为对应 locale code（尽量保留用户语义；必要时补全默认地区）。
     - 若存在歧义（如 Portuguese 可能是 `pt-PT` 或 `pt-BR`），通过追问让用户二选一，不做硬错误终止。
     - 仅在用户明确表示自动推断（`auto/default/自动`）时走重置分支。

4. 写入配置：
   - 若目标为自动：执行 `git config --local --unset claude.commit.lang`（key 不存在也按成功处理）。
   - 否则执行 `git config --local claude.commit.lang <normalized_code>`。

5. 结果回显：
   - 再次读取 `git config --local --get claude.commit.lang`
   - 无值回显 `auto`，有值回显最终 code。
   - 提示后续 `commit` 命令会优先使用该配置。

## Output Template

- 当前值：`<current|auto>`
- 目标输入：`<user_input>`
- 应用结果：`<final_code|auto>`
- 说明：`commit` 将优先读取 `git config --local claude.commit.lang`
