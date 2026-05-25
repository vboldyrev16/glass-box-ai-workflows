# Installation

What gets installed is one AI agent and its infrastructure, on your servers, with the eight workflow templates configured for your data. The system is **not open source**. The code is private and stays private. Installation is done by hand, on your infrastructure, by the maintainer. This page describes what that looks like in practice.

## What ends up running on your side

After installation, your team owns and runs:

- **One Linux VM**, 4 vCPU, 16 GB RAM. Any cloud, or on-prem. AWS, GCP, Azure, Hetzner, DigitalOcean. The VM does not need a public IP, only outbound HTTPS.
- **Docker Compose** with six containers: the agent runtime, n8n, Postgres, Redis, Nginx, and a Prometheus exporter for metrics.
- **One Slack app** registered to your workspace, with the bot user added to the channels where it should be available.
- **One Anthropic API key** owned by you, billed to you. Tokens are spent during the build conversation (around $5 to $10 per workflow built), not during scheduled runs. A team actively building new workflows runs around $10 to $40 per month, then drops to near zero once the catalog stabilizes.
- **One n8n workspace** with the eight templates installed and configured for your data.

The maintainer has no ongoing access to your installation after the 30-day support window ends, unless you explicitly extend it.

## What we need from you

To install, we need from your side:

1. **A Linux VM** ready to receive Docker Compose. Ubuntu 22.04 or newer recommended. Root or sudo, ideally inside a VPN or with a tight inbound firewall.
2. **A Slack workspace** where you can create a new Slack app. Admin permission required for the install, not for daily use.
3. **An Anthropic API key** with billing enabled.
4. **Read-only credentials** for the data sources you want the bot to query. CRM, billing, analytics warehouse, whatever applies. Read-only is non-negotiable, the agent should never have write access to source systems.
5. **A point of contact** who can answer questions during the configuration phase. Usually a COO or Head of Operations who knows what kinds of digests the team wants.

We do **not** need access to your production code, your customer data, or your secrets. The install runs from a tarball on your VM, with all credentials supplied by you via environment variables.

## How the install runs

A typical installation is one full day of synchronous work plus a few short follow-ups. The shape:

### Day 1 morning: discovery (1 hour)

- Quick interview with the point of contact. What do people ask for repeatedly today? Which of the eight templates fit? Which need parameter tuning?
- Walk-through of the data sources. What can be queried, what schemas, what should the agent never see?
- Decide the Slack channel topology. Which channels does the bot join, which DMs are it default for.

### Day 1 afternoon: install (2 to 3 hours)

- VM provisioning, if not already done.
- Docker Compose up.
- Slack app registration, OAuth scopes, event subscription.
- Anthropic API key plug-in.
- Data source credentials in env vars.
- Smoke test: a single test workflow built end-to-end.

### Day 2 morning: configuration (2 hours)

- Configure each of the eight templates for the customer's data. Column names, schedule, destination, message format.
- Set up RBAC. One Owner (usually the COO or a senior engineer), Team Owners per team, Managers under each Team Owner.
- Test each workflow with a small payload, confirm the destination receives a usable message.

### Day 2 afternoon: training (2 hours)

- Live training with two operators (typically COO plus one or two team leads). How to ask the bot for things. How to read the Slack confirmation. How to open a workflow in n8n. How to disable a workflow that is misbehaving. How to add a new user to a team.
- Recorded session that the team can re-watch.
- Written runbook tailored to your installation: what to do if X, who to call if Y.

### Days 3 to 30: support

- Slack channel with the maintainer for questions.
- Up to three follow-up sessions of 30 minutes each, scheduled as you need them.
- Bug fixes and small adjustments to the eight templates included.
- Adding new templates is **not** included (see [PRICING.md](./PRICING.md) for follow-up engagements).

## After day 30

The 30-day window ends. The system continues to run on your VM. You own everything. The agent has no callback to the maintainer, no telemetry sent out, no remote feature flags. If a model deprecates, you swap the model id in one config file. If you want to fork the prompts, you can.

If you want longer-term help, that is a separate engagement. Reach out.

## What can go wrong, honestly

A few things that have come up in real installations:

- **Slack rate limits** if you blast the bot with test messages. The system handles them gracefully but it slows down testing. Plan for it.
- **Data source schemas** that have grown organically. Sometimes a column name does not match the spec template. The bot asks for it, you tell it, and the configuration is updated. But it adds time on day 1.
- **VPN routing.** If your CRM is behind a corporate VPN, the VM needs to be on the same network or have a tunnel. This is the most common cause of an install dragging past day 2.
- **Anthropic API key quota.** Default quotas on a new key are sometimes too low for testing. Request a bump in advance.

None of these are blockers, all of them shift the install by a few hours if they happen.

## What is excluded

To be explicit about what the bundle does not include:

- **Custom workflows beyond the eight.** Adding new templates is a paid follow-up.
- **Integration with non-standard data sources.** If your data lives in a proprietary system without an API, that is a custom job.
- **Hosting.** The VM is yours, the maintainer does not host installations.
- **24/7 support.** The 30-day window is best-effort during business hours.
- **A second installation in the same company.** One install per offer. Need a second region or a separate workspace? Quote on request.

## Ready to start?

See [PRICING.md](./PRICING.md) for the terms and [CONTACT.md](./CONTACT.md) for how to reach the maintainer. The fastest path is a Telegram DM with one paragraph about your team and your top-three automation asks.
