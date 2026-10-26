# Stockroom

Stockroom is a multi-warehouse inventory API with an AI assistant. I am building it from scratch as a 6-month learning project in TypeScript, Node.js, API design, PostgreSQL, system design, and AI engineering.

## What it will do

- Products (SKUs) and warehouses.
- Stock as an append-only ledger. Balances are derived from it, so concurrent updates cannot lose data.
- Reservations for orders, idempotent receiving, and CSV import as a background job.
- "Ask Stockroom": an agent that answers stock questions through tools, with evals.
- Search over supplier documents (RAG) with citations.
- An MCP server, so other AI clients can use the inventory tools.

## Stack

- Node.js (LTS) and TypeScript (strict)
- Fastify, Zod
- PostgreSQL, Drizzle ORM, pgvector, pg-boss
- Vitest, Testcontainers, Stryker, k6
- Claude API, MCP TypeScript SDK

Versions are confirmed at setup and recorded in ADR-0001.

## Plan

The full plan is in [docs/plan.md](docs/plan.md). It has 26 weeks at one hour per workday. Each day has a concept, a hands-on exercise, a commit, and a fact of the day.

## Folders

- `docs/`: the plan, and later the ADRs, design notes, and journal.
- `claude-config/`: a separate Claude Code config for this project. It is git-ignored because it can hold login data. Start Claude Code with it from this folder:

```bash
CLAUDE_CONFIG_DIR="$PWD/claude-config" claude
```

## Status

Planning. No code yet. Week 1 starts with the repo setup.
