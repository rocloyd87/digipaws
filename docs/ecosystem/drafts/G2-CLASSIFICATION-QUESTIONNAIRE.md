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
