# G2 classification questionnaire — state at end of session 46 (2026-09-04 ~12:00 PHT)

**The question every row must answer:** did the money leave the household (spending → a category
and sub-category) or move between Lloyd's own accounts (transfer → `COUNTER:<own account>`)?
Lloyd's clarification: *transfer means own account → another own account*, nothing else.

**Guard-rail for the next session (Lloyd, verbatim intent):** balances as of **2026-08-28**
(the verified anchors in BALANCE_CONTROL) plus every transaction after that date must keep
correlating. Re-categorising never touches `signed_amount`, `date` or `account`; `COUNTER:`
only fills `counter_account_id/name`; snapshots are taken before every apply
(`alexSteadySnapshot_`). Do not rebuild the ledger, do not re-run merges, do not delete rows.

## Where the rows live
- Row-level worksheet `_ALEX_CategoryPass2` (144): 421 rows, DECISION col G, `applied_at` col H.
- Cluster worksheet `_ALEX_CategoryPass2Clusters` (146): 115 clusters still undecided, DECISION col F.
- Flow after answers: `catPass2ClusterFill()` → `categoryPass2Preview()` → `categoryPass2Apply()`.
- Read-only n8n helpers kept for the next session: `W-G2-CLUSTER-DUMP` (`qTHp0UBpEDGppSiB`,
  all undecided clusters) and `W-G2-INSTAPAY-GROUPS` (`ju31Ptw9i3iYNk0r`, row-level transfer rows).
  Run from the editor, read the execution with `n8n_executions get … mode filtered`.
- Grammar today: `EXPENSE:<category label>` · `COUNTER:<own account name>` · `DEFER`.
  **Sub-categories are not yet writable** — extend 130/144/146 to accept
  `EXPENSE:<category> > <subcategory label>` (write `subcategory_id/label` from
  `CAT_SUBCATEGORIES` in 21_CatRefData) before applying Sets 3–5.

## Rulings already applied this session
- `deposit to gsave account` (14 rows) → `COUNTER:CIMB Gsave` ✔ applied
- `manual balance adjustment` (13 rows) → `DEFER` ✔
- 16 rows from ledger evidence (merchant-majority / 119 routes) ✔ applied 05:54

## Lloyd's category rules given so far
- Anthropic → `C3 Subscriptions > Ai & Productivity Tools`
- Coffee bar / cafés → `B1 Food & Groceries > Drinks & Coffee`
- City Hardware / Wilcon / Handyman → `E1 Family Support & Repairs > Hardware & Repairs` (or E4 one-off by item)
- Own-account moves → `COUNTER:<account>`

## SET 1 — transfer rails (₱1.13M, ~118 rows) — PRESENTED, AWAITING ANSWERS

| # | Kind | Rows · ₱ | From | Proposed |
|---|---|---|---|---|
| 1 | InstaPay "TRANSFER TO OTHER BANK" | 48 · 518,687 | BPI Main | Lloyd decides (own bank vs family) |
| 2 | InstaPay Transfer (bare) | 27 · 179,175 | BPI Main | same |
| 3 | Fund Transfer (bare) | 7 · 139,830 | BPI Main, Maya | same |
| 4 | Sent GCash → Union Bank …5788/…4112 | 8 · 78,070 | GCash | `COUNTER:Union Bank Debit` |
| 5 | Sent GCash → PayMaya/Maya …7727/…4984 | 10 · 92,500 | GCash | `COUNTER:Maya eWallet` |
| 6 | Sent GCash → BPI …4647 | 5 · 54,875 | GCash | `COUNTER:BPI Main Account` |
| 7 | Sent GCash → GrabPay …7727 | 4 · 4,910 | GCash | `D1 Transport > TN Vs (Grab)` |
| 8 | Deposit to GSave (stray) | 2 · 2,000 | GCash | `COUNTER:CIMB Gsave` |
| 9 | "SAVINGS - CIMB …" 28 Jun 25 | 3 · 88,139 | CIMB Gsave 37,654.26 / Upsave 39,999.51 / RCBC | own savings moves |
| 10 | Union Bank "ONLINE FUND TRANSFER UB…" | 5 · 1,250 | UB Debit | ₱250 on the 24th monthly, "TO OWN" — which account? |
| 11 | Partner Merchant Cash In | 4 · 15,749 | BPI Main | TIKTOK BNPL 722.68 + 13,105.84 (15 Sep 25); DRAGONPAY 1,420 (18 Nov 25); 500 (20 Jul 26) |
| 12 | PESONet 1,000 (17 Oct 25); Scheduled Transfer 1,000 (28 May, 26 Jun 26); Maya "fund transfer" 35,000 (6 May 25) | | | same as 1 |

