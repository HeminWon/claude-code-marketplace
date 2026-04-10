# Commit Message Template

## Format

```text
<gitmoji> <type>(<scope>): <subject>

<body>

<footer>
```

## Example

### 阶段 1：候选输出（1-3 个，直接展示完整 Footer）

> 说明：候选版本的 Subject 保持一致，差异主要在 Body 详细程度。

```text
v1 (recommended)
✨ feat(auth): add token refresh on expired sessions

- refresh token when access token is expired

Refs: #123

🤖 Generated with <tool> (<model>)

Co-Authored-By: <AI tool> <email>
Signed-off-by: Your Name <you@example.com>
```

```text
v2
✨ feat(auth): add token refresh on expired sessions

- refresh token when access token is expired
- persist refresh timestamp for audit

Refs: #123

🤖 Generated with <tool> (<model>)

Co-Authored-By: <AI tool> <email>
Signed-off-by: Your Name <you@example.com>
```

```text
v3
✨ feat(auth): add token refresh on expired sessions

- refresh token when access token is expired
- persist refresh timestamp for audit
- add refresh failure log for troubleshooting

Refs: #123

🤖 Generated with <tool> (<model>)

Co-Authored-By: <AI tool> <email>
Signed-off-by: Your Name <you@example.com>
```

### 阶段 2：交互指令示例

```text
输入版本号：提交对应版本（如 1 / 2 / 3）
y：直接提交推荐版本
e：编辑 subject/body 后重新生成
n：取消
```

### 阶段 3：最终提交格式（与候选一致）

```text
✨ feat(auth): add token refresh on expired sessions

- refresh token when access token is expired
- persist refresh timestamp for audit

Refs: #123

🤖 Generated with <tool> (<model>)

Co-Authored-By: <AI tool> <email>
Signed-off-by: Your Name <you@example.com>
```

```text
🐛 fix(api): handle null response in user profile

- guard against null payload in profile endpoint
- return 404 when user is missing

Closes: #456

🤖 Generated with <tool> (<model>)

Co-Authored-By: <AI tool> <email>
Signed-off-by: Your Name <you@example.com>
```
