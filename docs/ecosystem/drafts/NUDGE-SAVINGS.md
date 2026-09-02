# Savings nudges — extension of `W-HERMES-NUDGE` (mobile draft, 2026-09-02)

**Status: DRAFT, UNVERIFIED on the machine.** Extends `W-HERMES-NUDGE — Threshold Alerts`
(`vJpvLLNtSKsQ1dcL`) and GAS `98_HermesNudges.js`. Do not create a second nudge workflow or any
`hermes_*` table. Advisory only: a nudge never writes MoneyMatter or stages anything.

## Numbers the rules are tuned against (live, 2026-09-02)

| Figure | Value | Source |
|---|---|---|
| Liquid | ₱401,220 | get_kpis (exec 20780) |
| Floor reserve (G2, 12 × essential month) | ₱863,601 | get_kpis `floorReserve` |
| Essential month (A1–C2 median) | ₱71,967 | get_kpis `floorBasis` |
| Floor gap (safe-to-spend) | −₱462,380 | get_kpis `safeToSpend` |
| Total burn median | ≈₱208,000/mo | session 43 |
| Runway | 1.87 months | get_kpis |
| Top burn driver | "G2 Review / Uncategorized" ₱112k/mo | first W-DAILY-BRIEF |
| Average monthly income, last 6 complete months | ₱493,729 | MoneyMatter `get_subscriptions_summary` (read 2026-09-02) |

The last row is the lever: income ≈ 494k against ≈ 208k burn leaves ≈ 285k/month of surplus.
Swept in full, the ₱462k floor gap closes in **two paydays**. The nudges below exist to make
that sweep happen and to stop the month from eating it first.

## Rules

Priority order when several fire on the same day: N1 › N2 › N3 › N5 › N4. **At most one nudge
per day**, none between 22:00 and 07:00 PHT, none within 30 minutes of the 07:00 brief.
Evaluate at 12:30 and 19:30 PHT (add these to the existing W-HERMES-NUDGE schedule).

| ID | Name | Fires when | Data | Cooldown | Severity |
|---|---|---|---|---|---|
| N1 | Income landed → sweep | liquid rose ≥ ₱100,000 since yesterday's snapshot (allotment / contract pay) | liquid (today, yesterday from `W-SNAPSHOT-REFRESH` store), essential month, recurring due ≤30 d, floor gap | 20 days | 💰 |
| N2 | Month pace | day ≥ 7 and MTD spend > pace × 1.15, where pace = burnMedian × dayOfMonth / daysInMonth; escalates once at × 1.30 | mtdSpend, burnMedian, burn drivers | 72 h (escalation once per month) | 🟠 / 🔴 |
| N3 | Runway floor | runway < 2.0 months (< 1.5 = red) | runwayMonths, last week's value | 7 days, Mondays after the 08:00 advisory | 🔴 |
| N4 | Floor milestone | floor gap crosses a ₱50k step downward (450k, 400k, …) or turns positive | safeToSpend, last milestone | once per milestone | 🟢 |
| N5 | Uncategorized drag | uncategorized share of MTD burn > 25 % | mtdUncategorized / mtdSpend | 7 days | 🧹 |

Suggested sweep for N1: `sweep = max(0, inflow − essentialMonth − recurringDue30d)`; the message
names the amount and the gap after it, but the transfer stays a Lloyd action.

## Messages (Telegram HTML, one screen each)

```
💰 <b>Income landed</b> +₱{inflow}
Floor gap ₱{gap} · liquid now ₱{liquid}
Suggested sweep <b>₱{sweep}</b> → floor
(keeps ₱{essentialMonth} essential month + ₱{recurring30} due ≤30 d)
Gap after sweep ₱{gapAfter} · {paydaysLeft} more payday(s) to fund
```

```
🟠 <b>Pace</b> · day {day}/{days}
₱{mtd} spent — {pctAhead}% ahead of your usual pace (₱{pace})
On this path the month ends ≈ ₱{projected} (median ₱{burnMedian})
Top driver: {driver1} ₱{driver1Amt}
```

```
🔴 <b>Runway {runway} mo</b> ({delta} vs last week)
Liquid ₱{liquid} · burn ₱{burnMedian}/mo
Floor still ₱{gap} short — next income {nextIncome}
```

```
🟢 <b>Floor gap under ₱{milestone}</b> · {pctFunded}% funded
₱{gap} to go at today's essential month ₱{essentialMonth}
```