### Set 1 row detail for 1–3 (BPI Main, 80 rows)
- Large round lumps: 25,000 (2025-08-18, 2025-10-21) · 50,000 (2025-09-19, 2026-01-08, 2026-01-12, 2026-07-22) · 35,000 (2025-09-22; 2026-05-22 Fund Transfer) · 30,000 (2026-03-27) · 33,000 (2026-06-17 Fund Transfer) · 20,000 (2026-04-13, 04-23, 04-29) · 10,000 (2026-05-13 Fund Transfer; 2026-08-24) · 9,000 (2025-09-19, 2026-07-02) · 7,000 (2026-07-30)
- Odd-centavo (look like exact bill / card-statement settlements): 6,116.04 (2025-12-10) · 19,575.98 (12-19) · 3,153.61 (2026-01-09) · 5,229.04 (01-12) · 4,581.48 (01-15) · 2,924.54 (01-19) · 3,539.50 (01-22) · 10,635.15 (02-03) · 10,225.55 (02-16) · 14,573.46 (02-27) · 8,679.71 (03-09) · 21,748.28 (03-23) · 7,856.78 (04-08) · 6,015.27 (04-13) · 11,125.41 (05-04) · 23,806.84 (05-18) · 13,955.27 (06-08) · 19,140.19 (06-12)
- Small round, several a month: 200 · 500 · 510 · 735 · 1,000 ×12 · 1,500 · 1,700 · 1,950 · 1,997 · 2,000 ×7 · 2,100 · 2,450 · 2,500 · 2,800 · 3,000 ×6 · 3,100 · 3,500 ×2 · 3,600 · 3,950 · 5,000 ×5 · 5,388
- Known counterparty: **21,330 on 2026-06-05 "Fund Transfer"** = Northfield rent (Jenrix) → `A1 Debt Service > Northfield`
- Answer format expected: `1-3: lumps = family; odd-centavo = my <card>; small round = family; 21,330 = Northfield` then `4 = … 12 = …`

## SET 2 — reconciliation placeholders (~₱300k) — NOT YET PRESENTED
Unaccounted Transactions (RCBC 84,249; BPI 26,552; Cash 43,723 + 22,024; BPI CC 10,635; Shopee Pay 212), Varioes/Varius Expences (GCash 22,347; Maya 21,026), "Edited on June 28" HSBC 67,783, "Edited on February 9" UB loan 7,733, Closed Account (4 · 7,261), Entered automatically by YNAB (2 · 3,263), TO BE ACCOUNTED FOR 1,047, Balance Adjustment 294, unnamed rows (BPI Credit to Cash 19 · 3,094; eWallet Maya 19,733; Credit to Cash 491), Un-accounted Expences (transportation…) 3,147. Proposed: keep as G2/`DEFER` — these are legacy YNAB/Tarsi placeholders; ask whether to leave them counted as spending or exclude pre-2026 ones from burn.

## SET 3 — people / debts — NOT YET PRESENTED
JAKE ADAME DEBT 85,000 (Other Accounts, 2025-06-27) · Calong Calong and Ann Rey Cerillo Paid 23,000 · Donation to Batch Mate 250 → `D2 > Donations, Tite & Gifts` · Batch Invitation Payment 1,000 → same · Bills Payment 2,930 · Payment to Merchant (5 · 16,752) · UBE Express 150 → `D1 > Public Transport`

