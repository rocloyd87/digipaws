# Hermes Ecosystem — Task List (2026-09-05 night, session 55)

Status words: DONE · LIVE · BLOCKED(who/what) · NEXT · LATER.

**SESSION 56 part 2 (2026-09-06 ~05:45, GAS @76 unchanged) - morning-after, first three checkpoints:** (`sessions/2026-09-06-session-56.md`)
- 03:30 guardian PASS (humanized, nothing critical). 03:00 weekly backup FAILED: published version still had the
  pre-rotation GAS key (draft had the fix) -> published, hardened (retry, timeouts, verdict fallback), D-135.
  03:40 verifier FAILED: gviz read broke when the sheet went Restricted -> Drive OAuth credential, re-run clean (21741).
- Persona-menus PRD presented (artifact a92079fe...), nothing built; four decisions are Lloyd's.
- **Yours:** answer the PRD's four questions; the rest of the 09-06 checkpoints are read after 20:05.

**SESSION 55 close (2026-09-05 20:50, GAS @75 -> @76) - what changed, what is yours:** (`sessions/2026-09-05-session-55.md`)
- **Balance gate PASSED** (20:28, var 0.00 on all 8 FULL). No ledger write. Real clock 20:27 at boot; every report
  checkpoint (09-06 03:30 / 07:00 / 11:06 / 12:30 / 18:00 / 20:00, 09-07 08:00) is STILL ahead - session 56 checks them.
- **D-133 PROVEN on the last two:** W-SENTINEL exec 21646 Run Checks `ok:true` (4/4 green, no message by design);
  W-API-MONITOR exec 21648 Read Usage `ok:true` (946 calls / 10 d, 94/day vs ceiling 500). Temp webhooks removed, 6 / 6.
- **FIXED live (D-134, GAS @76): the nudge's "1,648,653 below the runway floor".** Liquid (401,220.44) was correct -
  GCash enters at its 380.10 statement balance, the -641k computed figure is never read. The nudge priced its OWN floor
  at 12 x 0.9 x median total month (2,049,873) while get_kpis prices it on the G2 basis (12 x median essential month =
  976,726). 98_HermesNudges.js now takes `tier1.floorReserve`; live read-back says **"Liquid is P575,506 below the
  runway floor."** Harness 111/111; worktree `hermes-wave-1-trust-94a9fb` 2b40854 = live @76.
- **Heads-up:** my read-only `get_nudges` probe used the last two slots of this week's nudge cap (4/4), so Sunday's
  12:30 / 19:30 nudges may say `deferred`. Resets Monday. Not a defect.
- **Statements / Drive fallback / G2:** unchanged. Read-back identical to sessions 51-54 (5,347 rows, G2 22 = 309,457, name-only 7).
- **Yours (1 min):** `git -C D:\ObsidianVault push` after this session's vault commit.

**SESSION 54 close (2026-09-05 20:15, GAS @75 unchanged) - what changed, what is yours:** (`sessions/2026-09-05-session-54.md`)
- **Balance gate PASSED** (19:52, var 0.00 on all 8 FULL). No GAS change, no ledger write. Real clock 19:51 at boot;
  every report checkpoint is STILL ahead (09-06 03:30 / 07:00 / 11:06 / 18:00, 09-07 08:00) - session 55 checks them.
- **D-132 confirmed live:** refresh exec 21618 got an HTML page, no alert, no error execution since 21587.
- **FIXED live (D-133): five workflows carried a stale GAS shared secret** since the 08-30/31 rotation and reported
  SUCCESS while GAS answered `unauthorized`: W-HERMES-NUDGE (every 12:30/19:30 nudge silently empty since 08-31),
  W-HERMES-DIGEST (08-31 Monday digest crashed on empty text), W-SENTINEL, W-API-MONITOR, W-HERMES-ASSUMPTIONS. All
  five Config nodes now match W-HERMES. Verified: digest test sent you the humanized Monday card (message 380) and the
  nudge test sent a real 2-finding nudge (message 381) at 20:06. Sentinel (Sun 18:00) and API monitor (daily 20:00)
  prove themselves on their next runs.
- **FIXED live: W-HERMES-ASSUMPTIONS** - Gemini had retired `gemini-2.5-flash` (09-01 run failed); now on the brief's
  `gemini-3-flash-preview`. The test also exposed 4-x Gemini calls and raw-JSON cards on an all-OK month (the 19:58
  card you saw); now one call, the goal contract reaches the prompt, and a card goes out only on a `review` verdict,
  a quarter month, or an unreadable reply. Next real run 2026-10-01 09:00.
- **Check next session:** the nudge's "liquid ₱1,648,653 below the runway floor" figure (GCash PARTIAL may drag it).
- **Statements / Drive fallback / G2:** unchanged. Read-back identical to sessions 51-53.
- **Yours (1 min):** `git -C D:\ObsidianVault push` after this session's vault commit.

**SESSION 53 close (2026-09-05 16:25, GAS @75 unchanged) - what changed, what is yours:** (`sessions/2026-09-05-session-53.md`)
- **Balance gate PASSED** (16:05, var 0.00 on all 8 FULL). No GAS change, no ledger write.
- **Real clock:** it was 16:06 PHT at boot (n8n agrees), so the session-52 "16:40" stamp was ahead of the clock. Every
  report checkpoint is STILL ahead: 09-06 03:30 guardian, 07:00 brief (TASKS / NEWS), 11:06 no SUCCESS mail, 18:00 Sunday
  card + "Your week in money", 09-07 08:00 digest. Session 54 checks them for real.
- **FIXED live: W-SNAPSHOT-REFRESH failure alert (D-132).** GAS /exec answers ~5x/day with a Google HTML page; the alert
  put that HTML into a parse_mode=HTML Telegram message, so the alert node failed too (10 error execs 09-03..09-05, none
  reached you). `Fetch Failed` now skips the alert on a transient HTML page (harmless: live-GAS fallback) and escapes/
  truncates any real error. validateOnly -> apply -> active version read back 16:10. W-ERR was never attached, no mail noise.
- **Six off-worksheet G2 rows: DEFER by rule, no write, nothing to ask** (5 YNAB plugs + Batch Invitation 1,000 already
  ruled by 174). Worksheet mirror NOT done (needs a GAS append module + deploy for a cosmetic agreement) - LATER.
