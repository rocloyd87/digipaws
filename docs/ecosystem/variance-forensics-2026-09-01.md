# Variance forensics — GCash & BPI USD (2026-09-01)

**Scope:** Reconcile two balance variances between Tarsi (phone backup, exported 2026-09-01) and MoneyMatter (budget.rocloyd.com), period June–September 2026.

| Account | Tarsi | MoneyMatter | Variance |
|---|---:|---:|---:|
| GCash eWallet / GCash | PHP 380.10 | PHP 295.01 | Tarsi +85.09 |
| BPI US Dollar Account | USD 0.31 | USD 3.00 | Tarsi −2.69 |

> **Data limitation:** The MoneyMatter MCP server was unreachable for this entire session (every call returned `-32000 Invalid or expired session ID`, retried over ~20 minutes). The MoneyMatter transaction lists could NOT be pulled, so no line-by-line diff against MoneyMatter was possible. All findings below come from the Tarsi backup, which nevertheless contains decisive anchor evidence for both variances. Re-run the MoneyMatter pull (search_transactions, accountIds `01a05243-e8fc-7688-b396-e5cb0f5077ee` / `01a0589b-11e6-746d-acd0-552c2857f820`, startDate 2026-06-01, include transfers) once the connector session is restored to confirm the recommended fixes.

---

## 1. GCash — Tarsi 380.10 vs MoneyMatter 295.01 (Δ 85.09)

### Tarsi transaction history, Jun–Sep 2026 (account `ms0qfyw6asjphf`)

| Date | Type | Amount (PHP) | Note / detail | Tarsi ID |
|---|---|---:|---|---|
| 2026-06-29 | Expense | −101.00 | Buy Load for 09171348982 (uncategorized) | alx-gcash-1705d164801e74d72bf9 |
| 2026-07-21 | Transfer in | +2,000.00 | from BPI Main Account | alx-d31580ec0dc007015f1ad980 |
| 2026-07-21 | Transfer in | +2,000.00 | from BPI Main Account — **duplicate, see F2** | alx-gcash-712b2685bbf582a734af |
| 2026-08-04 | Expense | −381.00 | Buy Load for 09171348982 | alx-gcash-b2dcce687be1e945ffa4 |
| 2026-08-07 | Balance adjustment | sets 380.10 | previousBalance 0 → nextBalance 380.10, id prefix `alx-verified-` (verified balance check) | alx-verified-97ae5a17deb4d1b1 |
| 2026-09-01 | Balance adjustment | Δ 0.00 | "Alex import baseline: balance as of 2026-09-01", 380.10 → 380.10 | alx-baseline-ms0qfyw6asjphf |

### Findings

- **F1 — Tarsi's 380.10 is anchored, not computed.** A *verified* adjustment on 2026-08-07 pinned the balance at 380.10, and the fresh 2026-09-01 baseline re-confirmed 380.10 with zero delta. Tarsi records **no transactions at all after 2026-08-07** (net movement after that date = 0.00). So 380.10 reflects an actual observed GCash app balance on 08-07, still asserted as of today.
- **F2 — Duplicate ₱2,000 transfer-in on 2026-07-21.** Two identical transfers (BPI Main → GCash, same day, same amount) exist with different import lineages: one created 07-21 (BPI-side import, `alx-` id) and one bulk-imported 2026-08-28 (`alx-gcash-` id). This duplicate does **not** affect Tarsi's final balance (the 08-07 verified adjustment supersedes it) but should be deleted for a clean ledger, and explains why the 08-07 adjustment shows a large implicit correction.
- **F3 — No exact 85.09 decomposition found in Tarsi.** No Tarsi transaction or combination in the window equals 85.09. Because Tarsi is flat after 08-07 while MoneyMatter sits 85.09 lower, the discrepancy must live on the MoneyMatter side: either (a) MoneyMatter contains ~85.09 of expenses dated after 2026-08-07 that Tarsi never captured (plausible if MoneyMatter auto-imports GCash activity Tarsi missed), or (b) MoneyMatter is missing ~85.09 of credits (cashback/GSave interest/top-up) that never got entered in either system's transaction list but is embedded in Tarsi's verified 380.10.

### Verdict — GCash

**Tarsi (380.10) is more likely correct as of 2026-08-07**, because it is a verified, observed balance; MoneyMatter's 295.01 is a computed ledger balance. **However**, if any real GCash spending occurred after 2026-08-07, Tarsi would not know about it — and MoneyMatter's lower figure would then be the truer one. Confidence: **medium** (blocked from full confirmation by the MoneyMatter outage).

