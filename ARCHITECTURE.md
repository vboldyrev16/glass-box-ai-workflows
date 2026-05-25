# Architecture

Six layers, each with a clear job and a clear boundary. A request travels from top to bottom and back. New capabilities are added by extending one layer at a time, without touching the others.

```
1. User           Slack DM and channels · n8n UI · MCP clients (Claude Code, IDE)
        ↓
2. Orchestration  Agent runtime · Guardrails · RBAC · Confirmation gate
        ↓
3. Agent          Intent classification · Template fitting · Safe data access · Response formatting
        ↓
4. Tools          Workflow engine · Read-only data · Ticketing and docs · Slack · MCP server
        ↓
5. Memory         Personality and policy · Curated knowledge · Operational state
        ↓
6. Foundation     LLM models · Cloud VM · Storage · TLS / routing
```

## Layer 1: User

Three surfaces, one identity.

- **Slack DM and channels.** The default surface. Everyone on the team can ask in plain words.
- **n8n UI.** For anyone who wants to open the workflow that was just built. See the schedule, the data step, the destination. Edit, re-deploy, done.
- **MCP clients.** Developers can call the same toolbox from Claude Code, Cursor, or any MCP-compatible client. Same identity, same audit, no parallel store.

Identity is one user across all three surfaces. A user that Slack knows resolves to the same record that authorizes an MCP call.

## Layer 2: Orchestration

Identity, safety, control. **Nothing here is the LLM's decision.**

- **Agent runtime.** A long-running process that hosts the agent loop and exposes the tool surface. One container.
- **Guardrails.** Topic and safety rails. Blocks off-topic requests. Refuses to act on prompt-injection content surfaced from external data.
- **RBAC.** A role and team model with explicit scopes. Identity always comes from the signed request, never from a tool parameter the LLM constructed.
- **Confirmation gate.** Every write waits for an explicit Approve in Slack. No tool can mutate without an ack.

## Layer 3: Agent

The agent is intentionally small. It does four things, each with its own model selection and its own prompt: classify the intent, pick and fit a workflow template, build a scope-gated data query if needed, and format the response for the surface. Cheap classification on the fast lane, the more capable model only when the request needs reasoning.

## Layer 4: Tools

The systems the agent talks to. All of them are exposed through the MCP server as well, so a developer with the right identity can call any of them directly.

- **Workflow engine.** Create, update, activate, deactivate workflows in n8n through its REST API.
- **Read-only data.** Your data sources, with pre-built parameterized queries for the most common questions. Every query is scope-checked and audited.
- **Ticketing and docs.** Atlassian Jira search and create, Confluence search and publish. Every write gated by confirmation.
- **Slack API.** DM, channel, file upload, threads.
- **MCP server.** The same tool surface, exposed over an authenticated endpoint for power users.

## Layer 5: Memory

What the agent knows. **No vector database, no embeddings, no RAG.**

- **Personality and policy.** Short markdown documents the runtime reads at start. The bot's name, voice, how it confirms, what it refuses. Edit the file, restart, the behavior changes.
- **Curated knowledge.** Wiki pages and team docs are distilled by hand into short summaries. Each file says where the source came from and when it was last refreshed.
- **Operational state.** RBAC, grants, tokens, audit log, workflow registry, all in a relational database.

The deliberate choice to skip vector DB and RAG comes from operational experience: a small curated knowledge base under your control beats a large embedded one you cannot inspect. When the bot says something wrong, you grep the knowledge file, fix it, the next answer is correct. That feedback loop is what makes the system maintainable.

## Layer 6: Foundation

Where it runs. Boring, on purpose.

- **LLM.** Claude Sonnet for reasoning and drafting, Claude Haiku for the cheap classify path. The model selection per task is a one-line config change.
- **Cloud VM.** 4 vCPU, 16 GB RAM, VPN-gated. Any provider you prefer.
- **Storage.** Relational database for state, an in-memory store for queues.
- **Edge.** TLS termination and routing in front.
- All of it in Docker Compose. One disk, one backup target.

## Trust boundaries

The boundaries that matter:

- **LLM never passes identity.** The signed request is the only source of truth. A prompt injection cannot change who you are.
- **Confirmation before mutation.** No write executes without an explicit Approve.
- **Audit before the side effect.** The audit row is committed first. If the mutation fails, the audit row still says it was attempted.
- **Read-only on data sources.** The agent can never write to the source databases. Period.
- **Scope gating at every level.** Team admins can only grant inside their team. The cross-team admin role cannot exfiltrate audit history without leaving a trail.

## What this is not

- It is **not** a managed agent platform. You run it. You own the data.
- It is **not** a chat assistant. The system is for building automations, not for everyday Q&A.
- It is **not** a vector-search product. The knowledge base is hand-curated markdown.
- It is **not** vendor lock-in. Each layer is replaceable: swap the LLM provider, swap the workflow engine, swap the database, the contract between layers stays the same.

The specific prompts, schemas, and the tested workflow templates live in the implementation. See [INSTALL.md](./INSTALL.md) for what shows up after installation, and [WORKFLOWS.md](./WORKFLOWS.md) for the workflow catalog.
