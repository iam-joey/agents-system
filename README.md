# agent-system

Multi-agent Claude Code system for building full-stack apps. Clone once, symlink globally, use in every project — no per-project config files needed.

## Repo structure

```text
agent-system/
├── agents/
│   ├── orchestrator-agent.md   ← start here for every new project
│   ├── pm-agent.md
│   ├── system-design-agent.md
│   ├── db-agent.md
│   ├── api-design-agent.md
│   ├── ui-designer-agent.md
│   ├── be-dev-agent.md
│   ├── qa-agent.md
│   ├── code-review-agent.md
│   └── fe-dev-agent.md
├── CLAUDE.md
└── README.md
```

---

## One-time global setup

```bash
# 1. Clone to your home directory
git clone git@github.com:YOURUSERNAME/agent-system.git ~/agent-system

# 2. Symlink agents folder into Claude's global agents directory
ln -s ~/agent-system/agents ~/.claude/agents

# 3. Install Claude Code if not already installed
npm install -g @anthropic-ai/claude-code
```

That's it. Every agent is now available via `@agent-name` in any project, globally — no per-project setup required.

---

## For every new project

### 1. Prepare your Notion page

Create a Notion page with two sections:

- **PRD** — product requirements, features, tech stack, any constraints
- **UI Brief** — design vibe, colors, component library, screen list

Copy the page ID from the URL:
`https://notion.so/My-App-PRD-<PAGE_ID>`

### 2. Open Claude Code in your project folder

```bash
cd ~/projects/my-new-app
claude
```

### 3. Spin up the orchestrator

Type this in Claude Code:

```
@orchestrator-agent
```

Claude will load the orchestrator and ask:

> "What is your Notion page ID for this project?"

Paste your Notion page ID. The orchestrator reads your PRD + UI Brief from Notion, writes them locally, and asks which phase to start.

---

## Example session

```
You:       @orchestrator-agent

Claude:    What is your Notion page ID for this project?

You:       1a2b3c4d5e6f7g8h9i0j

Claude:    Reading your Notion page...
           PRD.md and UI_BRIEF.md written locally.
           Ready. Which phase should I start?

You:       Phase 1

Claude:    Starting Phase 1 — spinning up pm-agent and system-design-agent in parallel...
           [pm-agent]            → docs/features.md ✓
           [system-design-agent] → docs/system-design.md ✓
           Running db-agent...
           [db-agent]            → docs/schema.sql + docs/db-notes.md ✓
           Running api-design-agent...
           [api-design-agent]    → docs/api-spec.md ✓

           Phase 1 complete. Docs pushed to Notion:
           - Features:       https://notion.so/...
           - System Design:  https://notion.so/...
           - DB Schema:      https://notion.so/...
           - API Spec:       https://notion.so/...
           - Kanban:         https://notion.so/...

           Please review and reply "go ahead" to start Phase 2 + Phase 3 in parallel.

You:       go ahead

Claude:    Starting Phase 2 (backend) and Phase 3 (UI mockups) in parallel...
```

---

## What the agents do

| Agent | Phase | Model | What it produces |
|---|---|---|---|
| orchestrator-agent | all | Sonnet | Coordinates all phases, manages Notion kanban |
| pm-agent | 1 | Sonnet | features.md — features list, user stories, build order |
| system-design-agent | 1 | Sonnet | system-design.md — architecture, data flows |
| db-agent | 1 | Sonnet | schema.sql — full PostgreSQL schema |
| api-design-agent | 1 | Sonnet | api-spec.md — every route, request/response |
| ui-designer-agent | 3 | Sonnet | HTML mockups for every screen |
| be-dev-agent | 2 | Sonnet | Backend routes, one feature at a time |
| qa-agent | 2+4 | Sonnet | Tests every route and screen |
| code-review-agent | 2+4 | Haiku | Reviews diffs, approves merges |
| fe-dev-agent | 4 | Sonnet | Real Next.js screens wired to API |

---

## Updating agents

Since agents are symlinked, any edit in `~/agent-system/agents/` is instantly live in all projects.

```bash
cd ~/agent-system
vim agents/qa-agent.md
git add . && git commit -m "improve qa edge case coverage"
git push
```

All projects pick up the change immediately — no re-linking needed.

---

## Default tech stack

Bun · Hono · Next.js App Router · PostgreSQL (raw SQL) · JWT + HttpOnly cookies · Redis · TypeScript strict

Override per project in your Notion PRD.
