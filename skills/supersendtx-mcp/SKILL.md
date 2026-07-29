---
name: supersendtx-mcp
description: >-
  Use when installing or configuring the SuperSend TX MCP server for Cursor,
  Claude Code, or other MCP clients — supersendtx-mcp, stdio, local --http,
  hosted https://mcp.supersendtx.com/mcp, SUPERSENDTX_API_KEY / Bearer stx_,
  send_email, domain apply/verify, deliverability tools.
license: MIT
metadata:
  author: supersendtx
  homepage: https://docs.supersendtx.com/ai/agent-skills
  source: https://github.com/Super-Send/supersendtx-skills
  documentation: https://docs.supersendtx.com/ai/mcp
---

# SuperSend TX MCP

Package: `supersendtx-mcp` (npm). Uses the `supersendtx` SDK — no duplicate HTTP.

Env:

- `SUPERSENDTX_API_KEY` (required, must start with `stx_`)
- `SUPERSENDTX_API_URL` (optional; local app e.g. `http://localhost:3003/api`)

For product API patterns (sandbox, domains, errors), also load the `supersendtx` skill.

---

## Install — Cursor

`.cursor/mcp.json` or `~/.cursor/mcp.json` (stdio):

```json
{
  "mcpServers": {
    "supersendtx": {
      "command": "npx",
      "args": ["-y", "supersendtx-mcp"],
      "env": {
        "SUPERSENDTX_API_KEY": "stx_your_key_here"
      }
    }
  }
}
```

Restart Cursor after editing. The dashboard **Install in Cursor** button can deeplink a pre-filled config.

### Local HTTP mode

```bash
npx -y supersendtx-mcp --http --port 3000
```

Authenticate each request with `Authorization: Bearer stx_…`. Endpoint: `http://127.0.0.1:3000/mcp`.

### Hosted remote MCP

```json
{
  "mcpServers": {
    "supersendtx": {
      "url": "https://mcp.supersendtx.com/mcp",
      "headers": {
        "Authorization": "Bearer stx_your_key_here"
      }
    }
  }
}
```

No local process. Dashboard **Copy hosted MCP JSON** pre-fills the key. OAuth for remote clients is a follow-up; Bearer works today.

---

## Install — Claude Code

```bash
claude mcp add --transport stdio supersendtx -- npx -y supersendtx-mcp
```

Export `SUPERSENDTX_API_KEY=stx_…` in the environment where Claude Code runs. For HTTP, use local `--http` or hosted `https://mcp.supersendtx.com/mcp` with Bearer headers.

---

## Tools (current)

| Tool | Purpose |
|------|---------|
| `send_email` | `POST /emails` — `from`, `to`, `subject`, optional `html`/`text`/`reply_to` |
| `list_emails` / `get_email` | List or fetch sends |
| `list_domains` / `create_domain` | Domain inventory |
| `apply_domain_dns` / `verify_domain` | Apply DNS + verify |
| `list_webhooks` / `create_webhook` / `delete_webhook` | Webhook endpoints |
| `list_suppressions` / `add_suppression` / `remove_suppression` | Suppression list |
| `list_templates` / `get_template` | Templates by id or alias |
| `send_test_webhook_event` | `POST /emails/test` |
| `get_deliverability` | `GET /deliverability` (`7d` or `30d`) |

Sandbox rule: if `from` is `noreply@mail.supersendtx.com`, `to` must be the account email.

Present deliverability numbers as best-effort, not guaranteed inbox placement.

---

## When to prefer MCP vs SDK

| Prefer MCP | Prefer SDK / HTTP in app code |
|------------|-------------------------------|
| Interactive agent setup, domain DNS, one-off sends | Production app send paths |
| Dashboard/agent onboarding | CI, server routes, long-lived services |

Never put the API key in client-side code even when using MCP for local development.
