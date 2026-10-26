# Stockroom: 6-month learning plan

Goal: middle-level skills in TypeScript, Node.js, API design, PostgreSQL, system design, and AI engineering (tool use, agents, RAG, evals, MCP, rules, and skills). Everything is built from scratch in one repo, at 1 hour per workday.

Budget: 26 weeks x 5 workdays x 1 hour = 130 hours.
- Weeks 1-12 and 14-25: six monthly phases of four weeks each, with a checkpoint on the last Friday of each month.
- Week 13 and week 26: buffer weeks. Use them for slips. If there are no slips, use them for the stretch items.

---

## 1. What changed from the 3-month plan

- **Longer phases.** Each topic now gets a full week, not a day or two. Testing, security, evals, and system design each have their own week.
- **Monthly checkpoints.** Each month ends with a demo, a written review, and merged ADRs.
- **Deeper AI work.** Added structured outputs, streaming, LLM error handling, record-and-replay tests, prompt-injection evals, LLM-as-judge with calibration, and evals in CI.
- **Deeper data work.** Added query plans, migration safety (expand/contract), connection pooling, lock ordering, and serialization retries.
- **Deployment and operations earlier.** Deploy to staging in month 6, with a runbook for migrations and rollback.
- **System design practice in three places.** A design review at the end of month 3, a design week in month 6, and a mock interview in month 6.
- **Fact seeds are search topics, not claims.** The fact of the day comes from a search each day (see section 4).

---

## 2. Stack (confirm versions at setup)

| Area | Choice | Note |
|---|---|---|
| Runtime | Node.js LTS (24 now; 26 when it is LTS) | Confirm on nodejs.org at setup. Record the version in ADR-0001. |
| Language | TypeScript 7.0 (native compiler), `strict` on | Reported stable July 2026. Confirm in the TypeScript release notes. |
| HTTP | Fastify | Plugins, schema validation, `inject()` tests. |
| Validation | Zod | HTTP input, env config, and tool inputs. |
| Database | PostgreSQL + Drizzle ORM + drizzle-kit | Prisma is also production-ready; the choice is recorded in ADR-0001. |
| Vector search | pgvector | Same database as the rest of the app. |
| Jobs | pg-boss | Postgres-backed queue. Redis only if month 6 measurements show a need. |
| Tests | Vitest, Testcontainers, Stryker, k6 | Real Postgres in tests; mutation testing on the domain; load tests. |
| Lint/format | Biome | Lint and format in one tool. |
| Logs / traces | pino, OpenTelemetry | Structured logs, request IDs, traces across HTTP and jobs. |
| AI | Claude API (Messages, structured outputs, tool use, prompt caching, streaming), MCP TypeScript SDK | Use the current Claude model list from the Claude API docs at setup; record the model in an ADR. MCP spec 2026-07-28 per the versioning page; read the official spec in week 21. |
| Agent tooling | CLAUDE.md, Claude Code skills (`SKILL.md`), subagents | Project rules, reusable workflows, review subagents. |
| Delivery | Docker, GitHub Actions, any container host | Multi-stage Dockerfile, health checks, migrations run as a separate job. |
| Docs | ADRs in `docs/adr/`, C4 and Mermaid diagrams in `docs/design/` | Design reasoning lives in the repo. |

---

## 3. Daily format (60 minutes)

| Block | Time | What you do |
|---|---|---|
| Concept | 10 min | Read the official doc section or spec for the day's concept. Write 3 sentences in your own words in `docs/journal/`. |
| Practice | 35 min | Build the exercise by hand. Predict the result before you run it. |
| Repo | 10 min | Commit with a Conventional Commit message (`feat:`, `test:`, `docs:`). |
| Fact | 5 min | Search for the fact of the day (section 4). Write it to `docs/facts.md`. |

Friday: 10 min to review the week and merge the weekly PR (`week-N`). The weekly PR is the unit of review.

