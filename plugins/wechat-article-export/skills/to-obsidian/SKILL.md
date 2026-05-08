---
name: to-obsidian
description: 将微信公众号文章导出为 Markdown 并整理到 Obsidian 目录；缺少必要参数时通过 AskUserQuestion 向用户补齐。
disable-model-invocation: true
allowed-tools:
  - Bash
  - AskUserQuestion
  - Skill
---

# To Obsidian

## 用途

把微信文章链接转换为 Markdown，并按 Obsidian 友好的目录结构落盘。

## 调用

`wechat-article-to-markdown <微信公众号文章链接>`

## 参数

- 文章链接（必填）：`mp.weixin.qq.com` 文章地址。
- 保存目录（必填）：Obsidian 仓库内相对路径，例如 `xxx/xxx`。

若缺少保存目录，必须先用 `AskUserQuestion` 获取，不得使用默认路径替代。

## 执行流程

### 1) 参数与路径检查

1. 未提供文章链接：使用 `AskUserQuestion` 询问。
2. 未提供保存目录：使用 `AskUserQuestion` 询问（明确要求“仓库内相对路径”）。
3. 保存目录若不存在：先提示并让用户确认是否创建。
4. 拒绝绝对路径，仅允许相对路径。

### 2) 抓取并转换

调用 `wechat-article-to-markdown`（或对应封装 skill）完成文章抓取与 Markdown 转换。

关键要求是：完成转换后，需拿到并记录后续重组所需的信息，包括：

- 文章标题
- 作者
- 发布时间
- Markdown 产物路径（优先从命令输出获取）

优先从工具输出中提取这些信息；若输出格式与示例不一致，可从已生成文件路径与目录结构回溯定位。若无法确定 Markdown 产物路径，则视为转换失败并停止。

### 3) 文件重组（只用 mv）

以文章标题建立目标结构：

```text
保存目录/文章标题/
├── 文章标题.md
└── assets/
    └── 文章标题/
        ├── image-001.png
        ├── image-002.png
        └── ...
```

要求：

- 源 Markdown 与图片目录应基于可验证信息定位：优先使用命令输出中的产物路径，其次根据实际生成目录回溯确认；禁止无依据猜测路径。
- 将生成的 `.md` **移动（mv）**到 `保存目录/文章标题/文章标题.md`。
- 将图片资源 **移动（mv）** 到 `保存目录/文章标题/assets/文章标题/`。
- 禁止使用 `cp`，避免残留副本。
- 完成后删除转换工具产生的源目录（如 `xxx/文章标题/`）及空目录。
- 路径包含空格时必须加引号。

### 4) Obsidian 格式整理

调用 `obsidian-markdown` skill，仅做格式层面的规范化(图片资源路径信息更新)，正文内容不得改写。

硬性约束：

- 正文内容一个字不改（文本、代码块、引用内容均保持原样）。
- 图片链接统一为标准 Markdown 语法（`![alt](path)`），不使用 wikilink。
- frontmatter 至少补齐：`title`、`source`、`author`、`created`.
- 标题层级正确：单一 H1，子级连续不跳级。
- 统一空行、列表缩进（每级 2 空格）、引用与代码块格式。
- 表格补齐表头分隔并对齐；分割线统一 `---`。
- 中英文、中文与数字之间按规范补空格。

## 输出

结束时返回最终文件路径，例如：

`已保存到 xxx/xxx/文章标题/文章标题.md`

## 失败策略

- 抓取或转换失败：立即报告原因并停止后续步骤。
- 路径非法或不可写：提示修正后重试。
- 任何失败都不要继续执行后续链路。
