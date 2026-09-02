# Hermes Ecosystem — Task List (2026-09-02, session 43)

Status words: DONE · LIVE · BLOCKED(who/what) · NEXT · LATER.

## A — Lloyd's one-tap actions (≈5 min total)

0. **PowerShell users:** run each block below as its own command (`&&` is not valid in
   Windows PowerShell 5.1). First push + deploy were done → **@26**. A SECOND push + deploy is
   needed for the cache-fingerprint + essential-prefix follow-up (commit on the Hermes
   worktree after `3b60b12`); same commands, new description `v27 session-43b`.
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

2. ~~Delete the duplicate HENRICH row~~ **DONE 2026-09-02** (Lloyd approved in chat; agent deleted
   `01a05bc6-d3e3-763a-a138-2467f9bf7126`; Cash verified 6,036 via `get_accounts`).
3. **Test Hermes:** ~~/note~~ **DONE** (📥 FILED card, note in `00 Inbox`, exec 20779);
   ~~safe to spend?~~ **DONE 2026-09-02 10:39** — second deploy live (`v3` fingerprint,
   `floorReserve` 863,600.88): reply "🔴 NOT SAFE · reserve ₱863,601 unfunded by ₱462,380 ·
   liquid ₱401,220 · 1.87 months · G2 basis 12 × ₱71,967", 20 s, exec 20780.
   ~~receipt photo~~ **DONE 2026-09-02 11:14** — binary reached the model (104 kB jpg), parsed
   "−₱2,702 | SOCOTECO II | 2026-08-28 | U1 Utilities" and staged in 25 s (exec 20789, was
   80 s + max-iterations); inbox lane filed "Electric Utility Bill Paid" (exec 20790, 5.7 s);
   "ok. paid via bpi main" → APPROVED (exec 20793). All three Hermes paths verified live.
   **Found in the trace:** HERMES_STAGING got TWO rows for the one receipt (keys
   `tg-307-SOCOTECO` / Cash and `tg-00000307` / BPI Main) — the agent invented idempotency
   keys and re-staged on the correction instead of approving. Prompt rule 6 tightened live
   (exact key, approve-don't-restage). Lloyd: delete the stale `tg-307-SOCOTECO` row (Cash)
   in HERMES_STAGING before the next `hermesStagingApply`. Consider a server-side guard in
   `96_HermesStaging.js`: reject keys that don't match `^tg-\d+$`.
4. ~~Approve the BPI USD adjustment~~ **DONE 2026-09-02 10:48** — adjusted 3.00 → 0.31
   (txn `01a06004-2647-713d-91ce-c92b9da4f2b0`). GCash DONE (380.10). All 29 MoneyMatter
   accounts now match BALANCE_CONTROL truth on every evidenced leg.
5. Q1 is resolved for v1 (Drive vault, D-109) — say so if you want LifeVault instead. Still
   open: Q4 HSBC name. Install Autosync for Google Drive so captures reach the phone vault.

## B — Audit & fixes — DONE (session 43)

- [x] **Release CI red on every push** (fork has no signing secrets; `Sign Fdroid Variant APKs`
      failed after a green build). Fixed in `25691d6`: docs-only pushes no longer trigger it,
      signing/scan/publish steps gated on `SIGNING_KEY`, unsigned APK uploaded instead.

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

## C — W-INBOX-FILE — BUILT v1 (session 43), awaiting live test

- [x] Vault for captures: Drive "Obsidian Vault" for v1 (D-109); LifeVault stays the journal.
- [x] `W-INBOX-FILE — Telegram Capture to Vault` (`EDfBh8vqrQjthY7C`): Execute-Workflow
      trigger → classify (Gemini 3.5 flash, text/caption only) → note with frontmatter →
      Drive `createFromText` into `00 Inbox` (journal → `Journal/2026`) → 📥 card; failure card.
- [x] Wired in W-HERMES: `Is Inbox Note` (`/note …`) → `File Note to Inbox`; `Upload Photo
      Evidence` → `File Photo to Inbox`; `Upload Document Evidence` → `File Document to Inbox`
      (all `waitForSubWorkflow: false`, parallel to the receipt/PDF lanes).
- [ ] Live test (Lloyd, §A step 3). Then: voice notes (transcribe via Gemini audio), inline
      OK/Undo buttons (needs the Telegram Trigger to also listen to `callback_query`), undo =
      trash the Drive note, and RAG indexing of `00 Inbox` (extend `W-HERMES-DOCS` folder
      watch or add a second Drive trigger).
- [ ] If LifeVault becomes the target: verify `livesync-bridge` bidirectionality on the VPS,
      then re-point the folder map in `Build Note`.

## D — Memory & RAG — LIVE

- [x] `remember/recall/forget` (doc_class=memory), Chat Memory 20 turns.
- [x] LifeVault → RAG nightly (`W-OBSIDIAN-INGEST`), `rag_search` journal recall proven.
- [ ] After C: inbox-lane notes indexed the next night (no new tables).

## E — Proactive layer — IN PROGRESS

- [x] `W-DAILY-BRIEF — 07:00 Morning Brief` (`Cu6opCPfQPHJMKRJ`) **LIVE 2026-09-02**: schedule
      07:00 PHT + owner hook `POST /webhook/daily-brief-run`; get_kpis (safe-to-spend on the G2
      basis, floor reserve, runway, liquid, burn + top drivers, budget left, recurring due ≤7 d,
      kept, next income) + Google Calendar today → Telegram HTML. First brief sent (msg 304,
      exec 20787). Dead sources are labelled, never fatal.
- [x] `/spend` `/networth` `/runway` → deterministic /stats card (W-HERMES `Is Stats Command`);
      `/spend` verified live 2026-09-02 11:04 (card in seconds, no LLM call).
- [ ] Stats-card budget rows look implausible (B3 Household ₱74, E4 ₱82 "6-mo medians") —
      the `budgetOutlook` enrichment needs a look once categories are cleaned.
- [ ] Brief v2: TickTick open tasks (needs a TickTick credential/token in n8n), MoneyMatter
      subscriptions (needs the OAuth client W-DASH-SYNC uses), yesterday's spend, news top 5.
- [ ] Data-quality flag from the first brief: top burn driver is **"G2 Review / Uncategorized"
      ₱112k/mo** — the category worksheet backlog is now the biggest distortion in every KPI.
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