- **Statements:** none issued; correlator and held captures unchanged. Drive-fallback decision still ~09-12.
- **Read-back identical** to sessions 51-52 (5,347 rows, G2 22 = 309,457, name-only 7). W-S48-READ untouched (10 nodes).
- **Yours (1 min):** `git -C D:\ObsidianVault push` after this session's vault commit (classifier blocks the agent).
- **Next session:** the report checkpoints above; statements ~12-18 Sep; Drive-fallback ~09-12. NO owner questions on the
  DEFER worksheet.

**SESSION 52 close (2026-09-05 15:20, GAS @75 unchanged) - what changed, what is yours:** (`sessions/2026-09-05-session-52.md`)
- **Balance gate PASSED** (14:50, var 0.00 on all 8 FULL). No GAS change, no ledger write.
- **Reports: nothing has fired yet** - every session-51 schedule's first run is after this session. Today's
  `[Alfred Statements] SUCCESS` (11:06) and Gmail Guardian (07:53) mails pre-date the @75 deploy. First real
  checkpoints: 09-06 03:30 guardian, 07:00 brief (TASKS / NEWS), 11:06 no SUCCESS mail, 18:00 Sunday card +
  "Your week in money", 09-07 08:00 digest. Session 53 checks them.
- **Your taps were already done** (mount verified via docker inspect, sharing Restricted, Watchtower healthy,
  both repos at origin). Drive stays the capture fallback until ~09-12.
- **DEFER cluster 2 CLOSED as DEFER** under your 14:50 rule: the 8 YNAB "Unaccounted" plugs (128,367.86) have no
  same-amount correlate anywhere and no possible source. 16 worksheet rows remain DEFER; 6 other open G2 ledger
  rows never reached the worksheet - 5 are the same plug class (84,248.84 RCBC / 22,347 GCash / 19,733.08 Maya /
  2,263.32 BPI / 455 RCBC POS), 1 has a handle (see (d) below).
- **DEFER worksheet CLOSED - nothing left to ask (15:35):** cluster 3 was ALREADY RULED on 09-05 07:13 (174): Calong Calong
  23k = money Lloyd LENT, already repaid, note only on the row ("ctx: loaned to Calong Calong and Ann Rey Cerillo"), G2/DEFER,
  no balance effect (Lloyd 15:30: "answered numerous times - put only notes, not to affect the current balance"); Bancnet 807,
  POS 455, Batch Invitation 1,000 = DEFER in the same batch. Every DEFER row now has an owner ruling. DO NOT RE-ASK ANY OF THEM.
- **Polish done:** `/spend` caption now says "your typical month per category, from the months it appeared in over
  the last 6"; philstar was already out of Miniflux (10 feeds, 0 errors).
- **Vault backed up (16:30):** D:\ObsidianVault origin was a dead cascadereservations-del URL; now rocloyd87/Alfred branch
  `vault` (origin/vault = 9980455, push.default upstream). Atlas artifact republished (version "Session 52").
- **Next session:** the report checkpoints above (09-06 03:30 / 07:00 / 11:06 / 18:00, 09-07 08:00), the 6 off-worksheet G2
  plug rows (DEFER by rule, optional worksheet mirror), statements ~12-18 Sep, Drive-fallback decision ~09-12. NO owner
  questions on the DEFER worksheet.

**SESSION 51 close (2026-09-05 14:05, GAS @75) - what changed, what is yours:** (`sessions/2026-09-05-session-51.md`)
- **Balance gate PASSED** (08:24:04, var 0.00 on all 8 FULL; re-read after the 177 apply). Directives 1-4 DONE, 5 built and
  waiting on one mount, 6 waits for the September statements.
- **Reports (D-130 #1):** the two daily mails are gone - `[Alfred Statements] SUCCESS` no longer sends (32 records the run;
  a failure still mails) and the Gmail Guardian digest is Sunday-only (weekday mail only on failures). ONE weekly e-mail:
  W-SCORECARD Sunday 18:00 -> "Your week in money" (test received 13:27). Critical channel: W-ERR now also e-mails; NEW
  W-GUARDIAN `PFS3bVKzTwyJNmxm` 03:30 (kpi_guardian: balance gate, sync, ingest, budget vs burn, G2 share, blanks, stale
  statements; GET /webhook/guardian-run). Digest, nudges, brief, weekly/monthly cards, dash-sync report, verifier, watchdog
  and W-ERR all in the title / sentence / groups shape - each read back live.
- **Brief v2 (D-130 #2) LIVE:** TickTick tasks (overdue / today / soon), ledger subscriptions in DUE <= 7 d, NEWS TOP 5 from
  Miniflux via Gemini. **Miniflux (D-130 #3) LIVE on alfred-brain** (/opt/miniflux, 10 feeds, 571 entries, credential
  `Miniflux API - alfred-brain`). **budgetOutlook fixed** (present-month median, 3-of-6 gate; B3 273/mo gone; 165k = 79 % of
  burn). 128 -> 175 renamed; 177 filled the 5 leg-disagreement counters (name-only 12 -> 7); 0 blank-category spending rows exist.
- **Yours (about 6 min):**
  (1) ~~n8n LifeVault mount~~ **DONE 14:35** (mount + `N8N_RESTRICT_FILE_ACCESS_TO=/lifevault` in the override; `/note lifevault
  test 2` landed in `/opt/lifevault/files/00-inbox` and CouchDB within 1 s). Drive fallback until ~2026-09-12.
  (2) ~~Drive folder Restricted~~ **DONE 14:28 (agent, in-app browser):** the anyone-link lived on the root folder `Alfred
  Shared State` (which also holds statement_sync and alfred_doc_passwords.md); set to Restricted, Drive API now lists only
  the owner on both the folder and the sheet.
  (3) ~~Watchtower env~~ **DONE 14:33 (Lloyd, ssh):** `DOCKER_API_VERSION=1.44` added to the Portainer stack JSON
  (`/var/lib/docker/volumes/infra_portainer_data/_data/compose/3/docker-compose.yml`, via `ssh --% alfred "python3 -c ..."`
  because PowerShell mangles sed quoting); container Up, first run scheduled 2026-09-06 04:00 PHT.
  (4) Optional: Zero Trust tunnel route miniflux.rocloyd.com -> http://localhost:8085 (admin `lloyd`, password in
  /opt/miniflux/.env on the VPS).
  (5) `git -C C:\Users\Lloyd\Claude\Projects\CoPilot\.claude\worktrees\hermes-wave-1-trust-94a9fb push` (b22a4d7) and
  `git -C C:\Users\Lloyd\Claude\Projects\digipaws push origin kt-rewrite`.
