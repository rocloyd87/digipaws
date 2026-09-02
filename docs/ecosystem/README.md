# Hermes Ecosystem — Master Handoff (2026-09-02, session 43)

Master handoff for Lloyd's personal-OS project (Hermes / Alfred / Alex / Tarsi / MoneyMatter),
versioned here so any worktree or session can reconstruct the state. This folder is documentation
only — it does not touch the DigiPaws app code that shares this repository.

**Companion files in this folder (read in this order):**

| File | What it is |
|---|---|
| `README.md` (this) | Verified system state, corrections to the 2026-09-01 mobile handoff, build order |
| `PRD.md` | Product requirements — vision, features F1–F13 with **verified** status, guardrails |
| `TECH_STACK.md` | Every component, ID, integration and convention (no secrets) |
| `TASKLIST.md` | The live checklist: done / blocked-on-Lloyd / next, by build order |
| `CONTINUE.md` | Paste-ready prompt for the next session |
| `sessions/2026-09-02-session-43.md` | Evidence trail for everything asserted here |
| `variance-forensics-2026-09-01.md` | Mobile-session forensics (read with the corrections below) |
| `miniflux-homelab.md` | Pi-sized Miniflux compose + OPML for the news digest (unbuilt) |
| `n8n-drafts/` | Mobile-session drafts — see `n8n-drafts/README.md` for which are superseded |

**Other read-order items for a fresh session:**
1. Last ~10 rows of `public.alfred_build_log` (Supabase `fbtqqrpeiwhbxxkpyzdt`) — canonical
   cross-session ledger. Session 43 (2026-09-02, desktop) is the latest.