## SET 4 — marketplaces (basket decides) — NOT YET PRESENTED
TikTok Shop (24 · 16,423) · Shopee (16 · 13,859) · Lazada (5 · 1,909) · Amazon MKTPL (12 · ~56k, RCBC Hexagon) · Amazon Prime 959 → `C3 > Media & Streaming` · Taobao (2 · 10,399) · TikTok PayLater 2,499. Ask for a default per marketplace or `DEFER`.

## SET 5 — merchants by business type — NOT YET PRESENTED (proposed sub-categories)
Buy Load (65 · 12,464 + 3 stray) → `C2 Connectivity > Family Internet & Load - Bohol` (confirm) · cafés: Downtown Cafe 760, Native Kopi 662, Oona 580, Starbucks 575, Bos Coffee 375, Coffee Bar 140 → `B1 > Drinks & Coffee` · Jollibee 648 → `B1 > Dining Out` · Robinsons Supt 634, Gaisano S/M 2,566, Gaisano Dep 5,380 → `B1 > Groceries` (family or personal?) · Watsons 590 → `B2 > Personal Meds & Pharmacy` · Citihardware ×3 3,670, Wilcon 4,386, Handyman 1,507 → `E1 > Hardware & Repairs` · Daiso 490, Muji 365, Mumuso 558, RDS 879, Rob Dept 329, R AND C 3,300, KCC 3,420 → `D2 > Personal Effects` or `B3 > Household Supplies` (ask) · Google One 285 → `C3 > Cloud Storage` · YouTube ×2 570 → `C3 > Media & Streaming` · Hetzner 643, Google Tacit Dynamics 470 → `C3 > Ai & Productivity Tools` · PAL tickets 7,432, Pal Airy 404, Booking hotel 1,915 → `D1 > Airfare` / `E3 Contract travel`? · Dubai Duty Free 102, Seajoy SG 221, PP*CODE SG 100 → `D2 > Personal Effects` · Interest (RCBC card) 3 · 1,326 → `G1 > Card Fees & Interest` · withholding tax / tax withheld / debit memo / partner merchant fee (~50) → `G1 > Bank Transfer Fees` · POS Debit 455, Interest unaccounted 22 → `DEFER`.

---

# Session 47 (2026-09-04 desktop) — state after the classification map