Claude's role: review your code, quiz you ("what happens if two requests run at once?"), explain errors, and critique your ADRs. It does not write the day's core code.

---

## 4. Fact of the day

Each day, search the web for one fact. It must be either:
- **Relevant:** important for what you build or learn today. Start from the "Fact seed" in today's row, then look for the current version of it.
- **New:** released or announced today or this week, in Node.js, TypeScript, PostgreSQL, Anthropic/Claude, MCP, or a library in the stack.

Steps:
1. Run a standard web search on the seed topic, or on the topic of the day. Pick the result that matters most for Stockroom.
2. On Fridays, also run one extended search for this week's news in those areas. Extended search costs more, so once a week is enough.
3. Write the entry in `docs/facts.md`: date, the fact in one sentence, the source link, and one line on why it matters for Stockroom.
4. If nothing new matters that day, use the seed topic, check its current status, and say in the entry that it is not new.

---

## 5. Repo layout (`stockroom`)

```
stockroom/
  CLAUDE.md                 project rules (month 5)
  .claude/skills/           reusable workflows (month 5)
  src/
    server.ts               Fastify app
    app.ts                  composition root
    config.ts               Zod-validated env
    db/                     Drizzle schema, migrations, seed
    modules/
      catalog/              products, SKUs, search
      inventory/            warehouses, stock movements, balances, transfers
      orders/               reservations
      auth/                 users, sessions, roles
      money/                minor units, rounding
    jobs/                   pg-boss workers
    ai/
      llm.ts                Claude client, retries, budget
      extract.ts            structured extraction
      tools.ts              tool definitions and validators
      agent.ts              agent loop
      rag/                  ingestion, chunking, retrieval, answers
  mcp/                      MCP server
  evals/                    agent, judge, retrieval, RAG, injection evals
  perf/                     k6 scripts and results
  deploy/                   Dockerfile, deploy config
  experiments/              throwaway scripts
  docs/
    adr/                    decision records
    design/                 C4 diagrams, scale notes, failure tables
    api/openapi.yaml        API contract
    runbooks/               deploy, rollback, drills
    reviews/                month-end reviews
    journal/                daily notes
    facts.md                fact of the day log
```

---

## 6. Phases at a glance

| Month | Weeks | Theme | Exit deliverable |
|---|---|---|---|
| 1 | 1-4 | TypeScript, Node, HTTP APIs | Catalog API with tests and OpenAPI contract |
| 2 | 5-8 | PostgreSQL, Drizzle, transactions, the stock ledger | Ledger with no lost updates; reconciliation test |
| 3 | 9-12 | Testing, idempotency, reservations, auth, jobs, observability | Production-grade backend; design review #1 |
| (buffer) | 13 | Catch-up | Skipped items done |
| 4 | 14-17 | LLM API, tool use, agent loop, guardrails, evals | Agent answering stock questions, with evals in CI |
| 5 | 18-21 | Embeddings, RAG, retrieval evals, MCP, CLAUDE.md, skills | RAG with citations and an eval report; MCP server in a client |
| 6 | 22-25 | System design, deployment, performance, capstone | Deployed staging app, runbooks, demo, mock design interview |
| (buffer) | 26 | Catch-up and retro | Final retro, next project chosen |

---

## 7. Weekly plan

Each week has 5 days. Columns: concept, practice, repo deliverable, and a fact seed (a search topic for the fact of the day).

### Month 1: TypeScript, Node, and HTTP APIs

#### Week 1: Toolchain and TypeScript basics
Weekly deliverable: CI green (typecheck, lint, test) on `main`.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Event loop, microtasks vs macrotasks | Script that logs `setTimeout`, `setImmediate`, `process.nextTick`, and a Promise. Predict the order first. | Init repo, README, `.editorconfig` | Node.js release schedule and current LTS |
| Tue | Unions, narrowing, generics | Model `Money`, `Sku`, and `Quantity` with branded types. | `tsconfig` strict flags | TypeScript 7 release notes |
| Wed | ESM vs CJS, `package.json` `exports`, lockfiles | Small ESM package run with `tsx`. | pnpm, Biome | pnpm workspace features |
| Thu | Promises, async/await, `AbortController` | `fetchWithRetry` with timeout and backoff. | Retry util + Vitest tests | `AbortSignal.timeout` in Node |
| Fri | Streams and bounded memory | CSV dry-run that counts rows without loading the file. | GitHub Actions CI. Merge `week-1`. | Node stream backpressure |

