# Hermes command routes — `/remind` `/todo` `/cascade` `/sub` (mobile draft, 2026-09-02)

**Status: DRAFT, UNVERIFIED on the machine.** Written on the mobile lane without n8n access. The
desktop session patches these into `W-HERMES — Financial Assistant` (`Diz990QbM3cZYCKp`) and
verifies each route with one live Telegram message. The Code-node bodies below were run locally
against real Luxon with stubbed n8n globals (see the `/remind` test table), but not inside n8n.

## Pattern (same as `/spend` → stats card)

Every command is a **pre-agent, deterministic route**: one IF node on the Telegram text, one
tool call, one HTML reply. No LLM unless the message cannot be parsed (only `/remind` can hit
that, and it degrades to a stated assumption rather than an LLM call).

```
Telegram Trigger → Owner Allowlist → Is Stats Command → Is Inbox Note
   → Is Remind Command  → Parse Remind → Create Calendar Event → Reply
   → Is Todo Command    → Build Todo   → TickTick Create Task   → Reply
   → Is Cascade Command → Fetch Airbnb iCal → Parse iCal        → Reply
   → Is Sub Command     → Call subscriptions_audit → Format Sub → Reply
   → (false on all)     → existing agent path
```

Chain the IF nodes exactly like `Is Stats Command` is chained today (true → route, false → next
IF). Node names referenced by the Code bodies: **`Telegram Trigger`** (rename the reference if
the live trigger node has another name). Every route reads `message.text`, replies to
`message.chat.id`, and never touches MoneyMatter.

### Shared snippets

IF node (replace the regex per command; the regex operator is the same one `Is Stats Command`
uses — mirror its exact `options` block when patching):

```json
{
  "name": "Is Remind Command",
  "type": "n8n-nodes-base.if",
  "typeVersion": 2.2,
  "parameters": {
    "conditions": {
      "options": { "caseSensitive": false, "leftValue": "", "typeValidation": "loose", "version": 2 },
      "combinator": "and",
      "conditions": [
        {
          "id": "cmd",
          "leftValue": "={{ $json.message?.text || '' }}",
          "rightValue": "/^\\/remind(@\\w+)?(\\s|$)/i",
          "operator": { "type": "string", "operation": "regex" }
        }
      ]
    },
    "options": {}
  }
}
```

Regexes: `/^\/remind(@\w+)?(\s|$)/i` · `/^\/todo(@\w+)?(\s|$)/i` · `/^\/cascade(@\w+)?(\s|$)/i` ·
`/^\/sub(s|scriptions)?(@\w+)?(\s|$)/i`. Also add the four commands to the bot's command list via
BotFather so they autocomplete.

Reply node (all four routes):

```json
{
  "name": "Reply Remind",
  "type": "n8n-nodes-base.telegram",
  "typeVersion": 1.2,
  "parameters": {
    "chatId": "={{ $('Telegram Trigger').first().json.message.chat.id }}",
    "text": "={{ $json.reply }}",
    "additionalFields": {
      "parse_mode": "HTML",
      "appendAttribution": false,
      "disable_web_page_preview": true,
      "reply_to_message_id": "={{ $('Telegram Trigger').first().json.message.message_id }}"
    }
  },
  "credentials": { "telegramApi": { "id": "", "name": "Telegram Hermes" } }
}
```

---

## 1 · `/remind <what> <when>` → Google Calendar event

**Credential exists:** `GCalendar - Rocloyd87@gmail.com` (`4q1Dm9DBfArZD2sA`). Calendar:
`rocloyd87@gmail.com` (primary, Asia/Manila — confirmed via the Calendar connector).

Flow: `Is Remind Command` → `Parse Remind` (Code) → IF `ok` → `Create Calendar Event` →
`Build Remind Reply` (Set) → `Reply Remind`. `ok=false` → reply with the usage line.

**Grammar** (deterministic, tail-anchored; title is everything before the matched tail):

| form | example |
|---|---|
| ISO date, optional time | `pay Meralco 2026-09-10 18:00` · `submit crew list 2026-09-05` (09:00 assumed) |
| relative | `call BPI in 2h` · `in 20m` · `in 3d` · `in 1w` |
| day word + optional time | `renew SIRB tomorrow 9am` · `gym tonight` (20:00) · `allotment check sat` · `dentist next mon 3.30pm` |
| time only | `standby call at 6pm` (today if still ahead, else tomorrow) |
| nothing matched | tomorrow 09:00, reply says "(assumed time)" |

