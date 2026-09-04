# Classification first principles — the decision ladder (Lloyd, 2026-09-04, session 47)

Lloyd's rules, verbatim intent, in the order they are applied. This is the contract every
engine, worksheet and questionnaire must follow. Anything that asks the owner a question the
ladder could have answered is a regression.

## The ladder

1. **Type first: income, transfer, or expense.** Nothing is categorised until it is typed.
2. **Transfer = the same single amount correlated between two of my own accounts** (not a
   sum, not a group), on the same date or within a few days. Correlation sources, in order:
   a. the other account's own rows in the ledger (statement, app export, e-mail-enriched);
   b. the bank's transfer confirmation e-mail (BPI_Online / UnionBank_Online tabs), which
      names the receiving bank, masked account and, for intra-BPI, the note;
   c. the receiving account's own history outside the ledger (screenshots / app history,
      dated, as in 117's evidence table).
   A row with no correlation in a, b or c is **not** a transfer. It is only then a payment
   or an expense. The question "which of five accounts?" is never asked while a source in
   a–c is unread.
3. **Income if inward** (positive, unpaired). Tag by sender: allotment/remittance, family,
   reimbursement, interest.
4. **Expense if outward** (negative, unpaired).
5. **Expense category from account + merchant + expense type**; the note, the establishment
   name and the counterparty name carry the context. Category and sub-category together.
6. **Patterns are established once and reused**, per account and per merchant family:
   - HSBC Live+ → mostly dining & restaurants (B1 > Dining Out) unless the merchant says otherwise
   - Grab → D1 Transport > TN Vs unless GrabFood / a food or drink establishment is named → B1
   - KCC, Robinsons Supermarket, Gaisano S/M, SM Supermarket → B1 > Groceries
   - Cafés (Starbucks, Bo's, Native Kopi, Oona, Downtown, Coffee Bar) → B1 > Drinks & Coffee
   - Citihardware, Wilcon, Handyman, "hardware" keywords → E1 > Hardware & Repairs (E4 if a durable)
   - "milk", diapers, school, allowance, family-linked accounts and recipients → E1 Family Support
   - Watsons, Mercury, Rose Pharmacy → B2 > Personal Meds & Pharmacy
   - Anthropic, OpenAI, Google One, Hetzner, YouTube Premium → C3 with the matching sub
   - Shopee / TikTok / Lazada → the basket decides (item classifier), keyword families by sub
   - Family-associated accounts and recipients: a maintained list (own-account registry has
     the own side; the family side needs the same treatment)

## Where the ladder is and is not implemented today (from the classification map)

| Rule | Implemented by | Gap |
|---|---|---|
| 1 type first | 115/116/118 typing, parsers | statement rows land typed by direction only |
| 2a ledger legs | 129 counter reconcile, 145 staging match | runs on demand, not nightly; ±5 d |
| 2b e-mail legs | 74/75/78 enrich | enriches note/counter of rows it recognises; InstaPay-out rows on BPI Main were NOT joined (merge_rule shows no EMAIL tag on the open lumps) |
| 2c external evidence | 117 `ALEX_EXTERNAL_TRANSFER_EVIDENCE` | code constant, four entries, developer-edited |
| 3 income tag | 115, 130 `INCOME:<tag>` | no sender→tag rules |
| 4 expense | default | — |
| 5 category + sub | 22 resolver (vote), 119 routes, 144 majority | rulings could not write subs until v35; 59 never calls the resolver |
| 6 patterns | 21 seed keywords, 119, 102, 124, 117 | eleven stores, none shared; account-level defaults (HSBC = dining) exist nowhere |

## What changes because of this (session 47 onward)

- No questionnaire card may be shown for a transfer-shaped row until sources 2a, 2b and 2c
  have been searched and the card states the result ("no leg in ledger; no confirmation
  e-mail; receiving account has no feed"). The Set 1 deck v1 violated this and is withdrawn.
- The rules ledger (D-121) carries rule 6 as data: `scope=account` rows (HSBC Live+ → B1 >
  Dining Out), `scope=merchant` rows, `scope=recipient` rows (family list), `scope=keyword`
  rows (milk → E1), each with evidence and an owner stamp.
- The nightly classify(row) applies the ladder in this order and stops at the first rule that
  answers; "no answer" produces an attention item that names which sources were checked.
