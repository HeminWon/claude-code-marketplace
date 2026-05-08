---
description: "将 to-obsidian 流程编排到 command：参数校验、下载转换、文件重组与按 Obsidian Markdown 规范整理"
allowed-tools:
  - AskUserQuestion
  - EnterPlanMode
  - ExitPlanMode
  - Bash(wechat-article-to-markdown:*)
  - Bash(test:*)
  - Bash(mkdir:*)
  - Bash(mv:*)
  - Bash(find:*)
  - Bash(rmdir:*)
  - Bash(dirname:*)
  - Bash(basename:*)
  - Skill
---

# To Obsidian Command

## Goal

把微信公众号文章链接转换为 Markdown，并落盘为 Obsidian 友好的目录结构。

## Inputs

- 原始参数：`$ARGUMENTS`
- 必填：
  - 文章链接（`mp.weixin.qq.com`）
  - 保存目录（Obsidian 仓库内相对路径，如 `xxx/xxx`）

## Workflow

### 1) 参数与路径检查

1. 从 `$ARGUMENTS` 解析文章链接与保存目录。
2. 缺少任一必填参数时，使用 `AskUserQuestion` 补齐。
3. 保存目录必须是仓库内相对路径：
   - 以 `/` 开头（绝对路径）→ 报错并停止。
   - 包含 `..`（如 `../`、`..\\`、独立 `..` 段）→ 报错并停止。
4. 目录存在性检查：
   - `test -d "<保存目录>"`
   - 不存在时先询问是否创建；确认后 `mkdir -p "<保存目录>"`，拒绝则停止。

### 2) 抓取并转换（调用 skill）

调用 `wechat-article-export` skill 执行下载与转换，不在本 command 内重复实现。

完成后必须拿到并记录：

- 文章标题
- 作者
- 发布时间
- Markdown 产物路径

规则：

- 优先从工具输出提取。
- 输出不一致时，可基于已生成文件和目录结构回溯定位。
- 无法确认 Markdown 路径则视为失败并停止。

### 3) 文件重组（仅用 `mv`）

目标结构：

```text
保存目录/
├── safe_title.md
└── assets/
    └── a1b2c3/
        ├── image-001.png
        ├── image-002.png
        └── ...
```

执行规则：

1. 先定位源 `.md` 与图片目录（可验证优先，禁止猜测）。
2. 生成 `safe_title`：
   - 将 `/ \\ : * ? " < > |` 替换为 `-`
   - 去首尾空白与尾随 `.`，连续空白折叠为单个空格
   - 为空时回退为 `untitled-article`
3. 生成 6 位小写字母或数字的 `asset_id`，并创建目录：
   - `mkdir -p "<保存目录>/assets/${asset_id}"`
4. 移动文件：
   - `mv "<源md路径>" "<保存目录>/<safe_title>.md"`
   - 图片资源 `mv` 到 `"<保存目录>/assets/${asset_id}/"`
5. 禁止 `cp`。
6. 重组后仅在目录已空时使用 `rmdir` 清理源目录（可逐级）。
7. 路径含空格必须加引号。

### 4) Obsidian Markdown 规范整理

仅做格式整理，不改动事实信息。

允许项：

- 图片链接统一为标准 Markdown：`![alt](path)`（不用 wikilink）
- frontmatter 至少补齐：`title`、`source`、`author`、`created`
- 标题层级修正为单一 H1，子级不跳级
- 统一空行、列表缩进（每级 2 空格）、引用、代码块
- 表格补齐表头分隔并对齐
- 分割线统一为 `---`
- 中英文、中文与数字之间按规范补空格

执行顺序（先计划、后确认、再改动）：

1. 在任何 Markdown 文本改动前，先进入 Plan 模式（`EnterPlanMode`）。
2. 读取当前 Markdown 后，输出“拟变更点清单”，至少包含：
   - 将新增或修正的 frontmatter 字段
   - 图片路径/写法将如何调整（含目标路径模式）
   - 标题层级、空行/缩进、表格、分割线、空格规范中将触发的具体项
3. 使用 `ExitPlanMode` 展示计划并请求用户批准。
4. 仅在用户同意后执行规范化编辑；用户拒绝时，仅完成文件重组并保留原文。

## Output

返回固定字段（按实际填值）：

```text
title: <文章标题>
author: <作者>
created: <发布时间>
markdown_path: <源md路径>
final_path: <保存目录>/<safe_title>.md
assets_path: <保存目录>/assets/<asset_id>/
```

可附加：`已保存到 <保存目录>/<safe_title>.md`

## Failure Policy

- 抓取或转换失败：报告原因并停止。
- 路径非法或不可写：提示修正后重试。
- 任一步骤失败：不继续后续链路。