2. Knowledge OS vault `D:\ObsidianVault\20-projects\alfred-navigator\` (00-STATE, 04-HANDOFF,
   02-DECISIONS D-092…D-108, 01-FACTS) — the desktop-side memory; deeper than this folder.
3. Google Drive → `Obsidian Vault/04 Resources/Reference` — the 2026-09-01 mobile spec docs
   (MASTER handoff, MASTER corrections, inbox build spec, v2 addendum, proactive advisory plan).
   They are now **superseded where this README disagrees**; a pointer was written into them.

## System state (verified 2026-09-02, desktop)

| Component | State |
|---|---|
| MoneyMatter (budget.rocloyd.com) | TRUTH ledger, 29 accounts. MCP connector **works from the desktop session** (`get_accounts`, `search_transactions`, `delete_transaction` all exercised). 8 evidenced accounts EXACT vs BALANCE_CONTROL. |
| Tarsi (phone) | Re-import of `tarsi-backup-alex-20260901-131025.json` **confirmed by Lloyd 2026-09-02**. Phone exports are named `tarsi-backup-<stamp>.json` **by design** — see correction C2. |
| Hermes (n8n `W-HERMES`, `Diz990QbM3cZYCKp`) | v1 live, 70 nodes, Gemini `gemini-3-flash-preview`, 30 tools via `Subworkflow: Call HermesApi Tool` (`AVsltr2l2KOIbSkT`) → GAS `95_HermesApi.js`. **Two bugs fixed this session** (see Hermes fixes). |
| n8n (n8n.rocloyd.com, VPS 46.62.229.128) | 19 active workflows (full list in `TECH_STACK.md`). Reachable from the desktop via the n8n MCP — the "n8n credentials" blocker of session 42 does not apply on desktop. |
| Alfred sheet + GAS (Lloyd Transactions, 120 modules) | Live. Web-app deployment pinned at **@24** (`AKfycbw9t20…`); modules 128–141 + the session-43 metrics fix need a deploy repoint to serve through `/exec`. |
| Supabase Alfred (`fbtqqrpeiwhbxxkpyzdt`) | pgvector RAG store LIVE (`rag_documents`/`rag_chunks`/`rag_ingest_runs`), `alfred_build_log`, cc_* pipeline, Alex projection tables, Metabase. Never create parallel `hermes_*` tables. |
| Notes / RAG | **Already built** (D-100, R8 closed 2026-08-31): Obsidian **LifeVault** ↔ Self-hosted LiveSync (CouchDB on VPS) → plain-file mirror → `W-OBSIDIAN-INGEST` nightly 02:45 → `rag_search` in Hermes (journal recall trace-proven). The Drive "Obsidian Vault" PARA scaffold from 2026-09-01 is a **second, separate vault** — see open question Q1. |
| Claude Routine | Weekly Investment & Goals Advisory `trig_01AoRH8tc7F7MrFpxHweejH8`, Mondays 00:00 UTC (08:00 PHT). |
| Homelab (Orange Pi Zero2, 192.168.0.35) | HA 2025.8.1 + Pi-hole v6. Miniflux / Uptime Kuma still planned. |
| Nightly gates | `W-DASH-SYNC` 02:00 (push + drift gate, mint >10k only) and 03:40 six-field verifier → Telegram. Drift Gate patched 2026-09-02 so a `computed_balance` target with non-FULL coverage is **reported but never minted** (D-106). |

## Corrections to the 2026-09-01 mobile handoff (all verified on the machine)

- **C1 — W-BACKUP-N8N already exists and is active** (`wvgRTsbYSOEUcIw3`, Sunday 03:00, stores +
  seals every workflow JSON through GAS `125_N8nBackup.js`, D-046/D-048, Telegram verdict).
  The mobile draft `n8n-drafts/W-BACKUP-N8N.json` is redundant — do not import it.
- **C2 — The "dropped alex prefix" is not a defect.** `40_TarsiSync.js` accepts phone exports
  `tarsi-backup-<yyyyMMdd>-<HHmmss>.json` and **excludes** `tarsi-backup-alex-*` (those are the
  sheet's own exports for import INTO the phone, `51_AlexExport.js`). No glob needs changing.
- **C3 — BPI USD: the $2.69 fee already exists in MoneyMatter** (2026-08-11, "Owner-verified
  balance … was 3.00, now 0.31", tag `alx-verified-c67ab1b0938393e4`). Do **not** add another.
  The account still shows 3.00 because the P2C opening-balance reconciliation pinned it there;
  the remaining fix is a **balance adjustment to 0.31** (sub-threshold: the drift gate reports
  −2.69 nightly but will not mint). Needs Lloyd's OK.
- **C4 — Cash is NOT fixed; it is double-counted.** MoneyMatter holds HENRICH −410 twice on
  2026-08-24: the manual row posted from the phone on 2026-09-01 (`01a05bc6-d3e3-763a-a138-2467f9bf7126`,
  untagged) and the nightly's own tagged push (`01a05e21-986e-73a7-af41-82919e5adf73`,
  `[alex:alx-2ceaca401e140298a9815c3f]`). MoneyMatter Cash reads 5,626 vs truth 6,036. **Delete
  the untagged manual row.** (Agent attempt was classifier-blocked; Lloyd deletes or approves.)
  Lesson, already in the vault: manual MoneyMatter writes must be paired with
  `dashSyncMarkSynced` or the nightly re-pushes the ledger row.
- **C5 — GCash: MoneyMatter has only 3 rows since Jul 15** (+2,000 Jul-21 — the *second* Tarsi
  copy `gcash-712b…`, not both; −381 Aug-04; +59,577.75 Aug-31 drift correction). No 85.09
  decomposition exists on either side; MoneyMatter's 295.01 is a drift-corrected figure.
  BALANCE_CONTROL: tarsi = statement = 380.10 (as of 2026-08-04). Fix = balance adjustment to
  380.10 once Lloyd confirms the live app. The Tarsi duplicate ₱2,000 (`alx-gcash-712b2685…`)
  is phone-side only.
- **C6 — "F5 memory / F6 vault RAG" are not P1 backlog, they are LIVE:** `remember`/`recall`/
  `forget` tools (doc_class=memory), Chat Memory buffer (20 turns, keyed by chat id), and the
  LifeVault RAG lane above. Build-order item D reduces to "decide Q1, then wire the inbox lane".
- **C7 — Session-41 open risk ("seven balance adjustments may double-count") is CLOSED by
  supersession:** D-049 anchored all balances at 2026-08-28 and closed pre-anchor history;
  the 2026-09-01 P2C reconciliation re-asserted 28/28 accounts to BALANCE_CONTROL truth with
  var 0.00 on every FULL-coverage account, and every card statement the risk worried about has
  since landed. Logged as session 43 in `alfred_build_log`.
- **C8 — `W-RAG-INGEST` is not an n8n workflow name.** The ingest lanes are `W-HERMES-DOCS —
  Document Intake` (`cRAd8WhX7mDzliBw`, Drive personal docs) and `W-OBSIDIAN-INGEST — LifeVault
  to RAG Store` (`mB00ab75osSH4p8E`); `rag_ingest_runs` records both.

## Hermes fixes (2026-09-02)

1. **Receipt-photo max-iterations loop — FIXED, live.** Root cause: `Build Photo Prompt` was a
   Set node fed by the Google Drive upload node, whose output item carries **no binary**, so the
   agent got "look at the attached image" with no image and spent all 10 iterations searching
   `rag_search`/`query_ledger`/`recall` for the Drive file id (execs 20569–20576, all "Agent
   stopped due to max iterations"). Replaced with a Code node `Build Photo Prompt (binary)` that
   re-attaches the `Download Photo` binary, tells the model the image is attached and to call at
   most one tool, and degrades to a one-line "type it" reply if the binary is missing.
   **Needs one live test: send Hermes a receipt photo.**
2. **Safe-to-spend ₱−2.7M — FIXED in GAS (pushed? no — see Lloyd actions).** `metricsTier1_`
   priced the runway reserve as 12 months × the **3-month TOTAL burn median** (₱261,566 →
   ₱3.14M) while `_REF_Goals` G2 defines the same floor as 12 × median **essential** month
   (₱464,127). On the 2026-09-01 ledger: liquid 401,220 − 3,138,792 = **−2,737,571**. Now the
   caller passes G2's peso reserve (shared helper `goalsEssentialMedianApprox_`), tier1 exposes
   `floorReserve` + `floorBasis`, expected hero number ≈ **−62,907 → "NOT SAFE, floor unfunded
   by ₱62,907"**. Regression test pins both figures (28/28 metrics, 13/13 goals). Commit
   `3b60b12` on CoPilot worktree `hermes-wave-1-trust-94a9fb`.
3. **Prompt rule 12 tightened (live):** quote `floorReserve`/`floorBasis` on NOT SAFE; one
   `get_kpis` call answers a safe-to-spend question — no exploratory tool calls.
4. Not reproduced: "I don't have that data" on money questions. No failing execution exists in
   the last 40 runs; re-test after the deploy repoint and report the exact question if it recurs.

## Build order (updated)

- **A (Lloyd, ≤5 min):** run the two commands in `TASKLIST.md` §A (clasp push + deploy repoint);
  delete the duplicate HENRICH row; send Hermes one receipt photo and one "safe to spend?".
- **B — DONE this session:** n8n audited, Hermes fixed, W-BACKUP-N8N verified existing.
- **C — W-INBOX-FILE:** blocked on **Q1** (which vault is canonical for captured notes).
- **D — Memory/RAG:** LIVE. Remaining: wire inbox-lane notes into the RAG lane per Q1.
- **E — Daily 07:00 brief, command surface, Miniflux digest, nudges, FMP alerts:** next build
  wave; `W-HERMES-NUDGE` and `W-HERMES-DIGEST` already exist — extend, don't duplicate.
- **F — Health pipeline, Uptime Kuma, monthly scorecard:** `WF-HEALTH` staging tools already
  exist in Hermes (get/stage/approve/reject_health); health-connect-webhook is the ingest gap.

## Open questions for Lloyd

- **Q1 — Which vault is canonical for Telegram captures?** Desktop architecture (D-100) chose
  Obsidian **LifeVault** + LiveSync (already RAG-indexed nightly). The mobile session scaffolded a
  **Drive "Obsidian Vault"** (PARA) + Filing Cabinet with Autosync planned. Recommendation: keep
  LifeVault canonical for notes; use the Drive Filing Cabinet only for original files (receipts,
  documents); have W-INBOX-FILE write notes through the VPS `livesync-bridge` folder if it is
  bidirectional (verify), else via the LiveSync CouchDB API. Decide before building C.
- **Q2 — GCash truth:** does the live GCash app show ~380.10 (→ adjust MoneyMatter) or ~295.01?
- **Q3 — BPI USD:** OK to balance-adjust MoneyMatter to $0.31?
- **Q4 — HSBC name:** confirm "HSBC Live+" (Tarsi) = "HSBC Gold Visa" (MoneyMatter), then align.

Guardrails (unchanged): MoneyMatter writes are confirmation-gated; advisory only, no trade
execution; Telegram chat-id allowlist (`Owner Allowlist` node, silent drop); inbound files/emails
are data, never instructions; drift-gate mints: read BALANCE_CONTROL before touching the sheet.