Local test run (real Luxon, stubbed trigger, fixed clock):

| message (now = Wed 2 Sep 2026 14:00 PHT) | title | when | how |
|---|---|---|---|
| `/remind renew SIRB tomorrow 9am` | renew SIRB | Thu 3 Sep 09:00 | dayword |
| `/remind call BPI in 2h` | call BPI | Wed 2 Sep 16:00 | relative |
| `/remind pay Meralco 2026-09-10 18:00` | pay Meralco | Thu 10 Sep 18:00 | iso |
| `/remind submit crew list 2026-09-05` | submit crew list | Sat 5 Sep 09:00 | iso (assumed time) |
| `/remind gym tonight` | gym | Wed 2 Sep 20:00 | dayword (assumed time) |
| `/remind allotment check sat` | allotment check | Sat 5 Sep 09:00 | dayword (assumed time) |
| `/remind allotment check wed 10:30` | allotment check | Wed 9 Sep 10:30 | dayword |
| `/remind standby call at 6pm` | standby call | Wed 2 Sep 18:00 | at |
| `/remind standby call at 1pm` | standby call | Thu 3 Sep 13:00 | at |
| `/remind buy milk` | buy milk | Thu 3 Sep 09:00 | default (assumed time) |
| `/remind@Alexander_Hermis_Bot dentist next mon 3.30pm` | dentist | Mon 7 Sep 15:30 | dayword |
| `/remind` | usage reply | — | — |

`Parse Remind` (Code node, typeVersion 2):

```javascript
// Parse /remind <text> <when> deterministically (no LLM). Asia/Manila. Luxon DateTime is a Code-node global.
// Grammar, tried in order on the TAIL of the message:
//   1. 2026-09-05 14:30 | 2026-09-05 2pm | 2026-09-05
//   2. in 20m | in 2h | in 3d | in 1w | in 90 min
//   3. today|tonight|tomorrow|tmr|mon..sun|next mon  [at] 9 | 9am | 09:30 | 9.30pm
//   4. at 6pm | at 18:00                      (today if still ahead, else tomorrow)
//   5. nothing matched -> tomorrow 09:00, flagged assumed=true
// Everything before the matched tail is the reminder title.
const TZ = 'Asia/Manila';
const msg = $('Telegram Trigger').first().json.message;
const raw = String(msg.text || '').replace(/^\/remind(@\w+)?\s*/i, '').trim();
const now = DateTime.now().setZone(TZ);

if (!raw) {
  return [{ json: { ok: false, reply: 'Usage: /remind <what> <when>\ne.g. /remind renew SIRB tomorrow 9am · /remind call BPI in 2h · /remind pay Meralco 2026-09-10 18:00' } }];
}

const DOW = { mon: 1, tue: 2, wed: 3, thu: 4, fri: 5, sat: 6, sun: 7 };
const clock = (h, m, ap) => {
  let hh = Number(h);
  const mm = Number(m || 0);
  if (ap) { ap = ap.toLowerCase(); if (ap === 'pm' && hh < 12) hh += 12; if (ap === 'am' && hh === 12) hh = 0; }
  return { hour: hh, minute: mm, second: 0, millisecond: 0 };
};
const TIME = '(\\d{1,2})(?:[:.](\\d{2}))?\\s*(am|pm)?';

let title = raw, start = null, assumed = false, how = '';
let m;

if ((m = raw.match(new RegExp(`^(.*?)\\s*(\\d{4}-\\d{2}-\\d{2})(?:\\s+${TIME})?$`, 'i')))) {
  const d = DateTime.fromISO(m[2], { zone: TZ });
  if (d.isValid) {
    start = m[3] ? d.set(clock(m[3], m[4], m[5])) : d.set({ hour: 9 });
    assumed = !m[3]; title = m[1]; how = 'iso';
  }
}
if (!start && (m = raw.match(/^(.*?)\s+in\s+(\d+)\s*(min|mins|minute|minutes|m|h|hr|hrs|hour|hours|d|day|days|w|wk|week|weeks)$/i))) {
  const n = Number(m[2]); const u = m[3].toLowerCase()[0];
  start = now.plus(u === 'm' ? { minutes: n } : u === 'h' ? { hours: n } : u === 'd' ? { days: n } : { weeks: n });
  title = m[1]; how = 'relative';
}
if (!start && (m = raw.match(new RegExp(`^(.*?)\\s+(today|tonight|tomorrow|tmr|(?:next\\s+)?(mon|tue|wed|thu|fri|sat|sun)[a-z]*)(?:\\s+at)?(?:\\s+${TIME})?$`, 'i')))) {
  const word = m[2].toLowerCase();
  let day = now.startOf('day');
  if (word.startsWith('tomorrow') || word === 'tmr') day = day.plus({ days: 1 });
  else if (m[3]) {
    // bare or "next" weekday = the coming occurrence, 1–7 days ahead (same weekday today → +7)
    const target = DOW[m[3].toLowerCase()];
    let delta = (target - now.weekday + 7) % 7;
    if (delta === 0) delta = 7;
    day = day.plus({ days: delta });
  }
  const t = m[4] ? clock(m[4], m[5], m[6]) : (word === 'tonight' ? { hour: 20 } : { hour: 9 });
  start = day.set(t); assumed = !m[4]; title = m[1]; how = 'dayword';
}
if (!start && (m = raw.match(new RegExp(`^(.*?)\\s+at\\s+${TIME}$`, 'i')))) {
  start = now.set(clock(m[2], m[3], m[4]));
  if (start <= now) start = start.plus({ days: 1 });
  title = m[1]; how = 'at';
}
if (!start) {
  start = now.plus({ days: 1 }).set({ hour: 9, minute: 0, second: 0, millisecond: 0 });
  assumed = true; how = 'default';
}
title = (title || raw).replace(/[\s,.-]+$/, '').trim() || 'Reminder';
if (start <= now && how !== 'relative') start = start.plus({ days: 1 }); // never create a past event
const end = start.plus({ minutes: 30 });

return [{
  json: {
    ok: true,
    title,
    startIso: start.toISO(),
    endIso: end.toISO(),
    whenLabel: start.toFormat('ccc d LLL HH:mm'),
    assumed,
    how,
    tgMessageId: msg.message_id,
    chatId: msg.chat.id
  }
}];
```