- **DEFER cluster 1 - CLOSED as DEFER (Lloyd 14:50: "if no further context or correlation found, then defer"):** the five
  BPI Main `Payment to Merchant` debits (2025-10-13 3,633.75 · 2025-10-30 4,381.75 · 2025-12-01 1,517.90 · 2025-12-26
  2,053.00 · 2026-05-29 5,165.70). Evidence pass: no same-amount row on any account within 45 d, no order / bank e-mail
  source, no rule pattern; the label is BPI's generic app bills/merchant payment. Stay DEFER; do not re-ask. Cluster 2 next
  session: the YNAB "Unaccounted" plugs (9 rows, ~230k) - same rule applies unless a source appears.
- **Next session:** watch the 03:30 guardian and the Sunday 18:00 mail; once the September statements land (~12-18 Sep) the
  06:30 correlator consumes the confirmation legs and 145 the 4 held captures; then the HSBC September refresh.

**SESSION 50 final - Lloyd's directives (2026-09-05 12:45) - the session-51 work order:** (full prompt in `CONTINUE.md`)
1. **Reports:** one weekly e-mail summary instead of several per morning; same-day e-mail only for critical
   items (gate failure, failed import, W-ERR). **Humanize every report** (Telegram + e-mail): title, blank
   line, one sentence of context, then figures; blank lines between groups; ADHD-friendly, lead with what
   changed and what to do. Optimize the statement -> correlator -> 145 pipeline where slow or noisy.
2. **Brief v2:** add TickTick open tasks + Subscriptions sections to the 07:00 brief in the humanized shape.
3. **Miniflux on the VPS (not the Pi)**, then the news digest. **KPI data quality:** fix the budgetOutlook
   basis (B3 74/mo, E4 82/mo), add nightly data guardians/verifiers reporting through the critical channel.
   Owner context still needed: only the 22 DEFER rows, one cluster at a time in chat.
4. **Housekeeping, authorized:** 30 blank-category rows via 144/146 -> 130; the 4 name-vs-leg counters take
   the leg's account, the 8 institution-counters stay reported; rename `128_Own28SignFix` -> `175_`;
   small-account confirmations (list the evidence needed); sheet sharing -> Restricted.
5. **LifeVault as the capture target:** verify livesync-bridge both ways on the VPS, re-point W-INBOX-FILE
   and the RAG roots, keep Drive as fallback for a week.
Deferred (Lloyd): Uptime Kuma on the Pi, Huawei Health ingest.

**SESSION 50 close (2026-09-05 11:50, GAS @73 unchanged) - what changed, what is yours:** (`sessions/2026-09-05-session-50.md`)
- **Balance gate PASSED** (08:24:04, var 0.00 on all 8 FULL accounts). Correlator: no run since 06:31; nothing to retract.
  Worksheet 0 pending / 22 deferred; 12 name-only counters left as reported. September statements not issued yet.
- **Stale list corrected:** the awaiting_statement matcher (145) was already LIVE (ran 01:33 today; 3 captures held);
  FMP credential + price alerts already exist (`W-PRICE-ALERTS` active). HSBC ruling recorded: **Gold Visa = old
  cancelled card, Live+ replaced it** (vault FACTS + memory). The Jul-21 2,000 is one correctly paired transfer in Alex.
- **Capture lane v2 BUILT (n8n, validated):** voice notes (Gemini transcribe -> same text chain), inline OK/Undo buttons
  on W-INBOX-FILE cards, undo handler (Drive trash) in W-HERMES, `/todo` and `/cascade` routes, W-RAG-INGEST re-activated
  over `00 Inbox` + `Journal/2026`. W-HERMES 91 -> 112 nodes.
- **Later items:** `W-HEALTH-INGEST` (`sS5kebHIWMO3p41s`) and `W-SCORECARD` (`b6Qno1SMLMHG8tvj`) ACTIVE; P2C island
  webhooks removed (33 nodes); Uptime Kuma on the Pi DEFERRED (Lloyd); script cleanup inventoried (only the duplicate
  `128_` prefix; nothing removed).
- **Yours (about 8 min, one tap each):** (1) TickTick: developer.ticktick.com -> new app -> paste client id/secret into
  n8n credential `TickTick OAuth2 - rocloyd87` -> Connect -> send `/todo test task #ecosystem`. (2) Airbnb: Calendar ->
  Export calendar -> put the `s=` token into credential `Airbnb iCal - Cascade` and the listing id into W-HERMES node
  `Fetch Airbnb iCal` -> send `/cascade`. (3) Send Hermes a voice note. (4) Send `/note test` and tap **Undo**.
  (5) Point a phone exporter at `POST https://n8n.rocloyd.com/webhook/health-connect` with header `X-HC-Token`
  (value: W-HEALTH-INGEST Config). (6) `approve tg-333` (Cebu Pacific 336) if real.
- **Next session:** watch the 06:30 correlator once the BPI / HSBC / UB September statements land (~12-18 Sep) - the
  9+ confirmation legs and the 3 `awaiting_statement` captures should be consumed; HSBC September refresh then.

**SESSION 49 close (2026-09-05 07:00, GAS @70) - what changed, what is yours:** (`sessions/2026-09-05-session-49.md`)
- **Balance gate PASSED twice** (06:21:50 and 06:49:58 after the write: var 0.00 on all 8 FULL accounts).
- **Correlator audit:** the three 2026-09-04 decisions read back correctly by id; the retracted self-counter
  now carries C2 from the buy-load rule; the 06:31 run logged 0 decisions. Found + fixed: the live run
  dropped cardMatch/dayWindow/verdictFrom, so Amazon card matching had never run live (@69).
- **173 counter-id backfill APPLIED:** 29 name-only legs now carry the paired leg's account id (BPI Main
  Account), snapshot 20260905064146; 41 -> 12 name-only rows left, all 12 reported by design (4 name-vs-leg
  disagreements, 8 expense/income institution-counters).
