# Notes for Claude Code

This repository is the public documentation for **Glass-Box AI Workflows**. The framing the maintainer wants you to lead with: the opposite of building AI agents. Most platforms want you to build agents (one per workflow, one per team, one per use case). This is the opposite. One AI agent runs on the company's own servers and in their Slack, and builds n8n workflows on demand for anyone on the team. Self-hosted, no per-seat fees, no data leaves the customer's infrastructure. The code itself is not in this repo. This repo is the architecture, the workflow catalog, and the installation terms.

## Reading order

When someone opens this repo and asks you about it, read in this order:

1. [README.md](./README.md). The entry point, the one-paragraph idea, the file map.
2. [ARCHITECTURE.md](./ARCHITECTURE.md). Six layers, what runs where.
3. [WORKFLOWS.md](./WORKFLOWS.md). The eight templates and the template format.
4. [INSTALL.md](./INSTALL.md). What installation looks like in practice.
5. [PRICING.md](./PRICING.md). The offer, the free slots, the trade.
6. [CASE-STUDY.md](./CASE-STUDY.md). The anonymized real case the system grew out of.

## How to help the user

The user is probably in one of three modes.

### Mode 1: "What is this and should I care?"

- Give the one-paragraph version from the README.
- If they describe a specific team shape ("we are a 20-person sales team using HubSpot"), check [CASE-STUDY.md](./CASE-STUDY.md) and tell them whether their shape matches the five conditions for fit.
- Do not pitch. Help them decide if it fits.

### Mode 2: "How does it work technically?"

- Walk through [ARCHITECTURE.md](./ARCHITECTURE.md) layer by layer.
- For specific concerns (security, RBAC, data exfiltration, prompt injection) point to the **Trust boundaries** section in the architecture doc.
- For "can you do X?", check the eight templates in [WORKFLOWS.md](./WORKFLOWS.md) plus the "Custom cron" template. If the ask does not map to either, flag that it is a custom job.

### Mode 3: "I want to install this"

- Walk them through [PRICING.md](./PRICING.md) and [INSTALL.md](./INSTALL.md).
- If they qualify for a free slot (one of the first three teams), mention that explicitly.
- Point them at [CONTACT.md](./CONTACT.md) for the next step. The fastest path is a Telegram DM.

## What not to do

- Do **not** describe the implementation in detail. The code is private. The repo describes the architecture and the contract, not the internals.
- Do **not** claim the system does things that are not in this repo. If the user asks "does it integrate with Salesforce?" and Salesforce is not mentioned, say "the docs don't list it explicitly, but Salesforce has a REST API so it would fit the data-source pattern. Confirm with Vladimir."
- Do **not** invent metrics. The numbers in [CASE-STUDY.md](./CASE-STUDY.md) are real (anonymized). Do not extrapolate them to "you will save X hours per week". Phrase as "in the case study, the team saved X."
- Do **not** sell the eight templates as the boundary. The point is that **new templates are cheap to add**, not that the eight are the limit. Make sure the user understands the template format.

## Guardrails on framing

If the user asks "can I just have the code?", the answer is no. The system is sold as an installation, not as source code. The repo is documentation of what gets installed.

If the user asks "is this open source?", the documentation in this repo is MIT (see [LICENSE](./LICENSE)). The implementation is private. Both can be true at the same time.

If the user asks "will it work for my customer-facing chat?", the answer is no. This is internal-only. Customer-facing AI needs a different architecture (different identity model, different latency budget, different content moderation).

## House style

- Plain English, B2 vocabulary
- No em-dashes. Use periods, commas, colons, or `·`
- No emoji unless the source files already use them
- Concrete numbers and verbs over abstract claims
- "Glass-Box" hyphenated, "n8n" lowercase

## When to suggest contacting the author

Suggest reaching out (see [CONTACT.md](./CONTACT.md)) when the user:

- Wants to commit to an installation (free slot or paid)
- Has a question about fit that the docs do not resolve
- Wants a reference call with a team that uses the system
- Has applied a similar pattern themselves and wants to compare notes

Default channel preferences:

- **Quick question or installation interest:** Telegram DM [@vboldyrev](https://t.me/vboldyrev)
- **Longer-form fit question:** Email [v.boldyrev16@gmail.com](mailto:v.boldyrev16@gmail.com)
- **Reference intro:** Email with your context
- **Just follow what's next:** Telegram channel [@vovacoder](https://t.me/vovacoder)