```
🧹 ₱{uncat} ({uncatPct}%) of this month is uncategorized
Every KPI above is distorted by it — accept-all on the category rulings worksheet fixes it.
```

## What get_kpis already gives vs what GAS must add

| Field | Status | Note |
|---|---|---|
| `safeToSpend`, `floorReserve`, `floorBasis`, `liquid`, `runwayMonths`, `burnMedian`, burn drivers, budget left, recurring due ≤7 d, next income | exist (session 43) | used as-is |
| `mtdSpend`, `mtdUncategorized`, `daysElapsed`, `daysInMonth` | **add** to `metricsTier1_` or a small `metricsMonthPace_` helper | cheap: month-to-date sums over the ledger |
| `recurringDue30d` | **add** (same recurrence source as the 7-day figure, wider window) | for the N1 sweep |
| `liquidPrev` (yesterday) | from the `W-SNAPSHOT-REFRESH — KPI Store` data table, not GAS | day-over-day delta; if the store lacks a row, N1 is skipped, never guessed |

State: extend whatever `98_HermesNudges.js` already persists (Script Properties or its tab) with
`nudge:<id>:lastFiredAt`, `nudge:N2:escalatedMonth`, `nudge:N4:lastMilestone`. Nothing new in
Supabase.

## Rule sketch for `98_HermesNudges.js` (V8 GAS, pure function — unit-testable)