**Read first:** the classification map artifact (Alex Classification Map,
https://claude.ai/code/artifact/cb406300-94db-48e8-b3b9-5c01a0ae9c4c): every capture path,
where G2/blank is produced, the eleven category memories, and the one-rulings-ledger proposal.

## Grammar: sub-categories are now writable (commit 35a6231, NOT yet deployed)
`EXPENSE:<Category Label> > <Subcategory Label>` is accepted by 130 (writes
`subcategory_id/label` next to the category) and by 146 (cluster validator). The pair is
validated against `CAT_SUBCATEGORIES` (21); wrong parent or unknown sub is an error, never a
guess; case/spacing-insensitive; canonical labels written. Plain `EXPENSE:<Category>` unchanged.
Suite 98/98. **Lloyd:** copy `130`, `146`, `147` over a fresh pull → `clasp push -f` → deploy
`-d "v35 session-47: sub-category grammar + Set 1 evidence fill"`.

## Set 1 — what the ledger itself already answers (module 147, commit pending push)
Read-only helper `W-G2-SET1-EVIDENCE` (`J8HmQo6kGPUw4LlK`, exec 21216) paired every
transfer-shaped G2 outflow (129 rows) against opposite legs at the exact amount within ±5 days
on another own account. Result: **8 paired, 121 unpaired.** The odd-centavo hypothesis
(card-statement settlements) did NOT pair — no card-side leg at those amounts exists in the
ledger, so those stay Lloyd's. Settled by evidence and written by `set1EvidenceFillApply()`
(147; worksheet DECISION only, then `categoryPass2Preview()` → `categoryPass2Apply()`):

| Rows | Decision | Evidence |
|---|---|---|
| 2026-04-23 20,000 | `COUNTER:CIMB DragonFi Save` | 117: CIMB history names ROCLOYD PINOS LIGASON |
| 2026-03-27 30,000 · 04-13 20,000 · 04-29 20,000 | `COUNTER:DragonFi Cash` | 117: investment-app deposit history (2026-08-06 screenshot) |
| PESONet 1,000 2025-10-17 · 500 11-24 · 1,000 12-02 · 2,000 2026-07-21 | `COUNTER:GCash eWallet` | GCash side already transfer, counter=BPI Main, same amount same/next day |
| Scheduled Transfer 1,000 2026-05-28, 06-26 | `COUNTER:BPI Direct Saveup` | 131 R1 (Lloyd, 2026-09-01) |
| Fund Transfer 21,330 2026-06-05 | `EXPENSE:A1 Debt Service > Northfield Blk 4 Lot 1 and 3` | 117 route (Jenrix) |
| Sent GCash → BPI …4647 (5 · 54,875) | `COUNTER:BPI Main Account` | 131 R2/R3: 4647 (VYBE) = BPI Main |
| 3 stray "Deposit to GSave" (reference-prefixed notes) | `COUNTER:CIMB Gsave` | this morning's cluster ruling |

Balance-neutral by construction: 52 sums `signed_amount` per account; a counter changes a
balance only for txn_type transfer / credit_payment / loan_payment, and the grammar never
rewrites those types.

## Set 1 — still Lloyd's (answer format unchanged)
- **New evidence on the GCash sends:** the endings are account keys, not random. `…7727` is
  the ending on BOTH the Maya sends (9 · 86,485) and the GrabPay sends (4 · 4,910) → it is your
  mobile number, so both wallets are your own: proposed `COUNTER:Maya eWallet` and
  **`COUNTER:Grab Pay`** (session 46 had proposed D1 Transport; Grab Pay is a registry account).
  `…5788` also appears as a SENDER ("Received GCash from Union Bank … 5788") → own UB account:
  `COUNTER:Union Bank Debit` (8 · 80,570). Odd ones left blank: Maya `…0677` (8,015, 2025-06-13)
  and UB `…4112` (1,515, 2022-07-04). Say "confirm" and 147 gets a second block.
- **Maya −35,000 "fund transfer" 2025-05-06** is a four-row tangle (BPI +35,000 transfer
  ctr=Maya; BPI −35,000 F3 ctr=Grab Pay; BPI +35,000 ELINK income 05-08). Proposed `DEFER` until
  those four are looked at together; nothing in Set 1 depends on it.
- BPI Main InstaPay/Fund Transfer, unpaired: 25,000 ×2 · 50,000 ×4 · 35,000 ×2 · 33,000 ·
  10,000 ×2 · 9,000 ×2 · 7,000, the 18 odd-centavo amounts, and the small round series —
  **your call, one line per group** (`lumps = …; odd-centavo = …; small round = …`).
- Union Bank "ONLINE FUND TRANSFER … TO OWN" 250 monthly (which own account?), Partner Merchant
  Cash In (TikTok BNPL 722.68 + 13,105.84; DragonPay 1,420; 500), "SAVINGS - CIMB" 28 Jun 25 ×3.

## Sets 2–5: unchanged, not yet presented. Set 5 answers can now use the `>` form directly.

## Session 47, 13:02 PHT — Set 1 evidence rows APPLIED
v35 live as @38. `set1EvidenceFillApply` {toWrite 19} → `categoryPass2Preview` {decided 19, pending 359,
deferred 13, alreadyApplied 30, errors 0} → `categoryPass2Apply` APPLY decided 19, snapshot
`20260904130234`. Read back (exec 21223): 18 counters set, txn_type still expense on all; the
21,330 Northfield row is now `A1 Debt Service / Northfield Blk 4 Lot 1 and 3` — the first
sub-category ever written by a ruling. Remaining G2/blank spending: 2026-07 = 60 rows ₱81,520,
2026-08 = 45 rows ₱86,984 (lifetime ₱1.52M); 110 transfer-shaped rows still open = the groups
above that are yours. BALANCE_CONTROL 08:23 (pre-apply) all FULL accounts var 0.00; the 20:30
`alexBalanceScheduled` run is the post-apply re-check.

## Session 47, 15:30 PHT — Set 1 closed by e-mail correlation + Lloyd's seven answers
PST correlation (1,545 confirmations) named the recipient of 75 of the 110 open rows. Lloyd's
answers (2026-09-04): Own Bank …200000324 "Cascade payout" = **expense**, and a **new category
H1 Cascade Hideaway** (plus the existing `Cascade` tag for income: inward AUB transfers to …4647)
isolates the business; Cascade running costs go there too; named persons = one-time D2; HC
residences / Birch Tower = Airbnb stays (D2 > Entertainment & Leisure); TikTok BNPL cash-ins =
`COUNTER:Tiktok Paylater`; DragonPay 1,420 and the 500 = one-time TikTok payments; UB ₱250
"pag ibig" = family support; unknown GCash endings = family; CIMB closures with no leg = one-time
expense (F3; DragonFi balance must not move).
**Block 2 in 147 (commit `fa473c7`): 86 rows.** Held: 23 Cascade rows (wait for H1) and the
Maya −35,000 mirror leg (DEFER). **Lloyd:** (1) in the Tarsi app create expense category
**"H1 Cascade Hideaway"** (sub-categories suggested: Payouts, Cleaning & Supplies, Marketing,
Repairs), export a backup so the id can be synced into 21/130; (2) from `C:\Users\Lloyd\gas-v35`
push + deploy v36; (3) editor: `set1EvidenceFill2Preview()` → `set1EvidenceFill2Apply()` →
`categoryPass2Preview()` → `categoryPass2Apply()`.
Recipient dictionary (149 destinations, PST) is the seed for the rules ledger; 148 (standing
e-mail-leg correlator over `BPI_Online` / `UnionBank_Online`) is the next build.

## Session 47, 16:10 PHT — H1 Cascade Hideaway created (D-122)
- **MoneyMatter (budget.rocloyd.com):** `H1 Cascade Hideaway` (top level, id `01a06b0c-35f3-7365-8e1a-d1d15d044c3a`)
  with sub-categories Payouts, Cleaning & Supplies, Marketing, Repairs & Maintenance, Airbnb Income —
  verified through the app's own category API (86 categories).
- **Alex (commit `83a6175`, staged in `C:\Users\Lloyd\gas-v35`):** 21 `CAT_C.h1`, `CAT_CATEGORIES`,
  four `CAT_SUBCATEGORIES`; 130 label map; 147 block 3 (23 Cascade rows) gated on Tarsi.
- **Tarsi (Lloyd, phone):** create expense category **"H1 Cascade Hideaway"** with sub-categories
  **Payouts · Cleaning & Supplies · Marketing · Repairs & Maintenance**, and an income category
  **"H1 Cascade Hideaway"** with sub-category **Airbnb Income**; export a backup. Predicted ids:
  `custom-h1-cascade-hideaway`, `sub-expense-custom-h1-cascade-hideaway-payouts` etc. Next session
  runs `catSyncTaxonomy()` + `catAssertIntegrity()` on that backup; only then block 3 and any export.
- Cascade income today: tag `INCOME:Cascade` (existing tag) on the inward AUB → …4647 rows; the
  income category follows the Tarsi sync.

## Session 47, 14:25 PHT — Set 1 block 2 APPLIED (v36)
`set1EvidenceFill2Apply` 86 → `categoryPass2Preview` decided 86 / errors 0 → `categoryPass2Apply`
decided 86 (EXPENSE 45, COUNTER 41), snapshot `20260904142550`. Read-back (exec 21234): remaining
G2/blank unpaired spending ₱751,666 lifetime (was ₱1.52M at 08:00); Aug 2026 = 36 rows ₱58,934
(was ₱86,984). Open transfer-shaped rows: 24 = 23 Cascade (block 3, gated on Tarsi H1) + Maya
35k mirror (DEFER). **Set 1 is closed apart from the Cascade block.** Sets 2–5 next, evidence-first.

## Session 47, 14:40 PHT — Tarsi H1 verified, block 3 gate cleared
`tarsi-backup-20260904-143340.json`: `custom-h1-cascade-hideaway` (+ payouts, cleaning-supplies,
marketing, repairs-maintenance subs) and `income-custom-h1-cascade-hideaway` (+ airbnb-income) —
identical to the predicted ids. 21/130 markers flipped to "verified", income sub added. **Lloyd:**
push v37 from gas-v35 (21/130/147), then `set1EvidenceFill3Preview` → `Apply` → `categoryPass2Preview`
→ `Apply` (23 Cascade rows, ₱199k → H1). Income rows (inward AUB → …4647) get `INCOME:Cascade`
tag now; the income category id is wired for the rules ledger.

## Session 47, 14:43 PHT — block 3 APPLIED · SET 1 CLOSED
v37. `set1EvidenceFill3Apply` 23 → `categoryPass2Preview` decided 23 / errors 0 → `categoryPass2Apply`
decided 23 (EXPENSE 23, H1 Cascade Hideaway with subs), snapshot `20260904144304`. Read-back
(exec 21237): G2/blank unpaired spending **₱550,084 lifetime** (₱1,520,358 at 08:00 today);
Aug 2026 33 rows ₱52,384; transfer-shaped rows open = 1 (Maya 35k mirror, DEFER).
Finding for Set 2: month totals Nov 2025 → Jul 2026 are now **negative**, i.e. what is left in G2
for 2026 is mostly positive `statement_credit` rows (refunds / income wearing the G2 label), not
spending. Set 2 starts there, evidence-first (e-mail incoming_interbank_transfer records exist:
146 parsed).

## Session 47, 14:57 PHT — SET 2 block 1 APPLIED (inbound rows), v38 @41
The remaining "G2" was mostly money coming IN (196 positive rows). Matched to the PST
incoming-transfer confirmations: Asia United Bank …3350 = Airbnb payouts → `INCOME:Cascade` (79);
GSave withdrawals → `COUNTER:CIMB Gsave` (23); RCBC …8929 → `COUNTER:RCBC Hexa Debit` (5, ₱230k);
Maya / GrabPay …7727 → own; UB → GCash (11); interest & Bizlink payroll → `INCOME:Personal` (16);
one block-2 correction (GCash → UB 4,015 on 2025-12-30 is own: UB received +4,000 the same day).
Module 148 (`3f574bc`): `set2InboundBuildTab` 138 → preview decided 138 / errors 0 → **apply decided
138 (COUNTER 43, INCOME 95), snapshot `20260904145648`** — run by the agent under Lloyd's 14:45
authorization; clasp push + deploy also succeeded from the agent shell (@41).

### Set 2 — still yours (inbound, no evidence in ledger or e-mail)
| group | rows | ₱ | question |
|---|---|---|---|
| Own Bank …2767 → BPI Main (ELINK inter-bank) | 9 | 144,100 | Cascade distributions to you (INCOME:Cascade) or own-account moves? |
| Named senders: Belly Joe P Ligason 6,500 · Michael John A Lelim 47,000 · Conrad E Rigdaus 104,278 | 7 | 157,778 | family / loan repayment (Personal Loan - CONRAD account exists) / other? |
| ATM cash deposits BPI@Manila, Nov 2025 (20,000 · 27,000 · 12,800 · 50,000 · 43,145) | 5 | 152,945 | your own cash (COUNTER:Cash) or received cash (income tag)? |
| CIMB …4186 → BPI 50,000 (2025-08-18) | 1 | 50,000 | CIMB Gsave or Upsave (own)? |
| Other-bank senders: BDO …4395 46,000 (09-26) · RCBC …8017 45,000 · small RCBC 1,4xx–3,5xx · G-Xchange 3,000 · UB …5283 300 | 10 | 70,960 | guests / family / own? |
| No e-mail: Reversal-Annual/Memb Fee 1,550 · Shopee Ph credits · HSBC credits · GCash … | 14 | 137,256 | refunds (INCOME:Re-imburse)? |

## Session 47, 15:10 PHT — Set 2 block 2 APPLIED (v39 @42)
The 11 UB Debit "Received from ROCLOYD PINOS LIGASON" credits (₱262k) now carry counters
(10 × `COUNTER:BPI Main Account`, 1 × `COUNTER:GCash eWallet`); snapshot `20260904151041`.
They were positive `statement_credit` rows inside the spending scope, i.e. they had been
*reducing* reported spending. Their EXPORT_HOLD stamps stay.

## Session 47, 15:38 PHT — Set 2 block 3 + Set 5 owner rules APPLIED (runner 151, v44 @47)
- **Set 2 block 3** (Lloyd's six answers): 44 rows — INCOME 28 (Cascade: Own Bank …2767 + guests;
  Family: Belly Joe; Re-imburse: no-e-mail credits), COUNTER 16 (Cash deposits, Conrad loan
  account, CIMB Gsave, RCBC senders, BDO …4395 → Other Accounts). Snapshot `20260904153754`.
  Lelim loan (2 rows, 47,000) left DEFER until a liability account exists.
- **Set 5 owner rules** (149): 29 clusters → 112 rows (₱31,190.68): cafés → B1 > Drinks & Coffee;
  Jollibee → B1 > Dining Out & Ordered Food; hardware → E1 > Hardware & Repairs; Hetzner / Google
  Tacit → C3 > Ai & Productivity Tools; Google One → C3 > Cloud Storage; YouTube / Prime → C3 >
  Media & Streaming; buy-load (66) → C2 > Family Internet & Load - Bohol; Watsons → B2 > Personal
  Meds, Supplements & Pharmacy; card interest → G1 > Card Fees & Interest; withholding tax / debit
  memo / partner fee → G1 > Bank Transfer Fees; UBE Express → D1 > Public Transport.
  `categoryPass2Apply` decided 112, snapshot `20260904153807`.
- **Identifier index** (150, D-123): 40 rows mirrored to `_REF_AccountIdentifiers` — own accounts
  (UB 5788, RCBC 045868929, Maya/Grab 7727, GCash 8982, BPI 647/815/524, CIMB 4186/7523/5764, Own
  Bank 1871/3726, BDO 4395 closed, Bybit, Tiktok Paylater), family (627 Nanay, 5980, 3952 Papang,
  4850 Ate Kris, 142 Marif, Belly Joe), Cascade (AUB 3350 payouts, Own Bank 2767 owner,
  200000324 payout account, Honey, printing, Bria), counterparties (Northfield 069, Jake 639,
  Flordeth 591, Tumulak 334, Conrad, Lelim, Airbnb hosts).
- Pass-2 worksheet: applied 158 · pending 138 · deferred 13. **Still yours (one line each):**
  marketplaces default (TikTok 23 · Shopee 16 · Lazada 5 · Amazon 12 · Taobao 2 → D2 > Personal
  Effects or DEFER?); groceries (Robinsons Supt, Gaisano S/M, Gaisano Dep → Groceries - Family or
  - Personal?); department stores (Daiso, Muji, Mumuso, RDS, Rob Dept, R AND C, KCC → D2 >
  Personal Effects or B3 > Household Supplies?); PAL tickets 7,432 / Pal Airy 404 / Booking hotel
  1,915 → D1 > Airfare / E3 Career & Deployment?; Dubai Duty Free / Seajoy / PP*CODE → D2 >
  Personal Effects?; reconciliation placeholders (manual balance adjustment ×13, unaccounted plugs,
  "Edited on …", Closed Account, YNAB auto) → keep DEFER or exclude from burn?