#### Week 2: HTTP semantics and REST design
Weekly deliverable: products CRUD with OpenAPI contract and tests.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | HTTP methods, status codes, idempotency, caching headers | Raw `node:http` server with 3 routes. Call with `curl`. | `experiments/raw-http/` | RFC 9110 HTTP semantics |
| Tue | OpenAPI-first design, pagination styles | Write `openapi.yaml` for catalog endpoints before any code. | `docs/api/openapi.yaml` | OpenAPI 3.1 changes |
| Wed | Fastify plugins, encapsulation, `inject()` tests | Products CRUD in memory with Zod type provider. | Catalog module + tests | Fastify current major version |
| Thu | Validation and error responses | 400 with field errors; 500 without a stack trace. Use RFC 9457 problem details. | Error handler + tests | RFC 9457 problem details |
| Fri | Keyset pagination and filtering | Cursor pagination on `(created_at, id)`. Merge `week-2`. | Pagination + tests | Keyset vs offset pagination performance |

#### Week 3: TypeScript at depth and code structure
Weekly deliverable: typed domain errors and a composition root.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Conditional and mapped types, `satisfies` | Typed route map or event map. | Typed event bus | `satisfies` operator in TypeScript |
| Tue | Discriminated unions, exhaustive checks, errors as values | Domain errors as values in inventory operations. | ADR-0001 error handling | Error handling as values in TypeScript |
| Wed | Dependency injection without a framework | Wire services in a composition root. Test with fakes. | `src/app.ts` | Composition root pattern |
| Thu | Config and logging | Zod env config that fails fast. pino logger with redaction. | `config.ts`, logger | pino redaction and log levels |
| Fri | Simplify pass. Merge `week-3`. | Remove one abstraction you do not need. | Refactor commit | TypeScript strict-mode defaults |

#### Week 4: Team workflow and month 1 checkpoint
Weekly deliverable: month 1 review, demo of the catalog API, and merged `month-1` PR.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Trunk-based development, small PRs | Reword and squash your branch commits with `git rebase -i` in your terminal. | `CONTRIBUTING.md` | Trunk-based development |
| Tue | API versioning | Add search by SKU with tests on a feature branch. | Feature branch | API versioning strategies |
| Wed | Architecture decision records | Write ADR-0001 stack decision with trade-offs. | `docs/adr/0001-stack.md` | ADR template (Michael Nygard) |
| Thu | Event loop blocking | Find a blocking handler with a slow JSON path and fix it. | `perf/` note | Event loop lag monitoring |
| Fri | Month 1 review | Demo the catalog API. Write the retro. | `docs/reviews/month-1.md`. Merge `month-1`. | Node.js diagnostics channel |

### Month 2: PostgreSQL, Drizzle, transactions, and the ledger

#### Week 5: PostgreSQL fundamentals
Weekly deliverable: schema written in SQL by hand, running in Docker.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Tables, keys, constraints, joins | Write the schema in plain SQL. Run Postgres in Docker. | `docker-compose.yml`, `schema.sql` | Current PostgreSQL major version |
| Tue | `EXPLAIN` and `EXPLAIN ANALYZE` | Read the plan for 3 queries on 100k rows. | `docs/design/query-plans.md` | `EXPLAIN (ANALYZE, BUFFERS)` |
| Wed | Indexes: B-tree, composite, partial | Seed 1M rows and measure an index's effect. | Seed script + index migration | Partial indexes in PostgreSQL |
| Thu | Foreign keys and indexing | Query your schema for foreign keys with no index. Fix them. | Migration | Unindexed foreign keys in PostgreSQL |
| Fri | Query stats | Find the slowest queries with `pg_stat_statements`. Merge `week-5`. | Notes | `pg_stat_statements` |