`Create Calendar Event` (Google Calendar node; the `reminders` sub-fields follow the node's UI
naming and are UNVERIFIED — set a 10-minute popup in the UI if the JSON is rejected):

```json
{
  "name": "Create Calendar Event",
  "type": "n8n-nodes-base.googleCalendar",
  "typeVersion": 1.3,
  "parameters": {
    "calendar": { "__rl": true, "mode": "list", "value": "rocloyd87@gmail.com", "cachedResultName": "rocloyd87@gmail.com" },
    "start": "={{ $json.startIso }}",
    "end": "={{ $json.endIso }}",
    "useDefaultReminders": false,
    "additionalFields": {
      "summary": "={{ $json.title }}",
      "description": "=via Hermes /remind · tg msg {{ $json.tgMessageId }}",
      "remindersUi": { "remindersValues": [{ "method": "popup", "minutes": 10 }] }
    }
  },
  "credentials": { "googleCalendarOAuth2Api": { "id": "4q1Dm9DBfArZD2sA", "name": "GCalendar - Rocloyd87@gmail.com" } },
  "onError": "continueRegularOutput"
}
```

`Build Remind Reply` (Set node, one string field `reply`):

```
={{ $json.error
  ? '⚠️ Calendar refused the event: <i>' + ($json.error.message || $json.error) + '</i>'
  : '⏰ <b>Reminder set</b>\n' + $('Parse Remind').first().json.title + '\n'
    + $('Parse Remind').first().json.whenLabel
    + ($('Parse Remind').first().json.assumed ? ' <i>(assumed time — say a time next time)</i>' : '')
    + ($json.htmlLink ? '\n<a href="' + $json.htmlLink + '">open</a>' : '') }}
```

Optional LLM fallback (off by default): if `how === 'default'` route to a Gemini structured-output
node with the prompt "Extract {title, startIso} in Asia/Manila from: <text>; today is <date>" and
feed the same Calendar node. Only add it if the assumed-time replies become annoying.

---

## 2 · `/todo <text> [#tag] [!high|!med|!low]` → TickTick task

**Credential does not exist yet** — create the generic OAuth2 credential described in
`W-DAILY-BRIEF-v2.json` (Read Me, section 1: developer.ticktick.com app → n8n OAuth2 API,
auth `https://ticktick.com/oauth/authorize`, token `https://ticktick.com/oauth/token`, scope
`tasks:read tasks:write`, Authentication = Header, name `TickTick OAuth2 - rocloyd87`). Tokens
are long-lived (~6 months) with no refresh; reconnect on 401.

