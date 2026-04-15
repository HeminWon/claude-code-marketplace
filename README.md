# claude-code-marketplace

Public Claude Code plugin marketplace with curated plugins for development workflows.

## Marketplace Name

`hemin-marketplace`

## Available Plugins

- `commit` — Generate standardized git commit messages and commit directly.

## Usage

### Via `npx skills` (multi-agent)

```bash
npx skills add HeminWon/claude-code-marketplace
```

Supports Claude Code, Cursor, Codex, and [40+ more agents](https://github.com/vercel-labs/skills#supported-agents).

---

### Via Claude Code plugin system

### 1) Add marketplace

```bash
/plugin marketplace add HeminWon/claude-code-marketplace
```

### 2) Install plugin

```bash
/plugin install commit@hemin-marketplace
```

### 3) Configure commit language (local repo)

```bash
# set by language name (will be normalized to locale code)
/commit:configure 中文

# set by locale code
/commit:configure pt-BR

# reset to auto-detect
/commit:configure auto
```

### 4) Update marketplace and plugin versions

```bash
/plugin marketplace update hemin-marketplace
```

Optional: check current plugin status/version.

```bash
/plugin
```

### 5) Remove plugin

```bash
/plugin uninstall commit@hemin-marketplace
```

### 6) Remove marketplace

```bash
/plugin marketplace remove hemin-marketplace
```