#### Week 6: Drizzle and migrations
Weekly deliverable: Drizzle schema, migration history, and a safe NOT NULL migration.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Schema as code; drizzle-kit migrations | Rewrite the schema in Drizzle. Compare generated SQL with your handwritten SQL. | `src/db/schema.ts` | drizzle-kit migrate vs push |
| Tue | Query builder vs relational queries | Write one query three ways. | Query examples + tests | Drizzle relational queries |
| Wed | Expand/contract migrations | Add a NOT NULL column in two safe migrations. | `docs/design/migrations.md` | Expand/contract migrations |
| Thu | Connection pooling | Set pool size. Test under concurrent requests. | DB client config | PgBouncer transaction pooling |
| Fri | Review. Merge `week-6`. | Check migration history is reproducible from zero. | Script | Drizzle release status |

#### Week 7: Transactions and concurrency
Weekly deliverable: lost update reproduced, fixed two ways, and a deadlock case handled.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | ACID and isolation levels | Reproduce a lost update with two scripts. | Test that reproduces it | PostgreSQL isolation levels |
| Tue | Row locks and conditional updates | Fix the lost update with `SELECT ... FOR UPDATE` and with a conditional `UPDATE`. Compare. | `docs/design/concurrency.md` | `SELECT ... FOR UPDATE SKIP LOCKED` |
| Wed | Deadlocks and lock ordering | Create a deadlock in a warehouse-to-warehouse transfer. Fix it by ordering locks. | Test | Deadlock detection in PostgreSQL |
| Thu | Serializable isolation and retries | Retry on serialization failure with a bounded number of attempts. | `withTransaction` helper | Serialization failure (SQLSTATE 40001) |
| Fri | Review. Merge `week-7`. | Check each concurrency test fails without its fix. | Test notes | PostgreSQL release notes, latest version |

#### Week 8: The stock ledger
Weekly deliverable: ledger with receive, ship, adjust, and transfer; reconciliation test; ADR-0002.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Append-only movements | `receive`, `ship`, `adjust` in one transaction. | Inventory module | Event-sourced ledgers |
| Tue | Derived balances and a balance cache | Update balances in the same transaction. Write a job that reconciles sum vs balance. | Reconciliation test | Double-entry bookkeeping |
| Wed | Constraints as guards | `CHECK (quantity >= 0)`. Tests show the database rejects bad writes. | Migration | PostgreSQL CHECK constraints |
| Thu | Transfers as two legs | Transfer atomically. Test a failure in the middle. | Transfer endpoint | Saga pattern |
| Fri | Month 2 review. Merge `month-2`. | Demo the ledger. Write ADR-0002. | `docs/reviews/month-2.md`, ADR-0002 | Temporal tables in PostgreSQL |

### Month 3: Production backend

#### Week 9: Testing strategy
Weekly deliverable: integration tests against real Postgres in CI; mutation score on the domain module.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Test pyramid vs testing trophy | Classify existing tests. Add domain unit tests. | `docs/design/testing.md` | Testing trophy |
| Tue | Integration tests with Testcontainers | Receive and ship tests against real Postgres. | Test helpers | Testcontainers for Node.js |
| Wed | Contract tests against OpenAPI | Validate responses against `openapi.yaml`. | Contract tests | OpenAPI 3.1 validation tools |
| Thu | Mutation testing | Run Stryker on the domain. Kill 3 surviving mutants with better assertions. | `stryker.config` | Stryker mutation testing |
| Fri | CI with service containers. Merge `week-9`. | Run integration tests in GitHub Actions. | CI workflow | GitHub Actions service containers |