Flow: `Is Todo Command` → `Build Todo` (Code) → IF `ok` → `TickTick Create Task` (HTTP POST) →
`Reply Todo`.

`Build Todo` (Code node):

```javascript
// Build the TickTick task body from "/todo <text>". Tokens: !high|!1 → priority 5, !med|!3 → 3, !low|!5 → 1,
// #word → tag. Everything else is the title. No dates here — use /remind for time-bound items.
const msg = $('Telegram Trigger').first().json.message;
const raw = String(msg.text || '').replace(/^\/todo(@\w+)?\s*/i, '').trim();
if (!raw) return [{ json: { ok: false, reply: 'Usage: /todo <text> [#tag] [!high|!med|!low]' } }];

const PRI = { high: 5, '1': 5, med: 3, medium: 3, '3': 3, low: 1, '5': 1 };
let priority = 0;
const tags = [];
const words = [];
for (const w of raw.split(/\s+/)) {
  const p = w.match(/^!(\w+)$/);
  const t = w.match(/^#([\w-]+)$/);
  if (p && PRI[p[1].toLowerCase()] !== undefined) priority = PRI[p[1].toLowerCase()];
  else if (t) tags.push(t[1].toLowerCase());
  else words.push(w);
}
const title = words.join(' ').trim() || raw;
return [{
  json: {
    ok: true,
    body: {
      title,
      projectId: 'inbox132802495',   // TickTick Inbox (id from the connector, 2026-09-02) — confirm via GET /open/v1/project
      priority,
      tags,                          // UNVERIFIED: the Open API task schema does not list tags; if ignored, append '#tag' to title instead
      timeZone: 'Asia/Manila',
      content: `via Hermes /todo · tg msg ${msg.message_id}`
    },
    chatId: msg.chat.id,
    tgMessageId: msg.message_id
  }
}];
```

`TickTick Create Task`:

```json
{
  "name": "TickTick Create Task",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "parameters": {
    "method": "POST",
    "url": "https://api.ticktick.com/open/v1/task",
    "authentication": "genericCredentialType",
    "genericAuthType": "oAuth2Api",
    "sendBody": true,
    "specifyBody": "json",
    "jsonBody": "={{ JSON.stringify($json.body) }}",
    "options": { "timeout": 15000 }
  },
  "credentials": { "oAuth2Api": { "id": "", "name": "TickTick OAuth2 - rocloyd87" } },
  "onError": "continueRegularOutput"
}
```

`Reply Todo` text expression:

```
={{ $json.error
  ? '⚠️ TickTick refused: <i>' + ($json.error.message || $json.error) + '</i>'
  : '✅ <b>Added to TickTick</b>\n' + $json.title
    + ($json.priority ? ' · P' + $json.priority : '')
    + ($json.tags && $json.tags.length ? ' · #' + $json.tags.join(' #') : '') }}
```

UNVERIFIED: whether the Open API honours `tags` on create (the documented task schema omits it,
the connector returns it). If the created task shows no tag, drop `tags` and append `#ecosystem`
to the title instead — TickTick parses hashtags in titles.

---

## 3 · `/cascade` → Cascade Hideaway status card

One call: the Airbnb calendar export (iCal) already consumed by the Cascade inventory system
(`Code.gs` booking-calendar integration). The URL carries a secret `s=` token, so store it as an
n8n **Query Auth** credential (`Airbnb iCal - Cascade`, parameter `s`) and keep only the listing
path in the node. Airbnb's feed has no guest names — the card is occupancy-only by design.

Flow: `Is Cascade Command` → `Fetch Airbnb iCal` (HTTP GET, response format = text) →
`Parse iCal` (Code) → `Reply Cascade`.

```json
{
  "name": "Fetch Airbnb iCal",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "parameters": {
    "url": "https://www.airbnb.com/calendar/ical/LISTING_ID.ics",
    "authentication": "genericCredentialType",
    "genericAuthType": "queryAuth",
    "options": { "timeout": 15000, "response": { "response": { "responseFormat": "text", "outputPropertyName": "data" } } }
  },
  "credentials": { "queryAuth": { "id": "", "name": "Airbnb iCal - Cascade" } },
  "onError": "continueRegularOutput"
}
```

`Parse iCal` (Code node) — sample output for a stay ending 4 Sep and a booking 6–9 Sep:

