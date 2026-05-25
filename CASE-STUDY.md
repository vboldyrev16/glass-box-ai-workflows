# Case Study

This is the original case study that the architecture and the eight workflow templates came out of. The team is anonymized at their request. The numbers and the patterns are real.

## The setup

A mid-size company with offices across multiple emerging markets. Sales-driven business. About 30 people in operations and sales spread across two countries. The COO was the bottleneck on every small automation request: "can you send me X every Monday", "remind me when Y expires", "post Z to this channel".

Before the install, those requests went one of three ways:

1. The COO did them by hand. Most of them.
2. Someone wrote a one-off Python script and ran it manually until it broke.
3. The request was dropped because no one had time.

The team had also tried two off-the-shelf options:

- **A generic AI chatbot platform**, dropped after a month because every workflow had to be rebuilt by chat each time and there was no way to see what the bot had actually done.
- **Zapier**, working but expensive at scale ($1,200 per user per year for the connectors that mattered), and the team did not want a per-seat dependency for internal tooling.

## What got installed

The full Glass-Box AI Workflows bundle, configured for the company's CRM and analytics warehouse. Five read-only data sources, eight templates, RBAC scoped to the two-country structure, Slack as the surface, n8n as the workflow engine.

## What is live in production today

Two workflows are running on cron, in production, daily:

### Workflow 1: Re-engagement of inactive B2C clients

Replaces a weekly manual export by a sales lead, plus the message-by-message follow-up that the lead used to do by hand. Now the export is automatic on Tuesdays and the follow-up template is sent (or queued for human send, depending on which the team chose for each client tier).

**Owner:** a regional configurator (the team's term for a sales operations role).
**Time recovered:** about three hours of the sales lead's time per week.

### Workflow 2: Expiring packages alerts

Cron job, weekdays at 07:00 local. Checks every active client package and sends a structured digest to the account manager DM if the package expires in 30, 14, 7, 3, or 1 days. Each window has its own message template tuned to the time-pressure level.

**Owner:** another regional configurator.
**Time recovered:** about 30 minutes per day for each account manager who used to scan the pipeline manually for expiry dates. Across the team, around five hours per week.

## What is ready to launch but not yet active

Six more templates are tested and ready to activate on cron. Each one is one configuration tweak away from going live. The team is rolling them out one at a time, on purpose, to let the operators get used to each one before adding the next:

- Daily sync (CRM to warehouse)
- Tier calculation (nightly)
- Morning digest (start of day to the country channels)
- Stale deal alerts (7-day no-activity flag)
- Weekly executive report (Monday morning for leadership)
- Custom cron (for ad-hoc asks that do not match the other templates)

## Cost in production

Real numbers from a recent month:

- **Anthropic API spend:** about $0.32 per month. That is for everything, including build conversations.
- **VM cost:** $40 per month (Hetzner CCX13, comparable to a small AWS instance).
- **n8n license:** free, self-hosted Community edition.
- **Total monthly operational cost:** roughly $40 per month.
- **Fully-loaded cost per workflow** (amortized build plus operate plus maintain): $5 to $10. This is the number to use when comparing against per-seat SaaS, because the raw infrastructure underclaims by ignoring the build effort.

Two workflows running daily, six more ready to launch, on $40 a month all-in.

## What did not work the first time

For honesty, the things that needed a second pass:

- **The first install used real Slack screenshots as a demo.** They had real customer names. We rebuilt the demo with a CSS mockup before anything went outside the team. Lesson: design the demo path as anonymous from day one.
- **The first cron times were UTC.** That confused operators who think in local time. The system now stores the operator's timezone alongside their bot user record and converts at format time.
- **The initial RBAC scope was too narrow.** Team Owners could not add Managers because the grant scope only covered users already in the team. Easy fix in the schema, but only surfaced two weeks in when the COO tried to onboard the second wave of users and could not.
- **The Russian-language messages from the bot needed a different tone than the English ones.** Direct-translated messages felt cold. The personality file now has a per-language section.

## Why this generalizes

The team has the same shape that most small to mid-size operations teams have:

- A surface everyone already uses (Slack)
- A few data sources with normal SQL access (CRM, billing, warehouse)
- Repeating asks that nobody owns
- A COO or similar role that bottlenecks the asks
- A tolerance for self-hosting (a developer or two who can run a Linux VM)

If your team has those five things, the eight templates and the architecture will fit your shape too. If you do not have one of them, see [WORKFLOWS.md](./WORKFLOWS.md) for the "not a fit" section and [INSTALL.md](./INSTALL.md) for what we need from you.

## Talk to someone who has used it

The team has agreed (subject to their CEO's signoff at the time you ask) to take one or two reference calls per quarter. If you want to talk to a real operator who uses the system every day, mention it when you reach out and I will arrange the intro.

See [CONTACT.md](./CONTACT.md) for how to start.