#### Week 10: Idempotency, reservations, and the outbox
Weekly deliverable: idempotent receive; reservations that cannot oversell under concurrency; ADR-0003.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Idempotency keys | `Idempotency-Key` on `POST /stock/receive`. Store the response and replay it. | Idempotency module | Stripe idempotency keys |
| Tue | Reservations: available = on hand minus reserved | `POST /orders` reserves stock. Cancel releases it. | Orders module + tests | Stock reservation patterns |
| Wed | Transactional outbox | Write an outbox row in the same transaction. A worker publishes it to pg-boss. | ADR-0003 | Transactional outbox pattern |
| Thu | Concurrency under load | 50 parallel reserves on 10 units. Exactly 10 succeed. | Concurrency test | Overselling prevention |
| Fri | Review. Merge `week-10`. | Check the outbox replays after a crash. | Test notes | Idempotency in distributed systems |

#### Week 11: Authentication and security
Weekly deliverable: login with refresh rotation, per-warehouse roles, IDOR tests, and a STRIDE model.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Password storage | Register and log in with `crypto.scrypt`. | `src/modules/auth/` | OWASP password storage cheat sheet |
| Tue | Refresh token rotation and reuse detection | Rotate refresh tokens. Store them hashed. Detect reuse. | Auth tests | Refresh token rotation |
| Wed | Authorization per warehouse | Role checks in the service layer. IDOR tests for cross-warehouse access. | RBAC tests | OWASP API Security Top 10 (2023) |
| Thu | Rate limiting and body limits | Limit login attempts. Set request body limits. | Rate-limit plugin | Rate limiting algorithms |
| Fri | Threat model. Merge `week-11`. | STRIDE table for the stock API. | `docs/design/threat-model.md` | OWASP ASVS current version |

#### Week 12: Background jobs and observability
Weekly deliverable: pg-boss workers for outbox and CSV import, dead-letter queue, and a trace from HTTP to job.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | At-least-once delivery; idempotent consumers | pg-boss worker for the outbox and CSV import. | `src/jobs/` | pg-boss features |
| Tue | Retries, backoff, dead letters | A job that fails 3 times moves to the dead-letter queue. | Job tests | Dead-letter queue pattern |
| Wed | Traces across HTTP and jobs | One OpenTelemetry trace from the request into the job. | `docs/design/observability.md` | OpenTelemetry Node.js SDK status |
| Thu | SLOs and RED metrics | Define 2 SLOs. Expose metrics. | SLO doc | Error budgets |
| Fri | Month 3 review and design review #1. Merge `month-3`. | Review the monolith's module boundaries. | `docs/reviews/month-3.md` | Modular monolith |

#### Week 13 (buffer): Catch-up
- Mon-Wed: finish skipped items in this order: must-haves from months 1-3, then the rest.
- Thu: load test baseline with k6 on the catalog and reserve endpoints. Record the numbers in `perf/`. Use this day as stretch if there are no slips.
- Fri: short retro. Note what slipped and why.

### Month 4: AI engineering I

#### Week 14: LLM API fundamentals
Weekly deliverable: a Claude client with retries and a token budget; a structured extraction endpoint.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Tokens, context windows, temperature, usage | Minimal client. Print the usage field for each call. | `src/ai/llm.ts` | Current Claude model list and pricing |
| Tue | Structured outputs | Extract a supplier order from text into Zod-validated JSON. | `src/ai/extract.ts` | Claude structured outputs |
| Wed | Streaming responses | Stream a summary to HTTP clients using server-sent events. | Stream route | Server-sent events in Fastify |
| Thu | Errors, rate limits, timeouts | Wrap the client with retry, timeout, and a spend limit. | `src/ai/llm.ts` tests | Claude API rate limits and errors |
| Fri | Merge `week-14` | Test the extraction against 10 sample documents. | Eval notes | Claude model deprecation schedule |

