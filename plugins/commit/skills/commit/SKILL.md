---
name: commit
description: 基于已暂存的变更生成标准化的 git commit message，包含 gitmoji、type/scope、subject/body/footer 规则，以及可选的 commit/push 流程。当用户要求提交信息建议或需要根据 git diff/staged 生成严格格式时使用。
disable-model-invocation: true
argument-hint: "[type] [scope] [subject]"
allowed-tools:
  - Bash(git status:*)
  - Bash(git diff:*)
  - Bash(git config:*)
  - Bash(git branch:*)
  - Bash(git commit:*)
  - Bash(git push:*)
---

# Git Commit Message

## Bundled Resources

- `assets/commit-message-template.md`：提交信息模板与示例

## Arguments

- 原始参数：`$ARGUMENTS`
- 约定格式：`[type] [scope] [subject]`
- 若未提供参数，则完全基于 staged diff 自动推断
- 若仅提供部分参数（如 `type`），其余字段自动推断并让用户确认
- 当 staged diff 过大或噪声过高时，可参考当前对话上下文辅助推断 `subject/body`（仅补充意图，不新增事实）

## Workflow

1. 获取改动信息（仅暂存区，先轻后重）：
   - `git status --short`
   - `git diff --staged --stat`
   - `git diff --staged --name-only`
   - `git config user.name`、`git config user.email`
   - 若暂存区为空，提示用户先 stage 并停止。
   - 仅在以下情况展开 `git diff --staged` 全量内容：类型/范围无法判断、存在 breaking 风险、用户要求精细对比。

2. 分析改动：
   - 识别模块：基于路径/高频词确定模块或组件。
   - 识别类型：`feat`, `fix`, `refactor`, `perf`, `docs`, `style`, `test`, `build`, `chore`, `ci`。
   - 判断范围：单模块使用 `scope`，多模块省略 `scope`。
   - 评估复杂度：简单/中等/复杂（按语义影响判断）。
   - Commit message 语言判断仅统计当前 git user 的最近三次提交，并忽略 MR/解决冲突类提交（例如 subject 含有 `MR`, `merge request`, `resolve conflict(s)`, `conflict`, `merge`, `解决冲突` 等关键词）。
   - 若 staged diff 过大导致成本过高，可参考当前对话上下文压缩语义，但必须先以 staged 事实做锚定。

3. 生成 Commit Message（按改动复杂度自适应生成候选版本数（1-3个））：
   - 格式：`<gitmoji> <type>(<scope>): <subject>`（多模块省略 scope）。
   - Subject：祈使句、<= 50 字符、无句号；各版本一致。
   - Body：简单 1-2 条，中等 2-4 条，复杂 4-6 条。
   - 按改动复杂度自适应生成候选版本数（1-3个）并标注推荐项。
   - 先生成并冻结 Footer（候选间保持一致），候选阶段直接展示完整 Footer；仅在 `BREAKING CHANGE/Refs/Closes` 存在事实差异时允许不同。
   - Footer 顺序如下：
     - 可选：`BREAKING CHANGE: <desc>`、`Refs: #123`、`Closes: #456`
     - 空行
     - `🤖 Generated with <tool> (<model>)`
     - 空行
     - `Co-Authored-By: <AI tool> <email>`
     - `Signed-off-by: Name <email>`（若 `git config` 缺失则省略）

4. 输出与交互：
   - 先输出摘要（files/modules/type/lines/complexity，是否展开 full diff）。
   - 若启用了对话上下文辅助推断，在摘要中显式说明。
   - 按改动复杂度自适应输出候选版本数（1-3个）并标注推荐版本（含完整 Footer）。
   - 提示用户回复：版本号 / `y` 提交推荐版本 / `e` 编辑 / `n` 取消。
   - 单次确认即提交：输入版本号或 `y` 后不再二次确认，直接执行 commit。

5. 提交与推送：
   - 选择版本（或输入 `y`）后直接使用已展示的完整 message 提交。
   - 使用 heredoc 或 `git commit -F <file>` 写入完整多行 commit message（避免多行被 `-m` 压缩），展示 commit hash。
   - commit 成功后再使用 `git branch --show-current` 显示分支并询问是否 `git push`。
   - 选择 `e` 进入交互式编辑 subject/body 后重新生成；Footer 按规则自动重算并保持结构不变。


## 核心规则

- 优先级：staged 事实 > 用户显式参数（`$ARGUMENTS`）> 当前对话上下文
- 首轮仅使用 `--stat + --name-only`，不确定时再读取 `git diff --staged` 全量
- 按改动复杂度自适应生成候选版本数（1-3个）
- 候选阶段直接展示完整 Footer（候选间默认一致并冻结），提交时保持相同内容，并通过 heredoc 或 `-F` 保留多行结构
- Commit message 语言：当前 git user 的近三次 commit → README → 英文（兜底）；自动忽略 MR/解决冲突类提交（关键词规则同上）
- 若对话上下文与 staged 事实冲突，立即降级为仅基于 staged 生成并请求用户确认
- 各版本 Subject 大致相同，差异主要在 Body 详细程度
- 严格遵循 Conventional Commits 和 Gitmoji 规范
- Scope 根据实际文件路径智能识别，无明确模块时可省略

## 规范参考

- Conventional Commits：https://www.conventionalcommits.org/（格式与 `BREAKING CHANGE` 规则）
- Gitmoji：https://gitmoji.dev/（emoji 与语义/类型一致）