- **159 split orders:** one order = two same-card AMAZON charges summing to the total (1.5 % tolerance);
  unmatched reason quotes the set window (-3..+10d). Preview on @70: 0/0, no ambiguity.
- **Rulings APPLIED 07:13 (174, @71-@73):** keyboard -> E1 (gift), Calong 23k noted as loaned out (G2,
  DEFER), Bancnet / POS / Batch DEFER. Worksheet 0 pending / 22 deferred. 130 now clears a stale sub.
- **Yours (optional):** the 12 reported counters if you want them paired.
- **Next session:** watch the 06:30 run consume the confirmation legs once statements land.

## A — Lloyd's one-tap actions (≈15 min total, session 45)

**SESSION 48 close (2026-09-05 06:00) - additions:**
- **Amazon feed complete:** 16 mails forwarded, 20 orders in `Amazon_Orders`; 170 re-ruled 11 Amazon
  rows from order evidence (keyboard -> D2 Gadgets, ship-to Ma -> B2 Family Meds, 2025 rows -> B2/D2
  Clothing). Say "E1" if the Marifel keyboard was a family gift.
- **Cluster 4 done:** Bills Payment -> C1; five Payment-to-Merchant DEFER. G2 open 22; worksheet 4
  pending: Calong Calong 23k (siblings say debt), Bancnet P2M 807, POS Debit 455, Batch Invitation 1,000.
- **Yours:** `git push` on `hermes-wave-1-trust-94a9fb`; make `cascadereservations-del/CoPilot` private.
- **Next session:** 133-style backfill of the 43 name-only counter rows; correlator sum-of-charges
  match for split Amazon orders + fix the misleading unmatched reason; reconcile 78 vs its generator.

**SESSION 48 (2026-09-04 evening) — what changed, what is yours:** (`sessions/2026-09-04-session-48.md`)

- **Balance gate PASSED** (20:31 alexBalanceScheduled: var 0.00 on all 8 FULL accounts). No restore.
- **G2 label cleared on 114 non-spending rows** (156, runner 157, v48 @51, snapshot `20260904204420`):
  93 paired counter legs + 21 balance_adjustment plugs. 58's export close no longer re-stamps paired
  rows. The "127 G2 income rows" never existed (tagged income = resolved); `W-G2-SET1-EVIDENCE` fixed.
  G2 open now = **78 unpaired expense rows, 526,011 lifetime, Aug 31,969** (unchanged: paired rows were
  never spending).
- **Rules ledger LIVE:** `_ALEX_Rules` seeded with 124 rules (158). **Morning correlator LIVE,
  preview-only:** 06:30 PHT `correlatorScheduled` (159, AUTO_APPLY=false) writes decisions to
  `_ALEX_CategoryPass2` / `_ALEX_CorrelatorInbound` and logs to `_ALEX_Correlator`. First preview: 4
  decisions (one self-counter rejected + retracted), 0 ambiguous, 28 JOIN_ORDER, 9 e-mail legs whose
  statements are not ingested yet.
- **Lelim:** inflows already INCOME:Personal; 150 re-ruled (closed; outflow A1 > Personal Loan). The
  47,000 repayment is in **no source** the system holds. **Lloyd:** which account and date paid it?
- Marketplace join re-run: 0 new matches (30 rows have no order source).
- **Lloyd (5 min):** (a) answer the Lelim question; (b) in the editor run `categoryPass2Preview()` →
  `categoryPass2Apply()` to apply the 3 pending correlator decisions (2 × COUNTER:Tiktok Paylater,
  Jake Adame 85k → A1 > Personal Loan), or wait a week and flip `CORRELATOR_CFG.AUTO_APPLY`;
  (c) rule the 78 G2 rows in `_ALEX_CategoryPass2` col G (biggest: YNAB "Unaccounted / Varioes
  expences" plugs ≈230k → `DEFER` or `EXPENSE:E4 Capital & One-Offs`; Calong Calong 23k; Amazon /
  Taobao 15 rows); (d) `git push` of `claude/hermes-wave-1-trust-94a9fb` was refused for the agent
  (access rights) — push it from your shell.
- **22:15 update (GAS @55):** Lelim closed (net-0 rows, balances verified), cluster 1 DEFER,
  AUTO_APPLY on, Amazon 13 → B2 supplements, Credit-to-Cash 20 → A1 Cash Loan, buy-load 3 → C2,
  Maya 35k leg paired. **Context mechanism live:** Hermes now asks "What was this for?" on
  memo-less captures and stores your answer; the 12:30/19:30 nudge lists open memo-less
  expenses; Telegram `rule: <payee> = <Category>` rulings reach open rows nightly. **Left for
  you (25 rows):** 15 marketplace rows without a basket, 6 Payment to Merchant / Bills Payment,
  Calong Calong 23k, 3 small — one cluster at a time in chat.
- Known: 43 rows carry counter NAME without counter ID (source-merge EXPORT_HOLD legs) → a 133-style
  backfill next session.

**SESSION 47 (2026-09-04 day) — what changed, what is yours:**

- **Classification map published:** https://claude.ai/code/artifact/cb406300-94db-48e8-b3b9-5c01a0ae9c4c
  (every capture path -> landing -> ledger -> consumers; the five G2/blank producers; the eleven
  category memories; a one-rulings-ledger proposal that keeps the 130 apply core and D-119).
- **Sub-category grammar built, suite 98/98, NOT deployed** (commits `35a6231` + `c3404bb` on the
  Hermes worktree `hermes-wave-1-trust-94a9fb`): `EXPENSE:<Category> > <Subcategory>` in 130/146.
  **Lloyd (2 min):** fresh pull, copy `130` `146` `147` over it, `clasp push -f`, deploy
  `-d "v35 session-47: sub-category grammar + Set 1 evidence fill"`.
- **Set 1: 19 rows settled by the ledger's own evidence** (117 external evidence, 2026-09-01
  rulings R1/R2/R3, exact-amount pairs from read-only exec 21216). **Lloyd, after v35:** editor ->
  `set1EvidenceFillPreview()` -> `set1EvidenceFillApply()` (147, worksheet only) ->
  `categoryPass2Preview()` -> `categoryPass2Apply()` (ledger, snapshot-first). Then answer the
  rest of Set 1 in `drafts/G2-CLASSIFICATION-QUESTIONNAIRE.md` (session-47 section): the
  `...7727 / ...5788 = own wallets` confirmation, the Maya 35k DEFER, and the BPI lumps / odd-centavo
  / small-round groups that no evidence settles.
