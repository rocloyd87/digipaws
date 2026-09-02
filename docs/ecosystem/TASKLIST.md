# Hermes Ecosystem — Task List (2026-09-02 night, session 45)

Status words: DONE · LIVE · BLOCKED(who/what) · NEXT · LATER.

## A — Lloyd's one-tap actions (≈15 min total, session 45)

**CORRECTION (session 45, verified on the machine):** the session-44 "GROUNDING FAILURE" did not
happen as recorded. No W-HERMES execution ever sent "staged tg-00000312, awaiting approval". The
real trace (execs 20825 / 20841 / 20853, `sessions/2026-09-02-session-45.md` §1): the live GAS
validator refused `tg-312` as "8+ chars"; the bare-key message went to the LLM, which made ten
exploratory calls (three `rag_search` with an empty query → exec 20847 / W-ERR) and replied "I don't
have the data for that capture"; `approve_staged` then correctly said NOT FOUND. All four real
defects are fixed in code (GAS, push pending) and live (n8n); the Grounding Guard v2 now also closes
the claimed-write gap for real.

1. ~~Push + deploy GAS v28~~ **DONE 2026-09-03 04:20 PHT (@28).** Verified live over `/exec`:
   `list_staged` works, the date guard refuses a 2024 date without writing, the key rule refuses
   `tg-photo-999`, and `reject_staged` discarded `tg-307-SOCOTECO`, `tg-287-receipt`,
   `tg-00000284`, `tg-00000285` (the last two were 2026-09-01 test captures dated 2023/2024).
   Pending now: only `tg-jollibee-20260822`. **Decide:** `tg-jollibee-150` (Cash −150, 2026-08-22,
   the Aug-26 self-test) is APPROVED and will append to Alex on the next `hermesStagingApply` —
   reply `reject tg-jollibee-150`... no: that key is not a tg-number, so use the curl in step 3 with
   `"tool":"reject_staged"` and `"idempotency_key":"tg-jollibee-150"` if it was never a real meal.

1b. **Push + deploy v29** (two follow-ups found while verifying @28; same pull dir, `96` and `93`
   already copied over it):
   - `1435aed` — approve/reject skip rejected rows, so the discarded `tg-307-SOCOTECO` can never
     shadow the approved `tg-00000307` when you say `approve tg-307`.
   - KPI cache fingerprint bumped so `get_kpis` stops serving the pre-v28 cached shape (it still
     lacks `monthPace` / `recurringDue30d` on @28 — the savings nudges are inert until this ships).

   ```powershell
   cd "C:/Users/Lloyd/AppData/Local/Temp/claude/C--Users-Lloyd-Claude-Projects-CoPilot--claude-worktrees-hermes-ecosystem-s45-34431f/00af9446-d1e8-46d6-82c7-fd1c9ecaff25/scratchpad/gas-pull"; clasp push -f
   ```

   ```powershell
   clasp deploy -i AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ -d "v29 session-45b: approve/reject skip rejected rows; KPI cache fingerprint v4"
   ```

2. **Telegram, after the deploy** (each one is a deterministic route, no LLM):
   - `/pending` → 📋 PENDING card (only `tg-jollibee-20260822` left; the stale rows were discarded
     from the desktop).
   - `/sub` → 🔁 SUBSCRIPTIONS card from the ledger detector.
   - `/remind test ping in 5m` → ⏰ REMINDER SET + event on rocloyd87@gmail.com (delete it after).
   - **One fresh receipt photo** → card must quote `tg-<that message id>`; then `approve tg-<id>`
     → ✅ APPROVED. If the date on the receipt is misread, the card asks "Is the date … right?"
     instead of staging. Report the exec ids.
3. **Chowking receipt by hand** (−564 · Chowking Gaisano Mall Bajada · 2026-09-02 · D2). Name the
   card account first, then run (replace `ACCOUNT`):

   ```bash
   curl -sS -X POST "https://script.google.com/macros/s/AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ/exec" -H "Content-Type: application/json" -d "{\"key\":\"<HERMES_SHARED_SECRET>\",\"tool\":\"stage_expense\",\"params\":{\"amount\":\"-564\",\"date\":\"2026-09-02\",\"payee\":\"Chowking Gaisano Mall Bajada\",\"category\":\"D2\",\"account\":\"ACCOUNT\",\"raw_input\":\"Chowking SI#08091072 09/02/2026 total due 564.00 Mastercard/Visa\",\"idempotency_key\":\"tg-312\",\"evidence_url\":\"https://drive.google.com/file/d/1VYYLqRpVGMUC7UFhjb2sQz243jRWdUdD/view?usp=drivesdk\"}}"
   ```

   Then in Telegram: `approve tg-312`. (The secret is the W-HERMES Config `gas_key`.)
4. **Category rulings accept-all** (Apps Script editor, Run in this order; each logs a count):
   `catAcceptAutofill` (135_CatAccept.js) → `categoryRulingsPreview` → `categoryRulingsApply`
   (134_CategoryRulings.js) → `catAcceptPairApply` (135). Note: 135 encodes the 2026-09-01
   worksheet, and many of its decisions keep rows in "G2 Review / Uncategorized" — it clears the
   backlog of *undecided* rows; a second pass on the G2 bucket itself is still needed to move the
   ₱112k/mo out of G2.
