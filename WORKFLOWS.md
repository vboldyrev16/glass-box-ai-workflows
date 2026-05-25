# Workflows

Eight production-ready templates ship with the system. Each one is a small JSON file plus a markdown spec the agent reads. Together they cover about 80% of the "can you send me X every Y" requests that come up in a small or mid-size company.

The interesting part is not these eight specifically. It is the **template format**: any new ask can become a new template in one or two hours of work, and once it exists, every user in the company can invoke it by chatting with the bot.

## The eight that ship

### 1. Reactivate inactive B2C clients

**What it does.** On a schedule, find clients who have not interacted in the configured window (default 60 days) and either send them a personalized re-engagement message via the channel of choice (email, in-app, SMS) or deliver the list to a Slack DM for a human to follow up with.

**Triggered by.** Cron (default: Tuesdays at 10:00 local time) or a Slack command.

**What you configure during install.** The inactivity window. The communication channel. The message template. The Slack destination if you want manual follow-up.

**Replaces.** A weekly query that a sales lead used to ask an analyst for.

### 2. Expiring packages or subscriptions

**What it does.** On a schedule, check which client packages will expire in the next 30, 14, 7, 3, or 1 days and send a digest to the owners. Owners can be account managers, the client themselves, or both.

**Triggered by.** Cron (default: weekdays at 07:00 local time).

**What you configure during install.** The package table and the expiry column. The notification windows (which day buckets you want). The destination per bucket. The message template.

**Replaces.** "Can you send me the list of clients with expiring packages this week?" asked by a sales manager every Monday.

### 3. Daily sync (diff-sync data pipeline)

**What it does.** On a schedule, pull new and changed rows from a source system (CRM, billing, support) into your analytics warehouse. Diff-based, only the delta. Logs what was synced and what failed.

**Triggered by.** Cron (default: 22:00 UTC).

**What you configure during install.** Source and destination connection strings (kept in env vars, never in the workflow). The primary key and `updated_at` columns. Conflict resolution rules.

**Replaces.** A handwritten ETL script that someone wrote once and now no one wants to maintain.

### 4. Tier calculation

**What it does.** On a schedule, recompute a tier or score for every customer based on configurable rules (revenue, activity, lifetime value, custom signals). Writes the new tier back to the source system or to a dedicated table the rest of the team can query.

**Triggered by.** Cron (default: 23:00 UTC).

**What you configure during install.** The tier definitions. The signal columns and weights. The write target. Whether to keep tier history for auditing.

**Replaces.** A spreadsheet that someone updates manually once a quarter, that goes stale within a month.

### 5. Morning digest

**What it does.** At the start of the day, post a summary of what changed yesterday to a Slack channel. New deals, new leads, expiring packages, missed calls, anything you want. Customizable per recipient.

**Triggered by.** Cron (default: weekdays at 09:00 local time).

**What you configure during install.** The sections you want in the digest. The source for each section. The destination channel or DM. The recipient timezone.

**Replaces.** The first hour of the day spent in tabs.

### 6. Stale deal alerts

**What it does.** On a schedule, flag deals or opportunities with no activity for N days (default 7). Sends a digest to the owner and optionally to the sales lead.

**Triggered by.** Cron (default: weekdays at 09:30 local time).

**What you configure during install.** The deal table and activity column. The stale threshold per pipeline stage. The destination per role.

**Replaces.** Manual review of the pipeline by the sales lead.

### 7. Weekly executive report

**What it does.** Once a week, generate a structured summary of the previous week for leadership. Top-line metrics, deltas vs the previous week, top deals won and lost, top issues by support volume, top changes by team. Delivered as a Slack message and optionally a Confluence page.

**Triggered by.** Cron (default: Monday at 05:00 UTC).

**What you configure during install.** The sections. The metrics per section. The destination. The Confluence page template if you want one.

**Replaces.** A weekly status meeting that becomes a memo.

### 8. Custom cron

**What it does.** Turn an ad-hoc ask ("every Tuesday at 9, query X, format Y, send to Z") into a workflow without writing a new template. The bot asks for the trigger, the data, the format, and the destination, then assembles a workflow from the existing building blocks.

**Triggered by.** User chat. The resulting workflow has whatever cron the user specified.

**What you configure during install.** The set of available data sources and destinations. The bot exposes only what you have authorized.

**Replaces.** The need to write a new template for every one-off request.

## The template format

Every template is two files in the repo:

```
workflows/
  expiring-packages/
    workflow.json        ← the n8n graph
    spec.md              ← what it does, who it is for, parameters
```

The agent reads the spec, asks the user for any missing parameters, fills the JSON with those parameters, and creates the workflow through the n8n REST API. After that, the workflow runs on its own.

A new template is one JSON plus one short markdown file. No agent code change. No model retraining. That is what "extending the system" means.

## What you cannot have

These templates are deliberate. The system does not include:

- **Browser automation.** No headless Chrome, no scraping. If a workflow needs data that does not have an API, it is not a fit.
- **Image or video generation.** Out of scope.
- **Customer-facing chat.** The bot is for your team, not for your customers.
- **A vector database or RAG.** See [ARCHITECTURE.md](./ARCHITECTURE.md) for why.

## How adding more works

If you need a workflow that is not in this list, two options:

1. **In the free installation slot**, you get the eight above plus configuration. Adding a new template is not included.
2. **In a paid follow-up engagement**, we add new templates at a fixed price per template (rough estimate: 2 to 6 hours per template depending on complexity). Most teams find the eight above plus the custom-cron template cover their first six months. Real demand for a ninth or tenth template usually shows up after that.

See [PRICING.md](./PRICING.md) for the bundle scope and [CONTACT.md](./CONTACT.md) to start the conversation.