#### Week 15: Tool use and the agent loop
Weekly deliverable: an agent that answers multi-step stock questions through tools; deterministic tests with recorded responses.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Tool definitions as JSON Schema | `get_stock_level` tool wired to your API. | `src/ai/tools.ts` | Claude tool use documentation |
| Tue | The agent loop by hand | `while` loop on `stop_reason`. Iteration cap. | `src/ai/agent.ts` | Agent stop reasons |
| Wed | Parallel tool calls | "Which warehouse holds the most of SKU X, and what should we reorder?" | Tests with a fake model | Parallel tool use |
| Thu | Record and replay tests | Record real model responses once. Replay them in tests. | Test fixtures | LLM test record and replay |
| Fri | Merge `week-15` | Compare a hand-written loop with a framework's loop. | Notes in `docs/journal/` | Agent frameworks vs hand-written loops |

#### Week 16: Guardrails and safety
Weekly deliverable: write tools need confirmation; tool inputs validated; tool access scoped by role; injection evals passing; ADR-0004.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Read vs write tools; confirmation | Write tools need a confirmation token from the user. | ADR-0004 | Human-in-the-loop agent approval |
| Tue | Prompt injection from data | Product descriptions with hostile text. The agent must not make unsafe tool calls. | `evals/injection/` | OWASP Top 10 for LLM Applications |
| Wed | Validation of tool inputs | Zod on every tool input. Reject out-of-range quantities. | Tool validators | OWASP LLM Top 10 current version |
| Thu | Least privilege per role | The agent only sees tools the user's role allows. | Tool RBAC | Least privilege for AI agents |
| Fri | Merge `week-16` | Run injection evals. Record the results. | Results in `evals/` | Recent AI security incidents |

#### Week 17: Evals, judges, and cost
Weekly deliverable: 25-case agent eval with a pass threshold in CI; calibrated LLM judge; prompt caching cost report; month 4 review.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Eval design: golden set, graders, pass rate | 25 cases for agent answers. | `evals/agent/` | LLM evaluation methodology |
| Tue | LLM as judge and its biases | Judge answers with a rubric. Check the judge against 5 human labels. | `evals/judge/` | LLM-as-judge bias |
| Wed | Prompt caching and cost | Cache the system prompt and tool definitions. Report cost per question. | Cost report | Claude prompt caching |
| Thu | Evals in CI | Run an eval subset in GitHub Actions with a pass threshold. | CI workflow | Running evals in CI |
| Fri | Month 4 review. Merge `month-4`. | Demo the agent. Show the eval dashboard. | `docs/reviews/month-4.md` | Pinning model versions |

### Month 5: RAG, MCP, and agent tooling

#### Week 18: Embeddings and vector search
Weekly deliverable: supplier documents ingested into pgvector with metadata filters.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Embeddings and similarity | Compute cosine similarity by hand for 5 sentences. | `experiments/embeddings/` | Embedding models recommended in the Claude API docs |
| Tue | Chunking and ingestion | Load 10 supplier documents. Chunk with overlap. Store source and position. | Ingestion job | RAG chunking strategies |
| Wed | pgvector and HNSW | Insert embeddings and query the top 5. | Migration | pgvector HNSW vs IVFFlat |
| Thu | Filtered vector search | Filter by supplier and date, then top-k. | Query + test | Filtered vector search |
| Fri | Merge `week-18` | Check the index is used with `EXPLAIN`. | Notes | pgvector release notes |

#### Week 19: Hybrid retrieval and reranking
Weekly deliverable: keyword and vector results combined; answers with citations; uncited claims rejected.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Full-text search with `tsvector` | Keyword search for SKUs and part numbers. | Retrieval module | PostgreSQL full-text search |
| Tue | Reciprocal rank fusion | Combine keyword and vector results. | RRF function + tests | Reciprocal rank fusion |
| Wed | Reranking | Rerank the top 20 with a reranker or an LLM. Measure the change. | `src/ai/rag/rerank.ts` | Reranker models for RAG |
| Thu | Context assembly and citations | Answer with citations. Reject answers with uncited claims. | `src/ai/rag/answer.ts` | Citation-grounded answers |
| Fri | Merge `week-19` | Review 10 answers by hand. | Notes | Contextual retrieval |

