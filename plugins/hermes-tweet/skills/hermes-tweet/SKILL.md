---
name: hermes-tweet
description: Use when installing or operating Hermes Tweet, the Hermes Agent X/Twitter plugin for social discovery, tweet reads, and approval-gated actions through Xquik.
metadata:
  short-description: Add Hermes Agent X/Twitter workflows
---

# Hermes Tweet

Hermes Tweet adds X/Twitter workflows to Hermes Agent. Use it when an agent needs social discovery, public tweet reads, or explicitly approved write actions.

## Install

```bash
uvx hermes-agent plugin install Xquik-dev/hermes-tweet
```

## Configure

Set `XQUIK_API_KEY` before using read tools.

Set `HERMES_TWEET_ENABLE_ACTIONS=true` only when write actions should be available. Leave it unset for read-only operation.

## Tool Groups

- `tweet_explore`: list bundled route metadata without network access.
- `tweet_read`: read public X/Twitter data when `XQUIK_API_KEY` is configured.
- `tweet_action`: run write actions only when both `XQUIK_API_KEY` and `HERMES_TWEET_ENABLE_ACTIONS=true` are configured.

## Safety

- Prefer `tweet_explore` before choosing a route.
- Treat write actions as explicit user-approved actions.
- Do not ask the user for action enablement when a read route can answer the task.
- Keep credentials in the local environment, never in prompts, logs, or generated files.

## Source

See `https://github.com/Xquik-dev/hermes-tweet` for the full Hermes plugin package, route catalog, tests, and release metadata.