5. **Credentials that unblock the remaining drafts** (n8n → Credentials):
   - `TickTick OAuth2 - rocloyd87` (generic OAuth2 API; steps in `drafts/W-DAILY-BRIEF-v2.json`
     Read Me §1) → then `/todo` route + brief tasks section.
   - `FMP apikey` (Query Auth, parameter `apikey`) → select it on `FMP EOD Light` in
     **`W-FMP-ALERTS — Price Alerts` (`X2bAv2WXOOZJ3pP6`, imported INACTIVE in session 45)**; run
     GAS `hermesWatchlistEnsure()` (142_HermesWatchlist.js, in v28) once to create the
     `_HERMES_WATCHLIST` tab with formulas + four seed rows; test-run; activate.
   - `Airbnb iCal - Cascade` (Query Auth, parameter `s`) → then `/cascade` route.
6. ~~MoneyMatter detect subscriptions~~ **RAN 2026-09-02 (session 45, under Lloyd's /goal
   authorization):** `detect_subscription_candidates` → **0 candidates** (fresh run, not cached).
   MoneyMatter's detector finds no recurring pattern in the mirrored rows, so `/sub` and the
   brief's subscriptions section stay ledger-only until subscriptions are created by hand.
7. Q4 HSBC name ("HSBC Live+" = "HSBC Gold Visa"?) is still open.

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
- [x] Brief v2 partial (session 45): **"💸 YESTERDAY" spliced live** into `Cu6opCPfQPHJMKRJ`
      (`Fetch Spend Series` → `Format Brief`), validated; first real run 07:00 2026-09-03. TickTick
      tasks + MoneyMatter subscriptions sections still wait for the credential / seeded subscriptions.
- [ ] Brief v2 remainder: **DRAFTED (mobile s44)** → `drafts/W-DAILY-BRIEF-v2.json`: TickTick
      open tasks (credential setup steps in the sticky), MoneyMatter subscriptions ≤7 d (connector
      read 2026-09-02: **0 subscriptions defined** — section self-hides until seeded), yesterday's
      spend via `spend_series` vs 30-day average. Desktop: import, test, splice into
      `Cu6opCPfQPHJMKRJ`. News top 5 waits for Miniflux.
- [ ] Data-quality flag from the first brief: top burn driver is **"G2 Review / Uncategorized"
      ₱112k/mo** — the category worksheet backlog is now the biggest distortion in every KPI.
- [ ] Commands: `/brief /spend /log /note /remind /todo /sub /networth /goals /cascade`
      (`/stats`, `/report` exist; route new ones in `Is Stats Command`-style IF nodes before the
      agent, one tool call each). **Session 45: `/sub` and `/remind` BUILT live** in W-HERMES
      (validated, awaiting one Telegram test each); `approve|reject tg-N`, bare `tg-N`, `/pending`
      also deterministic now. **`/todo` and `/cascade` NOT built** — need the TickTick OAuth2 and
      Airbnb iCal credentials (§A 5). Draft: `drafts/COMMANDS.md`.
- [ ] Miniflux on the Pi (`miniflux-homelab.md`), then digest workflow (templates #6011/#7627).
      **Feed URLs VERIFIED 2026-09-02 (session 45, desktop probe):** all six primaries + Google News
      are live RSS; two fallbacks 403. Table in `miniflux-homelab.md`. `W-FEED-PROBE.json` was not
      imported (probe ran from the desktop instead).
- [x] Savings nudges **CODED (session 45, `ad00d50`, live after v28):** `hermesNudgeSavingsRules_`
      N1–N5 in `98_HermesNudges.js` with 12/12 tests, `get_kpis.monthPace` + `recurringDue30d`
      in `93_Metrics.js`; `W-HERMES-NUDGE` schedule now 12:30 + 19:30 PHT. First N4 run only seeds
      the milestone state. Draft: `drafts/NUDGE-SAVINGS.md`.
- [ ] FMP price/breakout alerts (watchlist tab in the Alfred sheet; template #7701 pattern).
      **IMPORTED INACTIVE (session 45) as `W-FMP-ALERTS — Price Alerts` `X2bAv2WXOOZJ3pP6`**, with
      the GSheets + Telegram credentials set; needs the FMP apikey credential and one
      `hermesWatchlistEnsure()` run (§A 5) before activation. Draft: `drafts/W-FMP-ALERTS.json`. Constraint found: FMP `quote`
      endpoints are Premium-gated on this plan and PSE tickers are not on FMP, so the draft is
      end-of-day (FMP EOD light for US, GOOGLEFINANCE columns for PSE).
- [ ] Remove the P2C "island" webhooks from `TEST - OmniRoute gateway` after a quiet week
      (created 2026-08-31 → earliest 2026-09-07; untouched in session 45).

## F — LATER

- [ ] Health Connect webhook → n8n → `stage_health` (tools exist).
- [ ] Uptime Kuma on the Pi (n8n, MoneyMatter, HA → Telegram).
- [ ] Monthly scorecard; Sunday briefing.
- [x] ~~Wire real `essential_prefixes`~~ **DONE 2026-09-02 (`37bf844`):** `metricsKpis` uses
      `metricsEssentialMedian_` over `GOAL_CONTRACT.essential_prefixes`; the 0.9 approximation is
      only the fallback when no essential-coded rows exist.
- [x] Upstream account-feed fix **CODED (session 45, `ad00d50`, live after v28):**
      `dashSyncAccountsRead_` prefers `tarsi_balance` unless `coverage === 'FULL'`; the phantom
      −641k GCash line disappears from the 02:00 report the first night after the deploy (verify).
- [ ] Carried Alex items: 30 blank category rows; sheet script cleanup; HSBC September statement
      refresh; small-account confirmations; sheet link-sharing decision.