```
🏡 Cascade Hideaway
Today: OCCUPIED · check-out Fri 4 Sep (4 n stay)
Next check-in: Sun 6 Sep (in 4 d, 3 n)
September: 6/30 nights booked · 20% occupancy
source: Airbnb calendar · 3 events
```

```javascript
// Parse the Airbnb iCal export into today's occupancy + next check-in. No library needed:
// Airbnb emits VEVENT blocks with DTSTART;VALUE=DATE / DTEND;VALUE=DATE (check-out day, exclusive)
// and SUMMARY "Reserved" or "Airbnb (Not available)". Guest names are NOT in the feed.
const TZ = 'Asia/Manila';
const today = DateTime.now().setZone(TZ).startOf('day');
const item = $input.first().json;
const text = typeof item === 'string' ? item : (item.data || item.body || item.ics || '');

if (!text || !/BEGIN:VCALENDAR/.test(text)) {
  return [{ json: { ok: false, reply: '🏡 Cascade: calendar feed unavailable right now.' } }];
}

const unfold = text.replace(/\r?\n[ \t]/g, '');
const events = [];
for (const block of unfold.split('BEGIN:VEVENT').slice(1)) {
  const get = (k) => { const mm = block.match(new RegExp(`^${k}[^:]*:(.+)$`, 'm')); return mm ? mm[1].trim() : ''; };
  const ds = get('DTSTART'), de = get('DTEND');
  if (!ds || !de) continue;
  const toDate = (s) => DateTime.fromFormat(s.slice(0, 8), 'yyyyLLdd', { zone: TZ });
  const start = toDate(ds), end = toDate(de);
  if (!start.isValid || !end.isValid) continue;
  const summary = get('SUMMARY');
  events.push({ start, end, nights: Math.round(end.diff(start, 'days').days), blocked: /not available/i.test(summary), summary });
}
events.sort((a, b) => a.start - b.start);

const current = events.find((e) => e.start <= today && today < e.end);
const next = events.find((e) => e.start > today && !e.blocked);
const monthStart = today.startOf('month'), monthEnd = today.endOf('month');
let bookedNights = 0;
for (const e of events) {
  if (e.blocked) continue;
  const s = e.start < monthStart ? monthStart : e.start;
  const en = e.end > monthEnd.plus({ days: 1 }) ? monthEnd.plus({ days: 1 }).startOf('day') : e.end;
  if (en > s) bookedNights += Math.round(en.diff(s, 'days').days);
}
const daysInMonth = today.daysInMonth;
const occupancy = Math.round((bookedNights / daysInMonth) * 100);

const lines = ['<b>🏡 Cascade Hideaway</b>'];
if (current && !current.blocked) {
  lines.push(`Today: <b>OCCUPIED</b> · check-out ${current.end.toFormat('ccc d LLL')} (${current.nights} n stay)`);
} else if (current && current.blocked) {
  lines.push(`Today: <b>BLOCKED</b> until ${current.end.toFormat('ccc d LLL')}`);
} else {
  lines.push('Today: <b>VACANT</b>');
}
if (next) {
  const inDays = Math.round(next.start.diff(today, 'days').days);
  lines.push(`Next check-in: ${next.start.toFormat('ccc d LLL')} <i>(in ${inDays} d, ${next.nights} n)</i>`);
} else {
  lines.push('Next check-in: <i>none on the calendar</i>');
}
lines.push(`${today.toFormat('LLLL')}: ${bookedNights}/${daysInMonth} nights booked · ${occupancy}% occupancy`);
lines.push(`<i>source: Airbnb calendar · ${events.length} events</i>`);

return [{ json: { ok: true, reply: lines.join('\n'), current: !!current, nextCheckIn: next ? next.start.toISODate() : null, bookedNights, occupancy } }];
```

Later (second call, optional): month-to-date payouts from the Cascade source ledger sheet
(`1-NhZfI1sWj86V_ZZEY3KgxxMdelp0-wrJ8_gkDbLhmA`) via a Google Sheets read — tab and column names
must be read from the dashboard `Code.gs` first (the two known header-offset bugs live there).

---

## 4 · `/sub` → `subscriptions_audit` card

One call to the existing tool bridge, same as the `/spend` route calls `get_kpis`.

Flow: `Is Sub Command` → `Call subscriptions_audit` (Execute Workflow `AVsltr2l2KOIbSkT`, inputs
`tool = subscriptions_audit`, `args = {}` — copy the exact input mapping from the `/spend` route)
→ `Format Sub` (Code) → `Reply Sub`.

