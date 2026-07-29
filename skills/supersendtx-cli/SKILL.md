---
name: supersendtx-cli
description: >-
  Use when operating SuperSend TX from the terminal — supersendtx CLI, emails send,
  domains add/apply/verify, webhooks, suppressions, templates, API keys, automations.
  Trigger for shell scripts, CI, or npx supersendtx workflows.
license: MIT
metadata:
  author: supersendtx
  homepage: https://docs.supersendtx.com/ai/agent-skills
  source: https://github.com/Super-Send/supersendtx-skills
  documentation: https://docs.supersendtx.com/quickstart
---

# SuperSend TX CLI

Package: `supersendtx-cli` (bin: `supersendtx`). Wraps the `supersendtx` SDK.

```bash
export SUPERSENDTX_API_KEY=stx_…
# optional local:
# export SUPERSENDTX_API_URL=http://localhost:3003/api

npx -y --package=supersendtx-cli -- supersendtx <command>
# or: npm install -g supersendtx-cli && supersendtx <command>
```

For API semantics (sandbox, errors, field names), also load the `supersendtx` skill.

---

## Common flows

### Sandbox send

```bash
supersendtx emails send \
  --from noreply@mail.supersendtx.com \
  --to owner@example.com \
  --subject "Sandbox check" \
  --html "<p>It works.</p>"
```

`to` must be the account email when using the sandbox `from`.

### Production send

```bash
supersendtx emails send \
  --from you@verified.com \
  --to user@example.com \
  --subject "Hello" \
  --html "<p>Hi</p>"
```

### Domains

```bash
supersendtx domains add example.com
supersendtx domains apply example.com --provider cloudflare
supersendtx domains verify example.com
supersendtx domains list
```

### Emails ops

```bash
supersendtx emails list --limit 25
supersendtx emails get --id msg_…
supersendtx emails insights --window 7d
supersendtx emails cancel --id msg_…
supersendtx emails resend --id msg_…
supersendtx emails batch --file ./emails.json
```

### Webhooks

```bash
supersendtx webhooks list
supersendtx webhooks create --url https://yourapp.com/hooks
supersendtx webhooks get --id wh_…
supersendtx webhooks delete --id wh_…
```

### Suppressions

```bash
supersendtx suppressions list
supersendtx suppressions add --email user@example.com --reason bounced
supersendtx suppressions remove --email user@example.com
```

### Templates

```bash
supersendtx templates list
supersendtx templates get --id tpl_…
supersendtx templates publish --id tpl_…
```

### API keys

```bash
supersendtx keys list
supersendtx keys create --name ci
supersendtx keys delete --id key_…
```

---

## Rules

- Never print full `stx_` secrets into logs or commits
- Prefer CLI for ops; prefer SDK inside application code
- Output is JSON for most commands — pipe to `jq` when needed