- Read-only n8n helper added: `W-G2-SET1-EVIDENCE` (`J8HmQo6kGPUw4LlK`). The auto-mode classifier
  blocked an n8n workflow that would have written worksheet DECISION cells, so the fill is GAS (147).

- **13:02 — Set 1 evidence rows APPLIED** (v35 @38; fill 19 → preview decided 19 / errors 0 → apply decided 19, snapshot `20260904130234`; read-back exec 21223 confirms 18 counters + the Northfield sub-category). Remaining G2 spending: Jul 60 rows ₱81,520, Aug 45 rows ₱86,984; 110 transfer rows open. **Lloyd:** answer the Set 1 groups in the questionnaire session-47 section; check BALANCE_CONTROL after the 20:30 run (expect var 0.00 on every FULL account, as at 08:23).

- **15:40 — Sets 1, 2 and the owner-stated Set 5 rules are APPLIED** (v44 @47, runner 151; agent-run under the 14:45 authorization). Today: 19 + 86 + 23 (Set 1) · 138 + 11 + 44 (Set 2) · 112 (Set 5) rows classified through the 130 engine, every apply snapshotted. G2 spending ₱1.52M → ~₱0.77M lifetime (read-back pending). New reference: `_REF_AccountIdentifiers` (150, D-123). **Lloyd:** the six one-liners in the questionnaire (marketplaces, groceries, dept stores, airfare, duty free, placeholders); 20:30 BALANCE_CONTROL check.

**SESSION 46 (2026-09-03 night) — what changed, what is yours:**