**Recommended fix:**
1. Check the live GCash app balance. If it shows ~380.10 → add a reconciling credit of **₱85.09** to MoneyMatter GCash (or run a balance adjustment to 380.10), dated 2026-08-07, noted "Reconciliation vs Tarsi verified balance".
2. If the app shows ~295.01 → MoneyMatter is right; the missing piece is one or more post-08-07 GCash expenses totaling 85.09 that must be added to Tarsi (pull MoneyMatter GCash transactions dated 2026-08-08 onward to identify them — this was the intended diff blocked by the outage).
3. Independently, delete one of the duplicate ₱2,000 transfers of 2026-07-21 in Tarsi (keep `alx-d31580ec0dc007015f1ad980`, delete `alx-gcash-712b2685bbf582a734af`).

---

## 2. BPI US Dollar Account — Tarsi 0.31 vs MoneyMatter 3.00 (Δ 2.69)

### Tarsi transaction history, Jun–Sep 2026 (account `msbrj0gz14jf8u`)

| Date | Type | Amount (USD) | Note / detail | Tarsi ID |
|---|---|---:|---|---|
| 2026-08-02 | Balance adjustment | sets 3.00 | previousBalance 0 → nextBalance 3.00, manual (`kind: adjustment`) | msbrmyht4ibygq |
| 2026-09-01 | Balance adjustment | Δ 0.00 | "Alex one-time **issuer balance cutoff 2026-08-11**; closing balance refreshed 2026-09-01", 0.31 → 0.31 | alx-baseline-msbrj0gz14jf8u-20260811 |

No expenses, incomes, or transfers touch this account in the entire window (the only other records ever are two historical incomes from 2024–2025).

### Findings

- **F4 — The 3.00 and the 0.31 are the same story, two snapshots.** The manual adjustment of 2026-08-02 set the balance to a round **$3.00** — and MoneyMatter's balance is exactly that 3.00, i.e. MoneyMatter still carries the 08-02 manual figure. Nine days later, the issuer (BPI) reported **$0.31** at the 2026-08-11 balance cutoff, and that issuer figure was refreshed and re-confirmed 2026-09-01.
- **F5 — No transaction explains the −2.69 in either system.** The $2.69 drop between 08-02 and 08-11 has no recorded expense. A **$2.69 debit is characteristic of a BPI dollar-account service/maintenance or dormancy-related fee** (or the 08-02 "3.00" was simply a rounded manual estimate of a true ~0.31 balance). Either way the bank-reported 0.31 supersedes the manual round number.

### Verdict — BPI USD

**Tarsi (0.31) is correct** — it is issuer-sourced (BPI balance cutoff 2026-08-11, refreshed 2026-09-01), while MoneyMatter's 3.00 traces to a manual round-number adjustment from 2026-08-02. Confidence: **high**.

**Recommended fix:** In MoneyMatter, add a **USD 2.69 expense dated 2026-08-11** on account `01a0589b-11e6-746d-acd0-552c2857f820`, categorized Bank Fees, noted "BPI USD service charge / reconciliation to issuer cutoff balance 0.31" — or, if a fee cannot be confirmed on the BPI statement, apply a balance adjustment to 0.31 with the same note. (Not performed — MoneyMatter access is read-only for this exercise, and the server was down.)

---

## Summary of candidate explaining transactions

| # | Account | Candidate | Amount | Present in | Missing from | Action |
|---|---|---|---:|---|---|---|
| 1 | GCash | Duplicate transfer BPI Main → GCash 2026-07-21 | ₱2,000.00 | Tarsi (twice) | — | Delete duplicate `alx-gcash-712b2685…` in Tarsi |
| 2 | GCash | Unidentified post-2026-08-07 delta (no exact match — Tarsi has zero activity after 08-07) | ₱85.09 | MoneyMatter side (as lower balance) | Tarsi | Verify live GCash balance, then reconcile per verdict |
| 3 | BPI USD | Implied bank fee / manual-estimate correction between 08-02 and 08-11 cutoff | $2.69 | Neither (implied by issuer cutoff) | Both | Add $2.69 fee (or adjust to 0.31) in MoneyMatter |

**Follow-up required:** restore the budget.rocloyd.com connector session and re-run the two `search_transactions` pulls to (a) enumerate MoneyMatter GCash activity after 2026-08-07 and close finding F3, and (b) confirm no USD transactions exist in MoneyMatter for the BPI account.

*Prepared 2026-09-01 from Tarsi backup `tarsi-fresh.json` (exported 2026-09-01).*
