# Hermes Ecosystem — Task List (2026-09-02, session 43)

Status words: DONE · LIVE · BLOCKED(who/what) · NEXT · LATER.

## A — Lloyd's one-tap actions (≈5 min total)

1. **Push the GAS fix and repoint the web app** (agent is classifier-blocked on both). From any
   terminal on the desktop:

   ```bash
   cd "C:/Users/Lloyd/AppData/Local/Temp/claude/C--Users-Lloyd-Claude-Projects-CoPilot--claude-worktrees-alfred-vault-handoff-958845/cef8667b-3683-4d37-a88f-3713d73b003b/scratchpad/gas-pull" && clasp push -f
   ```

   (If that scratch folder is gone, do a fresh `clasp pull` into any empty folder with
   `.clasp.json` = `{"scriptId":"1kGk9s94z2R1EbyoLpPuVEqQG3Nqjtx4FivZk7hlOidlr7KPP6mWT4L4F"}`,
   copy `scripts/alex/93_Metrics.js` and `94_RefGoals.js` from CoPilot worktree
   `hermes-wave-1-trust-94a9fb` (commit `3b60b12`) over it, then `clasp push -f`.)

   ```bash
   clasp deploy -i AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ -d "v25 session-43: safe-to-spend G2 basis + modules 128-141"
   ```

2. **Delete the duplicate HENRICH row in MoneyMatter** (Cash, 2026-08-24, ₱410, the one WITHOUT
   an `[alex:…]` tag — id `01a05bc6-d3e3-763a-a138-2467f9bf7126`). Cash then reads 6,036 = truth.
3. **Test Hermes:** send one receipt photo (expect a one-line parsed row, no loop), then ask
   "safe to spend?" (after step 1: expect `NOT SAFE`, floor unfunded by ≈₱62,907, quoting
   `floorReserve` 464,127 — not −₱2.7M).
4. Answer Q1–Q4 in `README.md` (vault choice, GCash app balance, BPI USD adjust, HSBC name).
5. Optional: install Autosync for Google Drive (only if Q1 keeps the Drive vault).

## B — Audit & fixes — DONE (session 43)

- [x] n8n instance audited: 19 workflows, Hermes config read, allowlist verified.
- [x] W-BACKUP-N8N exists (weekly) — mobile draft marked superseded.
- [x] Backup-filename glob: no defect (phone vs sheet export naming is by design).
- [x] Hermes receipt-photo loop fixed (binary re-attached) — live.
- [x] Hermes safe-to-spend reserve fixed (G2 basis) — code + tests; live after A1.
- [x] Hermes prompt rule 12 tightened — live.
- [x] Drift gate hardened (D-106) and junk −641k GCash mint deleted — 2026-09-02 morning.
- [x] Tarsi phone re-import confirmed.
- [x] Session-41 double-count risk closed by supersession (logged session 43).
- [x] Variance forensics re-based on real MoneyMatter data (README C3–C5).

## C — W-INBOX-FILE — BLOCKED (Lloyd: Q1)

- [ ] Decide canonical vault for captures (LifeVault recommended).
- [ ] Verify `livesync-bridge` folder is bidirectional (VPS `/opt/lifevault/files`) — if yes,
      n8n writes notes there; if no, use CouchDB `_bulk_docs` via the LiveSync API.
- [ ] Build `W-INBOX-FILE` as a sub-workflow called from `Capture Type Switch` (photo/document
      already downloaded + evidence-uploaded upstream — do not re-download).
- [ ] `/note <text>` and voice → transcript → note; confirm/undo inline buttons handled in the
      Hermes callback branch.
- [ ] Receipt captures continue to go through `stage_expense` (unchanged).

## D — Memory & RAG — LIVE

- [x] `remember/recall/forget` (doc_class=memory), Chat Memory 20 turns.
- [x] LifeVault → RAG nightly (`W-OBSIDIAN-INGEST`), `rag_search` journal recall proven.
- [ ] After C: inbox-lane notes indexed the next night (no new tables).

## E — Proactive layer — NEXT wave

- [ ] `W-DAILY-BRIEF` 07:00 PHT: `get_kpis` (safe-to-spend, balances), subscriptions due ≤7 d
      (MoneyMatter `get_upcoming_subscription_payments`), Calendar today, TickTick open tasks,
      yesterday's spend (`spend_series`), news top 5 (after F9). Reuse `W-HERMES-DIGEST` format.
- [ ] Commands: `/brief /spend /log /note /remind /todo /sub /networth /goals /cascade`
      (`/stats`, `/report` exist; route new ones in `Is Stats Command`-style IF nodes before the
      agent, one tool call each).
- [ ] Miniflux on the Pi (`miniflux-homelab.md`), then digest workflow (templates #6011/#7627).
- [ ] Savings nudges: extend `W-HERMES-NUDGE` with MoneyMatter budget stats; paycheck-detection
      snapshot pattern from hail2victors/n8n-Actual-Automation.
- [ ] FMP price/breakout alerts (watchlist tab in the Alfred sheet; template #7701 pattern).
- [ ] Remove the P2C "island" webhooks from `TEST - OmniRoute gateway` after a quiet week.

## F — LATER

- [ ] Health Connect webhook → n8n → `stage_health` (tools exist).
- [ ] Uptime Kuma on the Pi (n8n, MoneyMatter, HA → Telegram).
- [ ] Monthly scorecard; Sunday briefing.
- [ ] Wire real `essential_prefixes` (A1,A2,B1,B2,B3,C1,C2) into `goalsEssentialMedianApprox_`
      (replaces the 0.9 approximation for the G2 floor).
- [ ] Upstream account-feed fix so W-DASH-SYNC stops carrying the phantom −641k GCash line
      (prefer statement/tarsi over COMPUTED_PARTIAL).
- [ ] Carried Alex items: 30 blank category rows; sheet script cleanup; HSBC September statement
      refresh; small-account confirmations; sheet link-sharing decision.
