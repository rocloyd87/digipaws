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
