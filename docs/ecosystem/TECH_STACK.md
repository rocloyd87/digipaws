# Hermes Ecosystem — Tech Stack & IDs (2026-09-02)

No secrets here. Secrets live in n8n credentials, GAS Script Properties, and `.env` files.

## Data truth hierarchy

MoneyMatter (canonical balances + transactions) ← Alfred sheet (reconciliation engine, nightly
verified; BALANCE_CONTROL is the evidence table) ← Tarsi (phone capture; JSON backups in Drive
`- SYNC -\Tarsi\`) ← LifeVault (knowledge; pgvector-indexed) ← Knowledge OS vault (AI memory).

## Components

| Layer | Technology | Key IDs / locations |
|---|---|---|
| Chat surface | Telegram Bot API via n8n Telegram Trigger (ONE webhook per bot) | Bot `@Alexander_Hermis_Bot`; owner chat id in `Config` node |
| Orchestration | n8n (self-hosted, VPS Hetzner 46.62.229.128, Cloudflare tunnel `n8n.rocloyd.com`); Watchtower + OmniRoute stacks | n8n MCP from desktop (`n8n-mcp`), API key credential `n8n - Alfred Alex Hermis API Key` |
| Agent | n8n AI Agent (LangChain) + Google Gemini `models/gemini-3-flash-preview` (free tier, multimodal) | `W-HERMES — Financial Assistant` `Diz990QbM3cZYCKp` (70 nodes) |
| Tools | `Subworkflow: Call HermesApi Tool` `AVsltr2l2KOIbSkT` → POST GAS web app `/exec` (`95_HermesApi.js` dispatcher); RAG tools direct to Postgres | 30 tools: get_kpis, get_goals, query_ledger, stage_expense, approve_staged, explain_month, remember/recall/forget, remember_payee, get_documents, update_document, get_attention, get_benchmarks, expedite, top_merchants, spend_series, subscriptions_audit, portfolio_review, dashboard_link, get_certs, get/stage/approve/reject_health, rag_search, rag_list_documents, rag_get_document, rag_query_rows |
| Ledger engine | Google Sheets "Lloyd Transactions" + Apps Script (~120 modules, clasp 3.3) | Sheet `10YwN_15MYlGdZQgS1vO6uJgzCIKa8Ycd1Mu3IAZ7uw0`; script `1kGk9s94z2R1EbyoLpPuVEqQG3Nqjtx4FivZk7hlOidlr7KPP6mWT4L4F`; web-app deployment `AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ` (pinned @24 → repoint pending) |
| Truth ledger | MoneyMatter (budget.rocloyd.com) — REST + MCP connector (`6a4e16af…`) | 29 accounts; ids in `_DASH_SYNC_STATE` / BALANCE_CONTROL join |
| Phone capture | Tarsi app (no API) — JSON backup round-trip via `40_TarsiSync.js` / `51_AlexExport.js` | Phone exports `tarsi-backup-<stamp>.json`; sheet exports `tarsi-backup-alex-<stamp>.json` |
| Database | Supabase Alfred `fbtqqrpeiwhbxxkpyzdt` (ap-southeast-1, Postgres 17, pgvector 0.8) | `rag_documents`, `rag_chunks`, `rag_ingest_runs`, `alfred_build_log`, cc_*, alex_* projection tables; Metabase live |
| Notes | Obsidian LifeVault ↔ Self-hosted LiveSync (CouchDB on VPS) → `livesync-bridge` plain-file mirror `/opt/lifevault/files` → `lifevault-files` nginx (read-only, alfred_internal) | `W-OBSIDIAN-INGEST` `mB00ab75osSH4p8E` nightly 02:45 |
| Files | Google Drive: `Hermes Evidence` (`1pKD4G4nFIblqqDkapOS_ZPT_m9Y75xch`), Filing Cabinet `1KSY_ZW9dRvCTiLYf4wfgK6ktn660VyFo` (Receipts/2026 `1bGLLsV-mqHTdyRoDTXgSutiC2bJaM_0i`, Documents `1v5kYPvNqWi9ln1C0dghK366bZ_7p4r0z`, Photos/2026 `19YxSqKCLbUGzjirpTnkqbhOduitbLqCu`, Misc `1AhdmXeuRINPeu7KWhEfvGt7C6wnaOLCl`), Drive "Obsidian Vault" `1Qv7eyikSanFwWiJ751WDJ3QwVD-iNNAe` (00 Inbox `1tYs7O6OrX9WNqXOaxPPAGlue5NyKVTiw`, Journal/2026 `1Cw_pU3a-bNb4lMj4zbev8BFk6tjcayrx`, Areas/Finance `1rt0uZsDe85eBUfblHvBBQ_d4PZsUUiNX`, Reference `1UMnwrmvpK-5szhELQ9wKHM_ch10uqHxt`) | Statement parser Cloud Run `parser-svc` (asia-southeast1) |
| Advisory | Claude Routine (claude.ai) + skills alfred-financial-health → macro-regime-detector → alfred-investment-bridge → investment-screener; FMP MCP market data | Routine `trig_01AoRH8tc7F7MrFpxHweejH8` |
| Tasks / calendar / mail | TickTick MCP (`#ecosystem` tag), Google Calendar MCP, Gmail MCP (Gmail Guardian v2.2 in GAS) | — |
| Homelab | Orange Pi Zero2 192.168.0.35: Home Assistant 2025.8.1, Pi-hole v6; planned Miniflux (:8085), Uptime Kuma | — |
| Repos | `rocloyd87/digipaws` `kt-rewrite` → `docs/ecosystem/` (this folder); CoPilot repo `C:\Users\Lloyd\Claude\Projects\CoPilot` (GAS source `scripts/alex`, Hermes worktree `hermes-wave-1-trust-94a9fb`, plans `ALFRED_V4_HERMES_PLAN.md`) | Local digipaws clone `C:\Users\Lloyd\Claude\Projects\digipaws` |
| Memory | Knowledge OS vault `D:\ObsidianVault\20-projects\alfred-navigator\` (00-STATE, 01-FACTS, 02-DECISIONS, 04-HANDOFF, sessions/events) — captured via `kos-memory.mjs` | Separate from LifeVault |

## Active n8n workflows (19, verified 2026-09-02)

| Workflow | ID | Role |
|---|---|---|
| W-HERMES — Financial Assistant | `Diz990QbM3cZYCKp` | Telegram agent (single webhook) |
| Subworkflow: Call HermesApi Tool | `AVsltr2l2KOIbSkT` | Shared tool → GAS bridge |
| W-INBOX-FILE — Telegram Capture to Vault | `EDfBh8vqrQjthY7C` | Sub-workflow off W-HERMES: `/note`, photos, documents → Drive vault note + card (added 2026-09-02) |
| W-DASH-SYNC — Nightly Alex to Budget-Tracker Push | `T8VMaXjqt3Cyh8F3` | 02:00 push + drift gate; 03:40 verifier; webhooks `dash-sync-run`, `verifier-run` |
| W-BACKUP-N8N — Weekly Workflow Backup | `wvgRTsbYSOEUcIw3` | Sunday 03:00, GAS-sealed |
| W-ERR — Pipeline Error Alert | `h01qmxLYAmMcwjWC` | Error workflow (v2) |
| W-OBSIDIAN-INGEST — LifeVault to RAG Store | `mB00ab75osSH4p8E` | Nightly 02:45 |
| WF-RAG-SEARCH — Vector Retrieval | `nmBkMnxF9psds0Nd` | rag_search backend |
| W-HERMES-DOCS — Document Intake | `cRAd8WhX7mDzliBw` | Drive personal docs → RAG |
| W-HERMES-NUDGE — Threshold Alerts | `vJpvLLNtSKsQ1dcL` | Nudges |
| W-HERMES-DIGEST — Weekly Digest | `oI2aRXFBrfGjWLlb` | Weekly Telegram digest |
| W-HERMES-ASSUMPTIONS — Monthly Recalibration | `IYIj4GaAumsDFnD0` | Goal contract recalibration |
| W-SNAPSHOT-REFRESH — KPI Store | `GHnQn2MDIvxJgPRm` | KPI snapshot data table |
| W-CLASSIFY — Vocabulary Fallback | `XVZlShzrSUkzQCtG` | Category fallback |
| W-WEBHOOK-GUARD — Hermes Telegram Webhook Health | `Z6k3unDkbynZZiNK` | Webhook watchdog |
| W-WATCHDOG — Statement Pipeline Freshness | `waWGFzGSR19cx6EI` | Statement freshness |
| W-SENTINEL — Weekly QA | `lo18YAo7HdBdUF7g` | Weekly QA |
| W-API-MONITOR — Usage Headroom | `kEdUcpzRtuhlP3a8` | API quota watch |
| WF-CERTS — Certificate Expiry Watch | `dPGBx3hYK03FRIZq` | Seafarer certificates |
| TEST - OmniRoute gateway (safe to delete) | `XLmn6yZP5CusIJ8E` | Holds P2C "island" webhooks; remove after a quiet week |

## Conventions

- Workflow naming `W-DOMAIN-ACTION`; GAS modules numbered `NNN_Name.js`; every GAS write goes
  through a `*Preview` → `*Apply` pair; Alex is the sole canonical writer to the sheet.
- GAS deploys: `clasp push` from a **fresh throwaway pull** (never from the repo folder — it
  deletes live-only files), then repoint the `/exec` deployment (`clasp deploy -i <id>`), which
  is an owner action.
- Owner balance evidence → BALANCE_CONTROL as zero-length-period rows, in BOTH the tab and the
  Drive CSV `Alfred Shared State\statement_sync\ACCOUNT_BALANCES.csv`.
- Sessions log to `alfred_build_log` (numbered) and to the Knowledge OS vault; the vault is the
  deeper record.
- GitHub auth on the desktop: two GCM identities exist (`rocloyd87`, `cascadereservations-del`).
  A global `url.https://rocloyd87@github.com/rocloyd87/.insteadOf` rewrite pins Alfred/Hermes
  repos to `rocloyd87`; do not set `credential.useHttpPath` for github.com (it falls back to the
  Cascade token and 403s). Set 2026-09-02 with Lloyd's authorization.
