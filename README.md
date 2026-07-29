# SuperSend TX Agent Skills

Official [Agent Skills](https://agentskills.io) for [SuperSend TX](https://supersendtx.com) — Cursor, Claude Code, Codex, and compatible clients.

## Install

```bash
# All skills
npx skills add Super-Send/supersendtx-skills -y

# Or one at a time
npx skills add Super-Send/supersendtx-skills --skill supersendtx -y
npx skills add Super-Send/supersendtx-skills --skill supersendtx-mcp -y
npx skills add Super-Send/supersendtx-skills --skill supersendtx-cli -y
npx skills add Super-Send/supersendtx-skills --skill email-best-practices -y
```

Docs: https://docs.supersendtx.com/ai/agent-skills

## Skills

| Skill | Purpose |
|-------|---------|
| [`supersendtx`](./skills/supersendtx/SKILL.md) | Send API, auth, sandbox, domains, SDK patterns |
| [`supersendtx-mcp`](./skills/supersendtx-mcp/SKILL.md) | MCP install + tools |
| [`supersendtx-cli`](./skills/supersendtx-cli/SKILL.md) | Common CLI flows |
| [`email-best-practices`](./skills/email-best-practices/SKILL.md) | Deliverability, DNS, suppressions, key hygiene |

For React Email authoring, use React Email’s own tools; send with SuperSend TX.

## License

MIT
