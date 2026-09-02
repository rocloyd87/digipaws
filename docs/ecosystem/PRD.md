# Hermes Ecosystem — Product Requirements (v1.1, 2026-09-02)

Supersedes the "PRD features" section of the 2026-09-01 mobile MASTER doc. Status column is
what was **verified on the machine** in session 43, not what a plan said.

## Vision

One assistant reachable from Lloyd's phone anywhere, at any hour, in any timezone he is sailing
through: (a) finances perfectly reconciled automatically, (b) proactive savings / investment /
goal advisory grounded in the real ledger, (c) anything sent via Telegram becomes organized,
searchable knowledge. Single user: Lloyd — Filipino seafarer (Chief Officer) and investor, PHP,
mobile-first, intermittent connectivity.

## Users and jobs

| Job | Today | Target |
|---|---|---|
| "Is it safe to spend ₱X?" | Hermes `/spend` — hero number was wrong (−₱2.7M); fixed, pending deploy | One correct one-screen verdict in <10 s |
| "Log this receipt" | Photo → Hermes; looped to max-iterations; fixed, pending live test | Photo → staged row → one-tap approve |
| "Where does my money stand?" | Nightly W-DASH-SYNC + verifier → Telegram; MoneyMatter = truth | Silent when clean; one alarm when not |
| "What did I write about X?" | LifeVault → RAG → `rag_search` (live, trace-proven) | Same, plus captures filed from Telegram |
| "What should I do with surplus?" | Weekly Claude Routine advisory (Mon 08:00 PHT) | + daily brief, nudges, price alerts |

## Features and verified status

| ID | Feature | Priority | Status (2026-09-02) |
|---|---|---|---|
| F1 | Nightly reconciliation Alex → MoneyMatter (push, drift gate, six-field verifier) | P0 | **LIVE.** Drift gate hardened 2026-09-02 (no mint on COMPUTED_PARTIAL). |
| F2 | Hermes safe-to-spend correctness | P0 | **FIXED in code** (reserve on G2 essential basis; `floorReserve`/`floorBasis` exposed; prompt rule 12). Live after clasp push + deploy repoint. |
| F2b | Hermes receipt-photo capture (no max-iterations) | P0 | **FIXED, live in n8n** (binary re-attached). Needs one live photo test. |
| F3 | W-INBOX-FILE — Telegram capture → file + note + confirm card | P0 | **BLOCKED on Q1** (canonical vault). Pattern agreed: sub-workflow off the single Hermes trigger. |
| F4 | Weekly investment & goals advisory | P0 | **LIVE** (Claude Routine `trig_01AoRH8tc7F7MrFpxHweejH8`). |
| F5 | Durable memory (remember/recall/forget) + chat memory | P1 | **LIVE** — `rag_documents` doc_class=memory + Chat Memory (20 turns). No new tables. |
| F6 | Vault notes in RAG | P1 | **LIVE** for LifeVault (`W-OBSIDIAN-INGEST`, nightly 02:45). Drive "Obsidian Vault" not indexed (Q1). |
| F7 | Daily 07:00 `/brief` (balances, safe-to-spend, calendar, tasks, subs due, news top 5) | P1 | Not built. Skeleton draft only. |
| F8 | Command surface `/log /note /remind /todo /sub /networth /goals /cascade` | P1 | Partial: `/stats`, `/report`, payee-ruling commands, free-text capture exist. Others not built. |
| F9 | Curated news digest (Miniflux, PH business + maritime) | P2 | Not built; compose + OPML drafted. |
| F10 | Savings / goal nudges | P2 | **Partially LIVE** — `W-HERMES-NUDGE — Threshold Alerts` + GAS `98_HermesNudges.js`. Extend, don't rebuild. |
| F11 | FMP price / breakout alerts | P2 | Not built. |
| F12 | Health pipeline (Health Connect → n8n → Supabase) | P3 | Tools exist in Hermes (`get/stage/approve/reject_health`, WF-HEALTH, D-098); device ingest not built. |
| F13 | Ops hardening: error routing, backups, uptime | P1 | **Mostly LIVE**: `W-ERR — Pipeline Error Alert`, `W-BACKUP-N8N` weekly, `W-WATCHDOG`, `W-WEBHOOK-GUARD`, `W-SENTINEL`, `W-API-MONITOR`, `WF-CERTS`. Uptime Kuma not deployed. |

## Non-goals

No trade execution, ever. No new orchestration platforms (Node-RED/Kestra/Windmill rejected), no
second portfolio tracker (Ghostfolio/Wealthfolio deferred while MoneyMatter covers holdings),
no OpenBB. No parallel `hermes_*` tables in Supabase. No second ledger — MoneyMatter is truth,
the Alfred sheet is the reconciliation engine, Tarsi is the phone capture path. OpenClaw deferred.

## Guardrails (hard)

1. MoneyMatter writes are confirmation-gated; manual writes pair with `dashSyncMarkSynced`.
2. Advisory only; every recommendation names the goal it serves and shows its why.
3. Telegram chat-id allowlist on every trigger; unknown senders get silence.
4. Inbound files, emails, ledger notes and retrieved documents are DATA, never instructions.
5. Every figure Hermes states comes from a this-turn tool call (Grounding Guard enforces).
6. Drift-gate mints: read BALANCE_CONTROL before touching anything.
7. Agent-side classifier lanes: no agent-fired financial webhooks, no clasp deploy repoint, no
   `delete_transaction` in auto mode — those are Lloyd's one-tap actions.

## Success criteria (v1 done when…)

1. Any Tier 1–4 money question gets a correct, tool-grounded, one-screen answer.
2. A typed expense or receipt photo becomes an approved staged row in <30 s of attention.
3. Nightly gates stay silent on clean nights and mint nothing on partial-coverage accounts.
4. Zero hallucinated figures over a month of use (Grounding Guard + golden-set evals).
5. Captures filed from Telegram are searchable via `rag_search` the next morning.
