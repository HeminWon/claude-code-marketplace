---
name: wechat-article-export
description: 下载并转换微信公众号文章为 Markdown，返回标题、作者、发布时间与产物路径。
disable-model-invocation: true
allowed-tools:
  - Bash
  - AskUserQuestion
---

# WeChat Article Export Skill

## 用途

仅负责下载并转换微信公众号文章为 Markdown，并返回产物信息。

## 调用

`wechat-article-to-markdown <微信公众号文章链接>`

## 参数

- 文章链接（必填）：`mp.weixin.qq.com` 文章地址
- 缺少参数时必须先用 `AskUserQuestion` 获取

## 执行流程

### 1) 参数检查

1. 未提供文章链接：使用 `AskUserQuestion` 询问。
2. 校验链接为 `mp.weixin.qq.com` 文章页（非主页、频道页或未展开短链态）。
3. 链接非法时立即报错并停止。

### 2) 抓取并转换

执行 `wechat-article-to-markdown`，并从命令输出或生成文件中提取：

- `title`
- `author`
- `created`
- `markdown_path`（优先来自命令输出）

规则：

- 仅使用可验证信息，不猜测路径。
- 无法确定 `markdown_path` 时视为失败并停止。

## 输出

结束时输出结构化结果，至少包含：

```yaml
title: "示例标题"
author: "示例作者"
created: "2026-05-08T10:30:00+08:00"
markdown_path: "/absolute/or/relative/path/to/article.md"
```

## 失败策略

- 抓取或转换失败：报告原因并停止。
- 无法提取关键产物信息：报告原因并停止。
