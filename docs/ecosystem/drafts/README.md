# drafts/ — mobile-lane output (session 44) and its desktop disposition (session 45)

**Desktop status, 2026-09-02 night (session 45):**

| Draft | Disposition |
|---|---|
| `W-DAILY-BRIEF-v2.json` | **Partially applied.** The `spend_series` "yesterday" source was spliced straight into the live brief (`Fetch Spend Series` → `Format Brief`, validated). TickTick tasks (no OAuth2 credential yet) and MoneyMatter subscriptions (0 defined) sections NOT applied. Not imported as a scratch workflow. |
| `COMMANDS.md` | **`/sub` and `/remind` applied** live in W-HERMES (deterministic routes, validated, one Telegram test each still owed). `/todo` (TickTick credential) and `/cascade` (Airbnb iCal credential) NOT applied. |
| `NUDGE-SAVINGS.md` | **Applied in GAS** (`hermesNudgeSavingsRules_`, `get_kpis.monthPace` / `recurringDue30d`, 12/12 tests, commit `ad00d50`) — live after the v28 push; `W-HERMES-NUDGE` schedule now 12:30 + 19:30. |
| `W-FMP-ALERTS.json` | **Imported INACTIVE** as `W-FMP-ALERTS — Price Alerts` (`X2bAv2WXOOZJ3pP6`) with GSheets + Telegram credentials set and the owner chat id filled; GAS `hermesWatchlistEnsure()` (142, v28) creates the tab. Lloyd: FMP `apikey` credential → select on `FMP EOD Light` → test-run → activate. |
| `W-FEED-PROBE.json` | **Superseded** — the probe was run from the desktop (open egress) instead; verdicts in `../miniflux-homelab.md`. Keep for a re-run from the Pi/VPS if ever needed. |

Original mobile-lane notes follow.

Everything here was written away from the desktop (claude.ai Code, no n8n / clasp / GAS test
access). **Nothing in this folder has run on the machine.** The desktop session imports, tests,
splices, and then either deletes the draft or marks it applied here.

| Draft | For | What it is | Local check done | Desktop must |
|---|---|---|---|---|
| `W-DAILY-BRIEF-v2.json` | `W-DAILY-BRIEF` (`Cu6opCPfQPHJMKRJ`) | Three new brief sources — TickTick open tasks, MoneyMatter subscriptions ≤7 d, yesterday's spend vs 30-day average — with the TickTick credential setup steps in the Read Me sticky | Code bodies run with real Luxon against happy / all-failed / empty fixtures; JSON validated | Create the TickTick OAuth2 credential; confirm the MoneyMatter REST path + auth from W-DASH-SYNC; confirm the tool-bridge input names; test, then splice into the live brief |
| `COMMANDS.md` | `W-HERMES` (`Diz990QbM3cZYCKp`) | `/remind` → Google Calendar, `/todo` → TickTick, `/cascade` → Airbnb iCal card, `/sub` → `subscriptions_audit`; IF-node + Code + tool + reply snippets | 12 `/remind` phrasings, `/todo` tokens, iCal parse, `/sub` formatter all run locally (table inside) | Patch the four IF routes after `Is Inbox Note`; verify each with one Telegram message; BotFather command list |
| `NUDGE-SAVINGS.md` | `W-HERMES-NUDGE` (`vJpvLLNtSKsQ1dcL`) + GAS `98_HermesNudges.js` | Five nudge rules (income-landed sweep, month pace, runway, floor milestone, uncategorized drag) with thresholds, messages, needed get_kpis fields and a pure-function GAS rule sketch | none (design + code sketch) | Add `mtdSpend`/`mtdUncategorized`/`daysElapsed`/`recurringDue30d` to get_kpis; port the rule function with golden tests; extend the nudge schedule |
| `W-FMP-ALERTS.json` | new workflow | End-of-day price alerts: US via FMP EOD light, PSE via GOOGLEFINANCE columns in a new `_HERMES_WATCHLIST` tab; rules above / below / pct_day / breakout_52w / drawdown_from_high; cooldowns; sheet write-back | Evaluate + normalize bodies run locally (5 alert types, cooldown, failed fetch); JSON validated | Create the tab + formulas; FMP `apikey` Query Auth credential; confirm Google Sheets node field names; seed two US + two PSE rows and run once |
| `W-FEED-PROBE.json` | one-shot | Fetches the six Miniflux feeds + Google News + fallbacks and posts verdicts to Telegram | JSON validated, code bodies parsed | Run once from n8n (open egress), paste verdicts into `miniflux-homelab.md`, delete |

Facts established from the connectors on 2026-09-02 (these are real reads, not guesses):

- MoneyMatter has **0 subscriptions, 0 subscription candidates** (`get_subscriptions`,
  `get_upcoming_subscription_payments`, `list_subscription_candidates` all `[]`). Average
  monthly income over the last 6 complete months is ₱493,729 (`get_subscriptions_summary`).
- TickTick has two tags (`ecosystem`, `finance`) and four open `#ecosystem` tasks, all in the
  Inbox project (`inbox132802495`).
- Google Calendar primary is `rocloyd87@gmail.com`, Asia/Manila.
- FMP on this plan: `quote` endpoints denied (Premium+), `historical-price-eod-light` works, no
  PSE listings (only OTC ADRs; FMP's `PSE` exchange code is Prague).
- The claude.ai Code sandbox cannot reach any news site or `budget.rocloyd.com` directly
  (egress proxy 403); connectors are the only live path.

Branch note: the claude.ai session assigned the branch name `claude/mobile-prompt-docs-4nkoff`
instead of the `mobile/<date>-<topic>` pattern in `CONTINUE.md`; treat it as the session-44
mobile PR.