#### Week 20: RAG evals and production issues
Weekly deliverable: retrieval and answer evals; incremental re-indexing; latency budget; eval report.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Retrieval metrics: recall@k, MRR, nDCG | Compute them for 30 questions. | `evals/retrieval/` | nDCG and MRR |
| Tue | Answer metrics: faithfulness, citation accuracy | Grade 20 answers. | `evals/rag/` | RAG faithfulness evaluation |
| Wed | Incremental re-indexing | Re-index only chunks whose content hash changed. | Ingestion | Incremental indexing with content hashes |
| Thu | Latency budget | Measure each stage. Cut the slowest one. | `perf/` note | RAG latency optimization |
| Fri | Merge `week-20` | Write the eval report. | `docs/reviews/rag-eval.md` | RAG best practices |

#### Week 21: MCP, CLAUDE.md, skills, and subagents
Weekly deliverable: MCP server working in a real client; project rules in CLAUDE.md; a tested skill; month 5 review.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | MCP: tools, resources, prompts, transports | Read the 2026-07-28 spec. Build an MCP server with 2 read tools. | `mcp/` | MCP specification changelog |
| Tue | Running MCP in a client | Connect to Claude Desktop or Claude Code. Test each tool. | `docs/mcp.md` | MCP stdio vs HTTP transport |
| Wed | CLAUDE.md | Write project rules. Check them with a small task. | `CLAUDE.md` | CLAUDE.md best practices |
| Thu | Skills | Write an "add-endpoint" skill. Test that it triggers on the right request. | `.claude/skills/add-endpoint/SKILL.md` | Claude Code skills |
| Fri | Subagents and review. Month 5 review. Merge `month-5`. | Ask a review subagent to review the month's PR. Compare findings. | `docs/reviews/month-5.md` | Subagent patterns |

### Month 6: System design, deployment, and capstone

#### Week 22: System design method
Weekly deliverable: scale estimates, failure table, caching and partitioning ADRs.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Requirements, estimates, API, data model | 10x scale estimates with stated assumptions. | `docs/design/scale.md` | Back-of-envelope estimates |
| Tue | Consistency models and failure modes | Failure table: DB down, queue lag, duplicate events, worker crash. | `docs/design/failures.md` | Consistency models |
| Wed | Caching and invalidation | Decide caching for catalog reads. Write ADR-0005. | ADR-0005 | Cache invalidation strategies |
| Thu | Partitioning | Partition `stock_movements` by time. Check pruning with `EXPLAIN`. | Migration experiment | PostgreSQL declarative partitioning |
| Fri | Design review #2 (written) | Write a design for a multi-warehouse tenant feature. | `docs/design/tenancy.md` | Multi-tenancy patterns |

#### Week 23: Deployment and operations
Weekly deliverable: staging deployment with migrations as a separate job, health checks, graceful shutdown, and a rollback runbook.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Multi-stage Docker builds, non-root user | Dockerfile for the API. | `deploy/Dockerfile` | Docker multi-stage build practices |
| Tue | Health checks and graceful shutdown | Handle SIGTERM. Drain connections. | Server shutdown code | Graceful shutdown in Node.js |
| Wed | Migrations on deploy; rollback | Run migrations as a separate job. Test rollback. | `docs/runbooks/deploy.md` | Database migrations in CI/CD |
| Thu | Staging deploy and smoke tests | Deploy to a container host. Run smoke tests. | Deploy workflow | GitHub Actions deploy pipelines |
| Fri | Merge `week-23` | Check the staging logs and traces. | Notes | Container host platform changes |

