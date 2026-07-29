---
name: email-best-practices
description: >-
  Use when writing or reviewing transactional email for deliverability and safety —
  SPF/DKIM/DMARC, verified domains, suppressions, bounce handling, API key hygiene,
  sandbox vs production, unsubscribe for bulk-like mail. Pair with supersendtx when
  integrating SuperSend TX.
license: MIT
metadata:
  author: supersendtx
  homepage: https://docs.supersendtx.com/ai/agent-skills
  source: https://github.com/Super-Send/supersendtx-skills
  documentation: https://docs.supersendtx.com/deliverability
---

# Email best practices (transactional)

Guidance for agents shipping password resets, receipts, invites, and other transactional mail on SuperSend TX.

---

## Auth and secrets

- Store API keys in server env only (`SUPERSENDTX_API_KEY`)
- Never ship `stx_` keys to browsers, mobile apps, or public repos
- Rotate keys if exposed; delete unused dashboard keys

---

## Domains and DNS

- Production `from` must use a **verified** domain in the sending account
- Ensure SPF, DKIM (and ideally DMARC) are correct for that domain — SuperSend TX domain verify checks the records it publishes
- Do not send high volume from a brand-new domain without gradual ramp-up
- Prefer a dedicated subdomain for mail (e.g. `mail.example.com` or `notifications.example.com`) when the root domain already sends elsewhere

---

## Sandbox vs production

| Mode | `from` | `to` |
|------|--------|------|
| Sandbox self-test | `noreply@mail.supersendtx.com` | Account email only |
| Production | Address on verified customer domain | Real recipients |

Never use the sandbox sender for customer-facing production mail.

---

## Suppressions and bounces

- After hard bounces / complaints, check suppressions before retrying
- Do not hammer the same address after bounce
- Use `GET /suppressions` (or CLI/MCP) before bulk retries
- Treat spam complaints as permanent opt-outs for that address

---

## Content and compliance

- Transactional only in this product path — no purchased lists or cold outbound here
- Include clear sender identity and a way to get help
- If mail is subscription-like, include unsubscribe and honor it
- Avoid misleading subjects; match body to subject
- Prefer plain text + HTML multipart when possible; keep HTML simple for clients

---

## Testing

- Prefer sandbox self-test or sends to your own verified addresses
- Do not test with invented `@gmail.com` / `@outlook.com` addresses — they bounce and hurt reputation
- Confirm webhook handlers verify signatures before acting on events

---

## Metrics honesty

- Dashboard / `GET /deliverability` numbers are **best-effort** from stored events
- Do not claim guaranteed inbox placement or full mailbox-provider telemetry

---

## React Email

Author and preview with React Email’s tools; send via SuperSend TX SDK (`react` option or pre-rendered HTML). Keep keys server-side.
