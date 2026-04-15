---
name: weekly-report
description: 基于 Git 历史生成最近 7 天的工作周报，按日期分组并统计提交类型与重点。用于用户请求 weekly report / weekly summary / work log / commit summary 时。
metadata:
  short-description: 基于最近 7 天 Git 提交生成中文工作周报
---

# Weekly Report

## 概述

基于当前用户的 Git 提交记录生成最近 7 天的中文周报。

## 工作流（精简版）

1. 确认在 Git 仓库内，失败则明确提示并停止。
2. 获取用户信息（`git config user.email` / `git config user.name`），邮箱缺失需用户补充。
3. 计算 ISO 周标识与时间范围（最近 7 天）。
4. 收集提交记录并按日期分组，解析 gitmoji/type，统计类型与重点。
5. 按模板输出中文周报。

## 规范来源

详细步骤、命令、模板与格式规范见 `./references/weekly-report.md`（单一事实来源）。
