# Glass-Box AI Workflows

A Slack-native AI teammate that builds your team's automations. Every workflow it deploys is transparent, editable, and yours. No black-box agent. No vector database. No per-seat fees.

> If you came here from a talk or a tweet, this is the source. Open the presentation, read this README, and reach out if you want it installed in your team.

**Watch the 14-slide talk:** https://glass-box-ai-amber.vercel.app

## The idea in one paragraph

Most AI agents do the work in front of you and then vanish. You cannot inspect what they did, replay it, edit it, or hand it to a teammate. Glass-Box AI Workflows takes the opposite shape. The agent's job is not to do the work, the agent's job is to build the thing that does the work. After the build, what stays in production is a normal n8n workflow you can open, read, edit, and re-deploy. The agent is no longer in the loop. The workflow is yours.

That single design choice changes everything. It makes the automation reviewable like any other piece of infrastructure. It makes the cost predictable, around $5 to $10 in tokens per workflow built, and $0 to run it forever after. It makes new capabilities a one-line PR in n8n plus a short markdown spec, not a model retraining cycle.

## What's inside

| File | What it is |
| --- | --- |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | The six-layer architecture. User, Orchestration, Agent, Tools, Memory, Foundation. What runs where and why. |
| [WORKFLOWS.md](./WORKFLOWS.md) | Eight production-ready workflows that ship with the system, plus the template format for adding your own. |
| [INSTALL.md](./INSTALL.md) | What the installation includes, what runs on your infrastructure, what we need from you, and how onboarding works. |
| [PRICING.md](./PRICING.md) | The price, what is included, the three free installations, and the limits on the offer. |
| [CASE-STUDY.md](./CASE-STUDY.md) | Anonymized results from production. The two live workflows, what they replace, hours saved per week, cost. |
| [CONTACT.md](./CONTACT.md) | How to reach the maintainer. LinkedIn, email, Telegram DM and channel. |
| [CLAUDE.md](./CLAUDE.md) | Reading order and house rules for Claude Code or other AI assistants reading this repo. |
| [llms.txt](./llms.txt) | Index for AI crawlers. |
| [presentation/](./presentation/) | Self-hostable copy of the 14-slide talk (single-file HTML). |

## Why this shape

| Concern | Most AI agents | Glass-Box AI Workflows |
| --- | --- | --- |
| What gets deployed | An opaque agent that runs on every request | A normal n8n workflow, schedule plus SQL plus formatter |
| Inspectability | Logs at best, often not even that | Open the workflow in n8n, see every step |
| Editability | Re-prompt the agent and hope | Edit the workflow directly, same as any infrastructure |
| Cost per run | Tokens every single time | Zero tokens after the build, only the cron tick |
| Multi-user safety | One chat, no audit | RBAC, scoped tools, audit row before every write |
| Add a new capability | Retrain or rewire the agent | One JSON file plus a short spec, no agent code change |

## What ships with the system

The repo includes the catalog and the architecture. The actual code is not open source. The eight workflows below are production-ready out of the box, with parameters tuned during installation.

1. **Reactivate inactive B2C clients** — re-engagement digests on a schedule.
2. **Expiring packages** — alerts when packages reach 30/14/7/3/1 days before expiry.
3. **Daily sync** — diff-sync data pipeline between source systems.
4. **Tier calculation** — recompute client tiers nightly on your business rules.
5. **Morning digest** — what changed yesterday, delivered to a Slack channel at the start of the day.
6. **Stale deal alerts** — flag deals with no activity for 7 days.
7. **Weekly executive report** — Monday morning summary for leadership.
8. **Custom cron** — a generic template that turns "every Tuesday at 9, do X, send to Y" into a workflow.

Plus the ability to add new ones with a single JSON file and a short markdown spec. See [WORKFLOWS.md](./WORKFLOWS.md) for the catalog and the template format.

## Installation, training, and pricing

The system is **not open source**. The code stays private. Each installation is done by hand, on your infrastructure, with training included.

**The offer right now**

- **Three free installations** for the first teams that commit. After that, a fixed-price package.
- **One package: setup plus training**, $2,500. Includes installation on your Slack and your cloud VM, configuration of the eight workflows on your data, training for two operators, and 30 days of post-install support.

See [PRICING.md](./PRICING.md) for the full scope, what is included, what is excluded, and how to claim a free slot.

## Who this is for

**Built for**: small and mid-size companies where the same kind of internal automation request keeps coming up. Sales asking for client digests, ops asking for reminders, leadership asking for weekly reports, product asking for Jira and Confluence shortcuts inside Slack. Teams that already use Slack as a working surface and have a developer or two who can host a workflow tool internally.

- **COOs and Heads of Operations** who keep being the bottleneck on small automation asks
- **Sales leads** whose team spends 30 minutes a day pulling lists out of the CRM
- **CTOs and engineering leads** evaluating AI agents and not wanting to bet on a black-box vendor lock-in
- **Founders of 10 to 50-person companies** who want internal automation without hiring a dedicated team

**Not a fit for**: companies that need a customer-facing AI product, enterprises with a mandatory vendor procurement process, or teams that do not use Slack.

## Contact

- LinkedIn: https://www.linkedin.com/in/vboldyrev16/
- Email: v.boldyrev16@gmail.com
- Telegram DM: https://t.me/vboldyrev
- Telegram channel: https://t.me/vovacoder

Maintained by **Vladimir Boldyrev**. See [CONTACT.md](./CONTACT.md) for what I am happy to help with.

## License

The text and diagrams in this repo are released under MIT (see [LICENSE](./LICENSE)). The implementation code that powers the system is private and not part of this repo.
