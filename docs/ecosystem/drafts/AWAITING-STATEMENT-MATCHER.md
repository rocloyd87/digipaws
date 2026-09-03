# Design — `awaiting_statement` matcher (session 46, 2026-09-03) — NOT BUILT

**Problem.** `hermesStagingApply()` (96_HermesStaging.js) appends approved captures only for
cash-class accounts (`HERMES_APPENDABLE_ACCOUNTS = ['Cash']`). Every other approved capture —
tg-328 TikTok on a card, tg-333 Cebu Pacific on BPI Credit Card — is marked
`awaiting_statement` and then **nothing ever consumes it**. The capture's evidence (receipt
photo, SMS text, category, payee) is lost to the ledger row that arrives later on the statement,
which lands with a bare merchant string and an empty category. D-017 is the reason the row is
never appended; this design is the missing second half: enrich the statement row when it lands.

**Non-goals.** No new ledger rows, no amount changes, no second source of truth for merges. The
statement merge (50_AlexMerge) stays the only thing that creates rows.

## Matching rule (pure, `hermesStagingMatch_(stagingRows, ledgerRows, H)`)

A staging row with `status = awaiting_statement` matches a ledger row when ALL hold:

| Field | Rule | Why |
|---|---|---|
| account | `parsed.account` = ledger `account_name` (registry name, via the card map) | statement rows belong to one account |
| amount | `cents(parsed.amount) == cents(signed_amount)` exactly | D-017 pattern: exact match only, no tolerance |
| date | ledger `date` within `[parsed.date − 1 d, parsed.date + 5 d]` | card posting lag (memory: ±3 d flags duplicates; statements post up to 5 d late, never earlier than the purchase minus a day) |
| uniqueness | exactly ONE ledger row satisfies the above AND exactly ONE staging row claims it | ambiguity is refused, never resolved (D-017's five excluded groups) |
| not already enriched | ledger `merge_rule` does not contain `HERMES_MATCH:` | idempotent |

Rows matched to nothing stay `awaiting_statement`; after **60 days** they are reported by
`get_attention` as "capture never arrived on a statement" (possible refund, cancelled auth, or
wrong account) — still never appended.

## What a match writes (I/O, `hermesStagingMatchApply()`)

On the ledger row, snapshot first (`alexSteadySnapshot_`), row found by `alex_id` never by
row number, current values re-asserted before writing, one mismatch aborts the batch:

1. `category_id` / `category_label` ← `parsed.category` **only if the ledger cell is blank or
   G2** (never overwrite an owner-set category).
2. `note` ← existing note + ` | hermes: <payee> | <raw_input> | evidence: <evidence_url>`
   (append only, one cell, same as 74/75 email enrich).
3. `merge_rule` ← existing + `|HERMES_MATCH:<idempotency_key>`.

On the staging row: `merged_at` = now, `merge_note` = `matched alex_id <id>`; status stays
`awaiting_statement` (the status vocabulary is not extended; `merged_at` is the terminal marker
the reader already filters on).

## Where it runs

Not on a schedule of its own. Call it at the END of `hermesStagingApply()` (already daily at
01:30 once `hermesStagingInstallTrigger()` is run) so every new statement import is swept the
next night. Preview (`hermesStagingMatchPreview()`) reports `{matched, ambiguous, unmatched,
stale60d}` and writes nothing.

## Tests to write first (`_tests/alex_hermes_staging_match.node.cjs`)

- exact-amount, same-account, +3 d → match; −2 d → no match; +6 d → no match.
- two ledger rows both qualify → `ambiguous`, nothing written.
- two staging rows claim one ledger row → `ambiguous`, nothing written.
- ledger row already `HERMES_MATCH:` → skipped.
- owner-set category is never overwritten; G2 and blank are.
- apply plan re-asserts the current note/category and aborts on drift.

## Open questions for Lloyd

1. Window `[−1, +5]` days — confirm against the HSBC/UB posting lag seen in BALANCE_CONTROL.
2. Should a matched capture also file the receipt link into the Drive vault note (W-INBOX-FILE),
   or is the ledger note enough?