#### Week 24: Performance and reliability
Weekly deliverable: load test report with numbers; one profiled fix; timeouts on external calls; chaos drill record.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Load testing with k6 | Scenario for catalog reads and reservations. Record the baseline. | `perf/` | k6 load testing |
| Tue | Profiling | `--cpu-prof` and heap snapshots. Fix one hotspot. Show before and after. | `perf/` report | Node.js profiling tools |
| Wed | Timeouts, retries, circuit breakers | Set timeouts on LLM and embedding calls. | `src/resilience/` | Circuit breaker pattern |
| Thu | Chaos drill | Kill the worker. Slow the database. Record what happens. | `docs/runbooks/drills.md` | Chaos engineering basics |
| Fri | Merge `week-24` | Compare drill results with the design's failure table. | Notes | Error budget policies |

#### Week 25: Capstone polish
Weekly deliverable: AI security re-check, architecture docs, a 5-minute demo, and a mock design interview.

| Day | Concept | Practice | Repo | Fact seed |
|---|---|---|---|---|
| Mon | Security review of AI endpoints | Re-run the injection and tool-permission evals. | `evals/` | AI security checklists |
| Tue | Architecture documentation | README and C4 diagrams (context and container). | `docs/architecture.md` | C4 model |
| Wed | Demo recording | Record a 5-minute demo. Write the script. | `docs/demo.md` | Demo best practices |
| Thu | Mock system design interview | Answer one prompt in 45 minutes. List the gaps. | `docs/design/mock-interview.md` | System design interview frameworks |
| Fri | Retro and merge `week-25` | Write the month 6 review. | `docs/reviews/month-6.md` | Node.js and TypeScript news this month |

#### Week 26 (buffer): Catch-up and close
- Mon-Wed: finish skipped items, must-haves first.
- Thu: write one short post on the concept you found hardest, from your journal.
- Fri: final retro. Choose the next project and the next concept to learn.

---

## 8. Month-end checkpoints

| Checkpoint | Week | Output |
|---|---|---|
| Month 1 | 4, Fri | Demo, retro, ADR-0001 |
| Month 2 | 8, Fri | Demo, retro, ADR-0002 |
| Month 3 | 12, Fri | Design review #1, retro, ADR-0003 |
| Month 4 | 17, Fri | Agent demo, eval dashboard, retro |
| Month 5 | 21, Fri | RAG eval report, MCP demo, retro |
| Month 6 | 25, Fri | Staging deploy, demo, mock interview, retro |

---

## 9. Must-have vs stretch

| Priority | Weeks | If you fall behind |
|---|---|---|
| Must | 1-12 (backend), 14-17 (agent and evals), 18-21 (RAG, MCP, rules, skills) | Do not cut these. Use buffer weeks first. |
| Should | 22-25 (design, deployment, performance, polish) | Keep the deliverable. Shorten each practice to one exercise. |
| Stretch | Week 13 and 26 extras, a Redis cache, a second LLM provider behind one interface, a small frontend | Only if ahead of plan. |

---

## 10. Sources to read

- Node.js docs (event loop, streams, `node:crypto`) and the nodejs.org release schedule
- TypeScript handbook and the TypeScript 7 release notes
- PostgreSQL docs: transactions, indexes, `EXPLAIN`, locking
- Claude API docs: tool use, structured outputs, prompt caching, streaming, rate limits
- Model Context Protocol specification and its versioning page
- OWASP API Security Top 10 and OWASP Top 10 for LLM Applications
- Designing Data-Intensive Applications (Martin Kleppmann): the reference for months 3 and 6
- Michael Nygard's ADR template

---

## 11. Verification notes

- Node version: confirm the current LTS on nodejs.org at setup. Sources disagreed on whether Node 26 is LTS yet.
- TypeScript 7.0: sources reported a stable release in July 2026, with different dates. Confirm in the official release notes.
- MCP spec: the 2026-07-28 changes come from secondary sources. Read the official spec in week 21.
- Claude models and pricing change. Check the Claude API docs at setup and again before weeks 14 and 17.
- Drizzle vs Prisma: both are production-ready. Drizzle is chosen for SQL-first learning. Check npm versions at setup.
- Fact seeds are search topics. They were not checked against sources. The daily search and its source link do the verification.