```javascript
/**
 * Evaluate the savings nudges. Pure: no I/O, no writes.
 * @param {Object} k   get_kpis payload + mtdSpend, mtdUncategorized, daysElapsed, daysInMonth, recurringDue30d, liquidPrev
 * @param {Object} st  { 'nudge:N1:lastFiredAt': iso, ..., 'nudge:N4:lastMilestone': number, 'nudge:N2:escalatedMonth': 'yyyy-MM' }
 * @param {Date}   now
 * @returns {Object|null} { id, severity, text, stateUpdates }
 */
function hermesNudgeSavingsRules_(k, st, now) {
  var H = 3600000, D = 24 * H;
  var cooled = function (id, ms) { var t = st['nudge:' + id + ':lastFiredAt']; return !t || (now - new Date(t)) >= ms; };
  var peso = function (n) { return '₱' + Math.round(n).toLocaleString('en-PH'); };
  var gap = Math.max(0, -(k.safeToSpend || 0));
  var out = [];

  // N1 — income landed
  if (k.liquidPrev != null && k.liquid - k.liquidPrev >= 100000 && cooled('N1', 20 * D)) {
    var inflow = k.liquid - k.liquidPrev;
    var sweep = Math.max(0, inflow - (k.floorBasis || 0) - (k.recurringDue30d || 0));
    var after = Math.max(0, gap - sweep);
    out.push({ id: 'N1', severity: 'info', text: '💰 <b>Income landed</b> +' + peso(inflow) + '\nFloor gap ' + peso(gap) + ' · liquid now ' + peso(k.liquid) +
      '\nSuggested sweep <b>' + peso(sweep) + '</b> → floor\n(keeps ' + peso(k.floorBasis) + ' essential month + ' + peso(k.recurringDue30d || 0) + ' due ≤30 d)' +
      '\nGap after sweep ' + peso(after) + (sweep > 0 ? ' · ' + Math.ceil(after / sweep) + ' more payday(s) to fund' : '') });
  }
  // N2 — pace
  if (k.daysElapsed >= 7 && k.burnMedian > 0 && cooled('N2', 72 * H)) {
    var pace = k.burnMedian * k.daysElapsed / k.daysInMonth, ratio = k.mtdSpend / pace;
    var month = Utilities.formatDate(now, 'Asia/Manila', 'yyyy-MM');
    var red = ratio >= 1.30 && st['nudge:N2:escalatedMonth'] !== month;
    if (ratio >= 1.15) {
      var d = (k.burnDrivers || [])[0] || {};
      out.push({ id: 'N2', severity: red ? 'alert' : 'warn', stateUpdates: red ? { 'nudge:N2:escalatedMonth': month } : {},
        text: (red ? '🔴' : '🟠') + ' <b>Pace</b> · day ' + k.daysElapsed + '/' + k.daysInMonth + '\n' + peso(k.mtdSpend) + ' spent — ' + Math.round((ratio - 1) * 100) +
          '% ahead of your usual pace (' + peso(pace) + ')\nOn this path the month ends ≈ ' + peso(k.mtdSpend / k.daysElapsed * k.daysInMonth) + ' (median ' + peso(k.burnMedian) + ')' +
          (d.name ? '\nTop driver: ' + d.name + ' ' + peso(d.amount || 0) : '') });
    }
  }
  // N3 — runway (Mondays only, after the advisory)
  if (k.runwayMonths != null && k.runwayMonths < 2.0 && now.getDay() === 1 && cooled('N3', 7 * D)) {
    out.push({ id: 'N3', severity: k.runwayMonths < 1.5 ? 'alert' : 'warn', text: '🔴 <b>Runway ' + k.runwayMonths.toFixed(2) + ' mo</b>' +
      (k.runwayPrevWeek != null ? ' (' + (k.runwayMonths - k.runwayPrevWeek >= 0 ? '+' : '') + (k.runwayMonths - k.runwayPrevWeek).toFixed(2) + ' vs last week)' : '') +
      '\nLiquid ' + peso(k.liquid) + ' · burn ' + peso(k.burnMedian) + '/mo\nFloor still ' + peso(gap) + ' short' + (k.nextIncome ? ' — next income ' + k.nextIncome : '') });
  }
  // N5 — uncategorized drag
  if (k.mtdSpend > 0 && k.mtdUncategorized / k.mtdSpend > 0.25 && cooled('N5', 7 * D)) {
    out.push({ id: 'N5', severity: 'info', text: '🧹 ' + peso(k.mtdUncategorized) + ' (' + Math.round(k.mtdUncategorized / k.mtdSpend * 100) +
      '%) of this month is uncategorized\nEvery KPI above is distorted by it — accept-all on the category rulings worksheet fixes it.' });
  }
  // N4 — milestone (lowest priority, positive)
  var step = Math.floor(gap / 50000) * 50000, last = st['nudge:N4:lastMilestone'];
  if (last != null && step < last) {
    out.push({ id: 'N4', severity: 'good', stateUpdates: { 'nudge:N4:lastMilestone': step },
      text: '🟢 <b>Floor gap under ' + peso(step + 50000) + '</b> · ' + Math.round((1 - gap / k.floorReserve) * 100) + '% funded\n' + peso(gap) + ' to go at today\'s essential month ' + peso(k.floorBasis) });
  } else if (last == null) {
    out.push({ id: 'N4-init', severity: 'silent', stateUpdates: { 'nudge:N4:lastMilestone': step } }); // seed state, send nothing
  }

  var order = { N1: 0, N2: 1, N3: 2, N5: 3, N4: 4 };
  out.sort(function (a, b) { return (order[a.id] == null ? 9 : order[a.id]) - (order[b.id] == null ? 9 : order[b.id]); });
  var pick = out.filter(function (n) { return n.severity !== 'silent'; })[0] || null;
  if (pick) pick.stateUpdates = Object.assign({}, pick.stateUpdates || {}, (function (o) { o['nudge:' + pick.id + ':lastFiredAt'] = now.toISOString(); return o; })({}));
  return pick;
}
```

Wire-up in W-HERMES-NUDGE: the existing tool call returns the chosen nudge (or null); the
workflow sends `text` when non-null and writes `stateUpdates` back through the same GAS
dispatcher. Golden tests to add next to `_tests/hermes_nudges`: (a) inflow 250k on gap 462k →
N1 with sweep 250k − 71,967 − recurring; (b) day 10, MTD 90k on 208k median → N2 at ratio 1.30
→ red once, orange after; (c) N4 never fires on its first run.

## FMP price alerts — see `W-FMP-ALERTS.json`

Findings from the FMP connector on this plan (2026-09-02): the real-time `quote` endpoints are
**Premium-gated** (access denied), `historical-price-eod-light` works, and **PSE tickers are not on
FMP** (only OTC ADRs such as SVTMF / ICTEF; FMP's "PSE" is Prague). The draft is therefore an
end-of-day workflow: US symbols via FMP EOD light, PSE symbols via GOOGLEFINANCE columns in a new
`_HERMES_WATCHLIST` tab of the Alfred sheet; rules above / below / pct_day / breakout_52w /
drawdown_from_high with per-row cooldowns; one Telegram card only when something fires. The
Read Me sticky inside the JSON carries the tab layout, formulas and credential names.