```json
{
  "name": "Call subscriptions_audit",
  "type": "n8n-nodes-base.executeWorkflow",
  "typeVersion": 1.2,
  "parameters": {
    "workflowId": { "__rl": true, "mode": "id", "value": "AVsltr2l2KOIbSkT", "cachedResultName": "Subworkflow: Call HermesApi Tool" },
    "workflowInputs": { "mappingMode": "defineBelow", "value": { "tool": "subscriptions_audit", "args": "={{ JSON.stringify({}) }}" } },
    "options": { "waitForSubWorkflow": true }
  },
  "onError": "continueRegularOutput"
}
```

`Format Sub` (Code node; key lists are tolerant because the tool's output was not read on the
machine):

```javascript
// Format the subscriptions_audit tool result as a Telegram HTML card. Shape-tolerant because the
// bridge output has not been read on the machine (UNVERIFIED) — adjust the key lists once seen.
const esc = (s) => String(s ?? '').replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
const peso = (n) => '₱' + Math.round(Number(n) || 0).toLocaleString('en-PH');
const pick = (o, keys) => { for (const k of keys) if (o && o[k] != null && o[k] !== '') return o[k]; return undefined; };

const j = $input.first().json || {};
if (j.error) return [{ json: { reply: `🔁 subscriptions_audit unavailable: <i>${esc(j.error.message || j.error)}</i>` } }];
const body = j.result ?? j.data ?? j;
const list = pick(body, ['subscriptions', 'items', 'recurring', 'rows']) || (Array.isArray(body) ? body : []);

if (!Array.isArray(list) || !list.length) {
  return [{ json: { reply: '<b>🔁 Subscriptions</b>\nno recurring merchants detected in the ledger.\n<i>MoneyMatter has 0 subscriptions defined — run its candidate detection to seed them.</i>' } }];
}
const rows = list.map((r) => ({
  name: pick(r, ['name', 'merchant', 'payee', 'label']) || '?',
  amt: Math.abs(Number(pick(r, ['amount', 'avgAmount', 'monthly', 'expectedAmount'])) || 0),
  cadence: pick(r, ['cadence', 'frequency', 'interval']) || '',
  last: String(pick(r, ['lastSeen', 'lastDate', 'last']) || '').slice(0, 10),
  next: String(pick(r, ['nextDue', 'nextDate', 'next']) || '').slice(0, 10),
  flag: pick(r, ['flag', 'status', 'note']) || ''
})).sort((a, b) => b.amt - a.amt);
const monthly = rows.reduce((s, r) => s + (/(year|annual)/i.test(r.cadence) ? r.amt / 12 : /week/i.test(r.cadence) ? r.amt * 4.33 : r.amt), 0);
const lines = rows.slice(0, 12).map((r) => {
  const tail = [r.cadence, r.next ? `next ${r.next}` : r.last ? `last ${r.last}` : '', r.flag].filter(Boolean).join(' · ');
  return `• ${esc(r.name)} — ${peso(r.amt)}${tail ? ` <i>(${esc(tail)})</i>` : ''}`;
});
const more = rows.length > 12 ? `\n<i>+${rows.length - 12} more</i>` : '';
const total = pick(body, ['monthlyTotal', 'total']) ?? monthly;
return [{ json: { reply: `<b>🔁 Subscriptions</b> · ≈${peso(total)}/mo · ${rows.length} recurring\n${lines.join('\n')}${more}` } }];
```

Context for the card's footer: MoneyMatter had **0 subscriptions, 0 candidates** on 2026-09-02
(connector read), so the audit's only source today is the ledger recurrence detection in GAS.
Seeding MoneyMatter subscriptions (`detect_subscription_candidates`, write scope, Lloyd's OK)
would let `/sub` and the brief cross-check both.

---

## Desktop verification checklist

1. Patch the four IF nodes after `Is Inbox Note`; keep the false-branch chain intact.
2. `/remind test ping in 5m` → event appears on rocloyd87@gmail.com; card links to it.
3. `/todo hermes route test #ecosystem` → task in TickTick Inbox with the tag (or title hashtag).
4. `/cascade` → card matches the Airbnb host calendar for today.
5. `/sub` → card renders; compare against `subscriptions_audit` called through the agent.
6. Add the commands in BotFather; note exec ids in `alfred_build_log`.
