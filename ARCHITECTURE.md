# Architecture

Six layers, each with a clear job and a clear boundary. A request travels from top to bottom and back. Anything you can build on this system, you can build by adding nodes to one of these layers without touching the others.

```
1. User           Slack DM and channels · n8n UI · Claude Code via MCP
        ↓
2. Orchestration  Agent runtime · Guardrails · RBAC · Confirmation gate
        ↓
3. Agent          Intent router · Template manager · Data-query · Digest formatter
        ↓
4. Tools          n8n REST · Read-only SQL · Atlassian · Slack · MCP server
        ↓
5. Memory         Personality file · Markdown facts · Curated knowledge · Postgres
        ↓
6. Foundation     Claude Sonnet 4.6 · Claude Haiku 4.5 · Cloud VM · Postgres · Redis · Nginx
```

## Layer 1: User

Three surfaces, one identity.

- **Slack DM and channels.** The default surface. The bot connects via Events API. Everyone on the team can ask in plain words.
- **n8n UI.** For anyone who wants to open the workflow that was just built. See the schedule, the SQL, the destination. Edit anything. Re-deploy.
- **Claude Code, Cursor, any MCP client.** For developers who want to call the same toolbox from their IDE. Same RBAC, same audit, no parallel store.

Identity is one user across all three surfaces. The Slack identity is mapped to the same Postgres row that the MCP JWT resolves to.

## Layer 2: Orchestration

Identity, safety, control. Nothing here is the LLM's decision.

- **Agent runtime.** A long-running process (open-source, MIT-licensed) that hosts the agent loop, manages plugins, reads the personality file at start, and exposes the MCP server. One container.
- **Guardrails.** Topic and safety rails (the open NeMo Guardrails framework). Blocks off-topic requests, refuses to act on prompt-injection content surfaced from external data.
- **RBAC.** Role × team × scope × `owns_teams`. Every grant is a row in Postgres. The LLM never passes `slack_id` or `user_id` as a parameter, identity always comes from the JWT or the verified Slack event signature.
- **Confirmation gate.** Every write waits for an explicit Approve in Slack. No tool can mutate without ack.

## Layer 3: Agent

Four roles. Each role has its own model selection and its own prompt.

- **Intent router.** Haiku-class model, cheap, fast. Classifies the incoming request into one of about a dozen intents (digest, alert, lookup, admin, etc).
- **Template manager.** Matches the intent against the workflow template catalog. Fills in parameters from the conversation. Asks for missing ones.
- **Data-query.** Builds scope-gated SQL against the read-only replica. Cannot touch tables outside the caller's scope.
- **Digest formatter.** Composes the reply or the digest body. Plain markdown, structured for Slack rendering.

## Layer 4: Tools

The systems the agent talks to. All of them are exposed through the MCP server as well, so a power user with a JWT can call any of them directly from their own Claude Code.

- **n8n REST.** Create, update, activate, deactivate workflows.
- **Read-only data sources.** Up to five sources in a typical installation, with about fourteen pre-built SQL templates. Each one parameterized, scope-checked, and audited.
- **Atlassian.** Jira search and create, Confluence search and publish. Every write gated by confirmation.
- **Slack API.** DM, channel, file upload, threads.
- **MCP server.** Bearer JWT, `/mcp` endpoint, identity from the JWT `sub` claim, audit row before every mutation.

## Layer 5: Memory

What the agent knows. No vector database, no embeddings, no RAG.

- **Personality file.** A markdown document the runtime reads at start. The bot's name, voice, how it confirms, how it apologizes. Edit the file, restart the container, the bot's behavior changes.
- **Workspace memory.** A folder of short markdown facts: company facts, decisions, glossary, who owns what. The agent reads them like a teammate reads a wiki.
- **Curated knowledge.** Wiki pages and team docs are distilled by hand into one-to-three-paragraph summaries with five-to-twelve key facts. Each file says where the source came from and when it was last refreshed.
- **Postgres.** Source of truth for RBAC, grants, MCP tokens, audit log, workflow registry.

The deliberate choice to skip vector DB and RAG comes from operational experience: a small curated knowledge base under your control beats a large embedded one you cannot inspect. When the bot says something wrong, you can grep the knowledge file, fix it, and the next answer is correct. That feedback loop is what makes the system maintainable.

## Layer 6: Foundation

Where it runs. Boring, on purpose.

- **Claude Sonnet 4.6** for reasoning, drafting, multi-step planning.
- **Claude Haiku 4.5** for routing and the cheap classify path. About 3× cheaper than Sonnet, plenty good for intent classification and template matching.
- **Cloud VM.** 4 vCPU, 16 GB RAM, VPN-gated. AWS, GCP, Azure, Hetzner, your choice.
- **Postgres** for state, **Redis** for queues, **Nginx** for TLS and routing.
- All in Docker Compose. Six containers in total. One disk, one backup target.

## Trust boundaries

The boundaries that matter:

- **LLM never passes identity.** The JWT or the verified Slack signature is the only source of truth. A prompt injection cannot change who you are.
- **Confirmation before mutation.** No write executes without an explicit Approve.
- **Audit row before the side effect.** The audit row is committed first. If the mutation fails, the audit row still says it was attempted.
- **Read-only replica for data queries.** The agent can never write to the source databases. Period.
- **Scope gating at every level.** Team Owners can only grant inside their team. The system has one Owner role for cross-team admin, and even that role cannot exfiltrate audit history without leaving a trail.

## What this is not

- It is **not** a managed agent platform. You run it. You own the data.
- It is **not** a chat assistant. The system is for building automations, not for everyday Q&A.
- It is **not** a vector-search product. The knowledge base is hand-curated markdown.
- It is **not** vendor lock-in. Every layer is replaceable: swap Claude for another model, swap n8n for another workflow engine, swap Postgres for anything compatible.

See [WORKFLOWS.md](./WORKFLOWS.md) for what the system can actually do today, and [INSTALL.md](./INSTALL.md) for what the installation includes.