- **/remind 403 — ROOT CAUSE FOUND (corrects the session-45 correction).** The n8n Google OAuth
  client (Client ID prefix `499022057812`, read off `GCalendar - Rocloyd87@gmail.com` in the n8n
  UI) lives in GCP project **NAVI-LLOYD**, where the Google Calendar API is **not enabled** (the
  console shows the Enable button). Project 884258367325 is one you cannot open ("You need
  additional access"), and Alfred-Financial-Tower (API enabled, zero traffic) is unrelated.
  Retest 21:36 PHT (exec 21083, agent-sent) still Forbidden; nothing was created.
  **RESOLVED 2026-09-04 05:42 PHT:** Lloyd enabled the API; `/remind test ping in 5m` → exec 21146,
  event `hp1eci9v8c1sv4n6g3bi4k4na4` created ("⏰ REMINDER SET · Fri 4 Sep 05:47"), then deleted.
  Remaining: confirm the 07:00 brief Calendar Today node returns events.
  **Lloyd (1 min):** https://console.cloud.google.com/apis/library/calendar-json.googleapis.com?project=499022057812
  → Enable → `/remind test ping in 5m` → delete the event. The 07:00 brief's Calendar Today
  node starts returning events on the same fix.
- **G2 second pass built, not run** (`144_CategoryPass2.js`, commit `880a271` on the Hermes
  worktree, 10/10 checks, suite 94/94). In-sheet merchant-majority (n≥2, share≥0.8; HIGH from n≥3)
  then 119's business-type routes; marketplaces get no suggestion. **Lloyd:** push + deploy v33
  (copy `144` + `96` over a fresh pull), then in the editor: `categoryPass2BuildTab()` → read the
  HIGH/MED/none counts in the log → `catPass2AcceptHigh()` → `categoryPass2Preview()` →
  `categoryPass2Apply()` → re-read `get_kpis.monthPace.mtdUncategorized`.
  **DONE 2026-09-04 05:46–05:57 PHT (v33 @33, 123 files from a fresh pull; agent drove the editor):**
  `categoryPass2BuildTab` → `_ALEX_CategoryPass2` **421 rows: 16 HIGH (₱48,885.52), 4 MED, 401 no
  evidence**; `catPass2AcceptHigh` {written 16}; `categoryPass2Apply` APPLY decided 16, no error;
  re-run shows alreadyApplied 16 / pending 405. `hermesStagingInstallTrigger()` → one daily 01:30
  PHT trigger. **Finding:** merchant-majority reaches only 16 of 421 G2 rows; the remaining 405 need
  a DECISION by hand in `_ALEX_CategoryPass2` (col G, `EXPENSE:<label>` / `DEFER`) or a new signal.
- **hermesStagingApply now schedulable:** `hermesStagingInstallTrigger()` (in 96, same v33) — one
  daily 01:30 PHT run before W-DASH-SYNC at 02:00. Run it once. Approve `tg-328` / `tg-333` first
  if you want them in (both are card accounts → they go `awaiting_statement`, not to Alex).
- **awaiting_statement matcher: DESIGN ONLY** — `drafts/AWAITING-STATEMENT-MATCHER.md` (exact
  amount + account, date window [−1, +5] d, ambiguity refused, enrich-only per D-017). Two
  questions for you inside it.
- **W-FMP-ALERTS still blocked:** no `FMP apikey` credential exists in n8n (credential list
  checked). Create it, then say so — the rest is 5 minutes.
- Overnight checks: nudges 12:30 (exec 21016) + 19:30 (21066) ran clean; W-DASH-SYNC 20933/20938
  success; no W-ERR since 20848 (2026-09-02).
- **/goal proceed (06:05–06:40):** Atlas artifact republished (session 46); OmniRoute test
  workflow **deactivated** (quiet since 08-31, nothing deleted); W-HERMES `rag_query_rows`
  expression fixed (validates clean). **Built, suite 96/96, NOT deployed:** `145_HermesStagingMatch.js`
  (the awaiting_statement matcher, D-116, hooked into `hermesStagingApply`) and
  `146_CategoryPass2Clusters.js` (one ruling per merchant for the 405 rows). BotFather list in
  `drafts/COMMANDS.md`. **Lloyd next (2 min):** from the same gas-pull folder
  `clasp push -f` then deploy with `-d "v34 session-46b: awaiting_statement matcher + cluster rulings"`;
  editor: `categoryPass2ClustersBuildTab()` (146) → rule the clusters in
  `_ALEX_CategoryPass2Clusters` col F → `catPass2ClusterFill()` → `categoryPass2Preview()` →
  `categoryPass2Apply()` (144). `hermesStagingMatchPreview()` (145) shows what the held captures
  would match.
- **06:15–06:25 (v34 live, FMP credential created):** `_HERMES_WATCHLIST` created (4 seed rows);
  FMP credential selected on `FMP EOD Light`; **test run FAILED at Read Watchlist: Forbidden → the
  Google Sheets API is also disabled in NAVI-LLOYD.** Enable
  https://console.cloud.google.com/apis/library/sheets.googleapis.com?project=499022057812 and say
  so; the agent re-runs and activates. `categoryPass2ClustersBuildTab()` → **117 clusters over 405
  rows, and the top five are transfers, not merchants:** instapay transfer transfer to (48 rows,
  ₱518,687) · instapay transfer (27, ₱179,175) · deposit to gsave account (14, ₱177,500) · fund
  transfer (7, ₱139,830) · manual balance adjustment (13, ₱116,455). 146 now accepts
  `COUNTER:<own account>` (commit `00b72af`, needs a v35 push of 146). **Lloyd rules in
  `_ALEX_CategoryPass2Clusters` col F:** e.g. `deposit to gsave account` → `COUNTER:CIMB Gsave`;
  InstaPay series → `EXPENSE:E1 Family Support & Repairs` if remittances, `COUNTER:<own>` if own,
  `DEFER` otherwise. Then `catPass2ClusterFill()` → `categoryPass2Preview()` → `categoryPass2Apply()`.
- **06:26–06:33 — Google APIs + W-FMP-ALERTS:** Lloyd enabled the Sheets API in the console; the
  agent then ran his `gcloud services enable` script on `navi-lloyd` (Workspace, Maps, AI, data
  groups — all four operations succeeded; gcloud SDK 579 signed in as rocloyd87). W-FMP-ALERTS
  manual test exec 21153 ran end to end and the workflow is now **ACTIVE** (05:45 / 15:45 PHT).
  **But the US lane got `401 Invalid API KEY` from FMP** (Sheets read OK: AAPL gf_price 328.21).
  **Lloyd:** open credential `FMP API` → parameter name must be exactly `apikey`, value = the key
  on the FMP dashboard (free keys need the confirmation email). The PSE lane works regardless.
- **07:27 — W-FMP-ALERTS LIVE end to end, without FMP.** Every key (original, re-pasted,
  regenerated) was rejected 401 by FMP itself (your own PowerShell call too), so the US lane now
  reads the same GOOGLEFINANCE columns as the PSE lane (FMP nodes disabled, re-enable note on
  `From Sheet Columns`). Test exec 21166: AAPL $328.21 above 320 fired → Telegram card (msg 346)
  → write-back. **Lloyd:** edit or delete the seed row `AAPL above 320` in `_HERMES_WATCHLIST`
  and add your real symbols/thresholds; FMP account issue is now FMP support's, not ours.
- **07:00 brief (exec 21157) VERIFIED:** 📅 TODAY lists 8 Calendar events (cal_ok true) — the
  Calendar fix reached the brief; 💸 YESTERDAY ₱0 present; driver line still G2 ₱112,141 (the
  transfer clusters). Noted for later: `BUDGET LEFT ₱164,489 of ₱164,489` and `kept this month ₱39`.

- **07:30–07:45 — Research watchlist LIVE (D-118).** `_HERMES_WATCHLIST` now holds VOO / QQQ /
  SCHD / GLD (sheet lane), BTC / ETH / SOL (new **CoinGecko** lane, no key), BDO / ALI / ICT /
  AREIT / SM (sheet lane, fires 15:45). Rows go in only through `W-WATCHLIST-UPSERT`
  (`eQ9NlFCenQFvJ4d7`, manual) + `hermesWatchlistEnsure()`. Exec 21171 sent the first real card:
  "📈 BTC $81,295 +5.4%" (msg 347). `Write Back` re-keyed on `row_number`. **Lloyd:** delete the
  two AAPL rows (2–3) in the tab; the claude.ai FMP connector needs the regenerated key (or drop it).
- **07:50–08:00 — deletions + Twelve Data.** AAPL rows 2–3 deleted (one-off Sheets delete step, helper
  removed afterwards); `Write Back` keyed on `row_number` proven (exec 21174). Workflow renamed
  **W-PRICE-ALERTS**; FMP nodes removed; US lane = `US Ids → Twelve Data Quote (batch) → From Twelve
  Data`, with per-row GOOGLEFINANCE fallback (which carried the run: VOO 710.72 etc.; BTC +5.2% card
  msg 348). **Twelve Data returned 401 "apikey parameter is incorrect or not specified"** although the
  credential's Name is `apikey` — same wording as FMP. Test from PowerShell:
  `Invoke-RestMethod "https://api.twelvedata.com/quote?symbol=VOO&apikey=PASTE_KEY"`. Rows → the n8n
  Query Auth path is dropping the parameter (switch to a Header Auth credential `Authorization: apikey KEY`);
  401 → the pasted Value is wrong (masked field ~48 chars vs a 32-char key).
- **07:58 — Twelve Data lane LIVE (exec 21176, `source: twelvedata`).** Root cause of every 401 on
  FMP and Twelve Data: the draft node had `genericAuthType: queryAuth`; n8n needs `httpQueryAuth`,
  so no credential was ever sent. Fixed on the node; the keys were fine (Lloyd's PowerShell test
  passed once the clipboard was not being overwritten by the agent's clicks in the n8n tab).
  Lesson: `30-knowledge/lessons/2026-09-04-n8n-generic-auth-type-must-be-httpqueryauth.md`.
- **08:30 — cluster rulings applied (goal):** `deposit to gsave account` → `COUNTER:CIMB Gsave`
  (14 rows) and `manual balance adjustment` → `DEFER` (13) written to the cluster tab, `catPass2ClusterFill`
  {2 clusters, 27 rows}, `categoryPass2Preview` {decided 14, pending 378, deferred 13} → `categoryPass2Apply`
  decided 14, no error. HEAD already had the COUNTER-capable 146 (fresh pull verified). **Lloyd:** the
  InstaPay / fund-transfer clusters (≈₱837k, 82 rows) are yours to rule: `EXPENSE:E1 Family Support & Repairs`
  if remittances, `COUNTER:<own account>` if to your own bank, else `DEFER`; then fill → preview → apply.
- **10:30–12:00 — G2 questionnaire started.** Lloyd's rules: own account → own account = transfer
  (`COUNTER:`); everything else categorised by recipient/notes down to sub-category (Anthropic →
  C3 AI tools, coffee → B1 Drinks & Coffee, hardware → E1 Hardware & Repairs). Full 115-cluster
  list dumped (read-only helpers `qTHp0UBpEDGppSiB`, `ju31Ptw9i3iYNk0r`); five sets drafted in
  `drafts/G2-CLASSIFICATION-QUESTIONNAIRE.md`; **Set 1 (transfer rails, ₱1.13M) presented with
  row-level detail, answers pending.** Sub-category grammar not yet writable (extend 130/144/146).
  Session 47 starts with a classification-pipeline HTML + redesign, under the 2026-08-28
  balance-correlation guard-rail.
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

1b. ~~Push + deploy v29~~ **DONE 2026-09-03 04:30 PHT as @30.** (@29 was deployed without a
   preceding `clasp push`, so it served the @28 code — always push, then deploy.) Verified over
   `/exec`: `get_kpis.freshness.fingerprint` = `v4…`, `monthPace` and `recurringDue30d` present
   (recurring due ≤30 d: ₱5,126.07 across 4 charges); approve/reject skip rejected rows.

1c. ~~Push + deploy v31~~ **DONE 2026-09-03 08:57 PHT as @32** (verified by a fresh pull: GoTyme in the
   registry, rejected rows re-stageable, 143 present). Remaining GoTyme steps below still owed.
   Original instructions kept for reference: **Push + deploy v31** (same pull dir; `90`, `96` and new `143` copied over it): GoTyme registry account
   (`GOTYME`, deposit, card 2334) and "a rejected staging key can be re-staged". One line:

   ```powershell
   clasp push -f; clasp deploy -i AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ -d "v31 session-45c: GoTyme account + evidence; re-stage after reject"
   ```

   ~~Run `ownerGotymeApply`~~ **DONE 2026-09-03 09:09** (agent-driven in the browser pane):
   `{"balance_id":"GOTYME|2334|2026-09-03","tabAppended":true,"csvAppended":true}`. ~~Create the GoTyme account in MoneyMatter~~
   **DONE 2026-09-03 09:0x** through the signed-in web UI in the browser pane: `GoTyme`, Saving
   account, PHP, balance 0, id `01a064cb-0d24-72e8-a823-6acb65c3e3df` (verified with get_accounts;
   30 accounts now). Also add GoTyme to the Tarsi app if you want a tarsi leg.

2. **Telegram tests (Lloyd opened Telegram web in the browser pane; the agent drove it):**
   - ~~`/pending`~~ **PASSED** (exec 20945, 15 nodes, 4.7 s, no agent) → one row `tg-jollibee-20260822`.
   - ~~`/sub`~~ **PASSED** (exec 20946, 16 nodes, 9 s) → 🔁 SUBSCRIPTIONS card, stale flags shown.
   - `/remind` → parses correctly (execs 20948, 10:03 retest) but **Calendar insert still 403** even
     after the 07:00 reconnect, while the brief reads the same calendar fine → the consent was
     granted view-only. **Lloyd:** n8n → Credentials → `GCalendar - Rocloyd87@gmail.com` → Reconnect
     and on Google's consent page tick **every** Calendar checkbox (edit calendars + edit events),
     then `/remind test ping in 5m` (delete the event afterwards).
     **21:02 PHT retest (exec 21079) STILL 403** after Lloyd re-consented; Google's
     "rocloyd.com has this access" page lists only Gmail scopes, so the Calendar grant never
     took. Google skips the checkbox screen on Reconnect when a grant already exists — the fix
     is: https://myaccount.google.com/connections → **rocloyd.com → Remove access**, then n8n
     Reconnect on `GCalendar - Rocloyd87@gmail.com` (fresh consent shows both Calendar boxes;
     tick both). Also confirm the Google Calendar API is enabled in the rocloyd.com GCP project.
     The `Gmail account` credential cannot be used: the node only accepts googleCalendarOAuth2Api.
     **CORRECTION 21:40 PHT:** the brief's `Calendar Today` getAll on the SAME credential also
     returns `Forbidden` (exec 20965, hidden by continueRegularOutput + alwaysOutputData), so this
     is NOT a consent problem — the Google grant to the n8n project ("Untitled project", GCP
     project 884258367325, granted 2025-10-14) already carries the full Calendar scope. Read AND
     write 403 = **Google Calendar API not enabled in that project.** Fix (Lloyd, 1 min):
     https://console.cloud.google.com/apis/library/calendar-json.googleapis.com?project=884258367325
     → Enable → then `/remind test ping in 5m`. Do NOT revoke anything.
   - ~~One fresh receipt photo~~ **PASSED 08:33** (TikTok order, exec 20979, key tg-328) — see 4b.
   - ~~Typed bank-SMS capture~~ **PASSED 10:02** (second attempt; the first, exec 20992, invented
     key `tg-1` and a May date because Build Text Prompt passed no message id / date — fixed live,
     and Guard v2 correctly withheld the bad card): −₱336 Cebu Pacific, T1, BPI Credit Card via the
     card map, dated today, key `tg-333`, pending.
   - ~~Decide `tg-jollibee-150`~~ Lloyd: real meal → stays APPROVED; its duplicate test row
     `tg-jollibee-20260822` rejected. Pending rows now: `tg-328` (TikTok), `tg-333` (Cebu Pacific).
3. **Chowking receipt by hand** (−564 · Chowking Gaisano Mall Bajada · 2026-09-02 · D2). Name the
   card account first, then run (replace `ACCOUNT`):

   ```bash
   curl -sS -X POST "https://script.google.com/macros/s/AKfycbw9t20LiJP--NLKmvI5C2PEttHV4iv3kcVjJFv-JWDz4osSPGkyM0EFhi64iy-7wsAQ/exec" -H "Content-Type: application/json" -d "{\"key\":\"<HERMES_SHARED_SECRET>\",\"tool\":\"stage_expense\",\"params\":{\"amount\":\"-564\",\"date\":\"2026-09-02\",\"payee\":\"Chowking Gaisano Mall Bajada\",\"category\":\"D2\",\"account\":\"ACCOUNT\",\"raw_input\":\"Chowking SI#08091072 09/02/2026 total due 564.00 Mastercard/Visa\",\"idempotency_key\":\"tg-312\",\"evidence_url\":\"https://drive.google.com/file/d/1VYYLqRpVGMUC7UFhjb2sQz243jRWdUdD/view?usp=drivesdk\"}}"
   ```

   Then in Telegram: `approve tg-312`. (The secret is the W-HERMES Config `gas_key`.)
4. ~~Category rulings accept-all~~ **ALREADY APPLIED — verified 2026-09-03 09:10–09:12** by running
   the sequence in the editor: `catAcceptAutofill` {written 0, skipped 321} →
   `categoryRulingsPreview` {decided 0, pending 30, alreadyApplied 321} → `catAcceptPairApply`
   {fills 0, holds 0}. Only the 30 blank-category rows remain, with no suggestion. The ₱112k/mo
   "G2 Review / Uncategorized" driver is therefore not a worksheet backlog: 135 ratified many rows
   INTO G2. **Session 46:** build a second suggestion pass over rows currently in G2
   (merchant-majority across the ledger + a manual worksheet for the remainder), then re-read
   `get_kpis.monthPace.mtdUncategorized` and the brief's driver line.

5. **Credentials that unblock the remaining drafts** (n8n → Credentials) — **SESSION 50: shells created;
   FMP already existed.** `TickTick OAuth2 - rocloyd87` (`TVu3QTaHN9dWC2Xy`) and `Airbnb iCal - Cascade`
   (`Ijb693H90z3qT1Np`) exist with placeholder values; `/todo` and `/cascade` routes are BUILT and wait
   only for the real values (see SESSION 50 close "Yours"). `FMP API` (`wk2ToB3n5OZ7Eukl`) already existed
   and `W-PRICE-ALERTS` (`X2bAv2WXOOZJ3pP6`) is ACTIVE.
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
7. ~~Q4 HSBC name~~ **ANSWERED (Lloyd, session 50): HSBC Gold Visa is the old CANCELLED card; HSBC Live+ replaced
   it — one account lineage. Recorded in vault 01-FACTS and memory; do not ask again.**

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
- [x] **v2 BUILT (session 50, n8n, validated; phone test is Lloyd's):** voice notes (Gemini
      transcribe → same text chain), inline OK/Undo buttons (Telegram Trigger now listens to
      `callback_query`; `Is Callback` lane in W-HERMES), undo = Drive trash of the note, RAG
      indexing of `00 Inbox` + `Journal/2026` via the re-activated `W-RAG-INGEST` (`2WnJBAj1XGJ7hgHI`).
- [x] Live test DONE 2026-09-05 12:11 (exec 21508 voice transcribed; 21505/21513 Undo trashed the notes).
- [ ] If LifeVault becomes the target: verify `livesync-bridge` bidirectionality on the VPS,
      then re-point the folder map in `Build Note`.

## D — Memory & RAG — LIVE

- [x] `remember/recall/forget` (doc_class=memory), Chat Memory 20 turns.
- [x] LifeVault → RAG nightly (`W-OBSIDIAN-INGEST`), `rag_search` journal recall proven.
- [x] After C: inbox-lane notes are indexed within ~30-45 min by `W-RAG-INGEST` (session 50; no new tables).

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
- [x] Commands: `/brief /spend /log /note /remind /todo /sub /networth /goals /cascade` — **`/todo` and `/cascade` LIVE and
      verified 2026-09-05 (exec 21514 TickTick task created; exec 21518 Cascade card sent).** Older text kept below for history:
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
- [x] FMP price/breakout alerts — shipped as `W-PRICE-ALERTS` (`X2bAv2WXOOZJ3pP6`, ACTIVE; Twelve Data · GOOGLEFINANCE ·
      CoinGecko; `FMP API` credential exists). Older text kept below for history:
      **IMPORTED INACTIVE (session 45) as `W-FMP-ALERTS — Price Alerts` `X2bAv2WXOOZJ3pP6`**, with
      the GSheets + Telegram credentials set; needs the FMP apikey credential and one
      `hermesWatchlistEnsure()` run (§A 5) before activation. Draft: `drafts/W-FMP-ALERTS.json`. Constraint found: FMP `quote`
      endpoints are Premium-gated on this plan and PSE tickers are not on FMP, so the draft is
      end-of-day (FMP EOD light for US, GOOGLEFINANCE columns for PSE).
- [x] P2C "island" webhooks REMOVED from `TEST - OmniRoute gateway` (session 50: 33 `p2c-*` nodes, 113 → 80;
      the workflow has been inactive since 09-03).

## F — LATER

- [x] Health Connect webhook → n8n → `stage_health`: `W-HEALTH-INGEST` (`sS5kebHIWMO3p41s`) ACTIVE (session 50).
      Lloyd: point the phone exporter at `POST /webhook/health-connect` with header `X-HC-Token`.
- [ ] ~~Uptime Kuma on the Pi~~ DEFERRED (Lloyd, session 50: the VPS Uptime Kuma already covers it).
- [x] Monthly scorecard + Sunday briefing: `W-SCORECARD` (`b6Qno1SMLMHG8tvj`) ACTIVE (session 50): Sunday 18:00
      weekly review, 1st 08:30 monthly scorecard, owner hook `GET /webhook/scorecard-run?mode=weekly|monthly`.
- [x] ~~Wire real `essential_prefixes`~~ **DONE 2026-09-02 (`37bf844`):** `metricsKpis` uses
      `metricsEssentialMedian_` over `GOAL_CONTRACT.essential_prefixes`; the 0.9 approximation is
      only the fallback when no essential-coded rows exist.
- [x] Upstream account-feed fix **CODED (session 45, `ad00d50`, live after v28):**
      `dashSyncAccountsRead_` prefers `tarsi_balance` unless `coverage === 'FULL'`; the phantom
      −641k GCash line disappears from the 02:00 report the first night after the deploy (verify).
- [ ] Carried Alex items: 30 blank category rows; sheet script cleanup (session 50: inventoried, only the
      duplicate `128_` prefix found, nothing removed); HSBC September statement refresh (waits for the
      statement, due ~13 Sep); small-account confirmations; sheet link-sharing decision.
