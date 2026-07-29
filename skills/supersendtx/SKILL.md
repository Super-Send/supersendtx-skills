---
name: supersendtx
description: >-
  Use when sending or integrating SuperSend TX transactional email — POST /emails,
  Node SDK (supersendtx), auth with stx_ keys, sandbox self-tests, domain verify,
  webhooks, suppressions, templates, or OpenAPI. Trigger whenever the user mentions
  SuperSend TX, supersendtx, stx_ API keys, or mail.supersendtx.com sandbox sends.
license: MIT
metadata:
  author: supersendtx
  homepage: https://docs.supersendtx.com/ai/agent-skills
  source: https://github.com/Super-Send/supersendtx-skills
  documentation: https://docs.supersendtx.com/ai/agent-skill
---

# SuperSend TX

Transactional email API. Prefer the Node SDK or HTTP; do not invent competitor field names in generated code.

## Defaults

1. Base URL: `https://api.supersendtx.com`
2. Auth: `Authorization: Bearer stx_…` (env `SUPERSENDTX_API_KEY`)
3. Contract: `https://docs.supersendtx.com/openapi.yaml`
4. Verify customer domains before production sends
5. Sandbox self-tests only — never to arbitrary recipients

Machine-readable docs: [llms.txt](https://docs.supersendtx.com/llms.txt) · [llms-full.txt](https://docs.supersendtx.com/llms-full.txt)

Related skills: `supersendtx-mcp`, `supersendtx-cli`, `email-best-practices`.

For React Email projects, use React Email’s own tooling to author/preview, then send with SuperSend TX (`react` option or rendered HTML). Guide: https://docs.supersendtx.com/guides/react-email. Do not rebuild React Email.

---

## Quick send — Node.js

```ts
import { SuperSendTX } from 'supersendtx'

const client = new SuperSendTX(process.env.SUPERSENDTX_API_KEY!)

const result = await client.emails.send({
  from: 'you@verified.com',
  to: 'user@example.com',
  subject: 'Hello',
  html: '<p>It works.</p>',
})

console.log(result.id, result.status)
```

**Gotcha:** the SDK **throws** on API errors (`SuperSendTXError`). Use try/catch — it does not return `{ data, error }`.

Install: `npm install supersendtx`

---

## Quick send — HTTP

```bash
curl -X POST https://api.supersendtx.com/emails \
  -H "Authorization: Bearer $SUPERSENDTX_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"from":"you@verified.com","to":"user@example.com","subject":"Hello","html":"<p>It works.</p>"}'
```

Success: `{ "id": "msg_…", "status": "sent" }` (or scheduled/queued variants per API).

Canonical body fields: `from`, `to`, `subject`, `html`, `text`, `reply_to`, `cc`, `bcc`.

HTTP also accepts migration aliases (`htmlBody`, `textBody`, `replyTo`, `scheduledAt`, attachment aliases). Prefer canonical snake_case names in generated code.

---

## Sandbox self-test

Before the customer verifies DNS:

| Field | Value |
|-------|--------|
| `from` | `noreply@mail.supersendtx.com` |
| `to` | Exactly the SuperSend TX account email |

```json
{
  "from": "noreply@mail.supersendtx.com",
  "to": "owner@example.com",
  "subject": "Sandbox check",
  "html": "<p>Sandbox works.</p>"
}
```

Do not send sandbox mail to arbitrary recipients — the API rejects that (`403`).

---

## Domain verification

1. `POST /domains` with `{ "name": "yourdomain.com" }`
2. `POST /domains/{id}` with `{ "action": "apply" }` (Cloudflare token in dashboard, or GoDaddy one-time credentials)
3. `POST /domains/{id}` with `{ "action": "verify" }` after DNS propagates
4. Switch production `from` to an address on the verified domain

Explain when the customer still needs DNS or registrar access.

---

## Templates and React

- Send with a published template: `template` (id or alias) + variables — mutually exclusive with `html` / `text` / `react`
- React Email: pass `react: <Component />` after `npm install @react-email/render react`; SDK compiles to HTML before `POST /emails`. Guide: https://docs.supersendtx.com/guides/react-email
- Do not fork or reimplement React Email — author/preview with React Email’s tooling, send with SuperSend TX

---

## Webhooks and events

- Register via dashboard or `POST /webhooks`
- Events include delivered, bounced, complained, and related types
- Poll `GET /events` when webhooks are not wired yet
- Test endpoint: `POST /emails/test` (dashboard/CLI/MCP when available)

---

## Errors

| Status | Meaning |
|--------|---------|
| `401` | Missing or invalid API key |
| `403` | Unverified domain, sandbox recipient mismatch, or account restriction |
| `400` | Validation (missing `from`/`to`/`subject`, malformed email) |

Body shape: `{ "error": { "message": "...", "details": ... } }`

---

## Useful follow-ups

- `GET /emails`, `GET /emails/{id}` — send status
- `GET /deliverability` — best-effort metrics (not guaranteed inbox placement)
- `GET /suppressions` — check before retrying bounced addresses

---

## Anti-patterns

- Do not expose `stx_` keys in browser/client bundles (no CORS by design)
- Do not send production mail from the sandbox `from` address
- Do not assume account-scoped SMTP is available
- Do not describe deliverability metrics as guaranteed inbox placement
- Do not brand SuperSend TX as a clone of any competitor in customer-facing copy

---

## MCP / CLI

- MCP: install skill `supersendtx-mcp` or see https://docs.supersendtx.com/ai/mcp
- CLI: skill `supersendtx-cli` — `npx -y --package=supersendtx-cli -- supersendtx …`
