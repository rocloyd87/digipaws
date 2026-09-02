# n8n drafts — status (2026-09-02)

| Draft | Status | Why |
|---|---|---|
| `W-BACKUP-N8N.json` | **SUPERSEDED — do not import** | `W-BACKUP-N8N — Weekly Workflow Backup` (`wvgRTsbYSOEUcIw3`) has been active since 2026-08-29: Sunday 03:00, fetches every workflow via the n8n API, stores + seals through GAS `125_N8nBackup.js` (D-046/D-048), Telegram verdict. Weekly cadence was the owner's choice; raise to nightly there if wanted. |
| `W-INBOX-FILE-skeleton.json` | **Reference only** | Correct pattern (Execute-Workflow sub-workflow off the single Hermes Telegram trigger), but its folder targets assume the Drive "Obsidian Vault" is canonical — blocked on README Q1. The existing Hermes graph already downloads photos/documents and uploads evidence to `Hermes Evidence`; the inbox lane should branch from `Capture Type Switch`, not duplicate the download. |
| `W-DAILY-BRIEF-skeleton.json` | **Reference only** | Real build should call the existing `get_kpis` sub-workflow and `W-HERMES-NUDGE`/`W-HERMES-DIGEST` helpers rather than raw MoneyMatter HTTP; TickTick/Calendar nodes need the credentials already present in n8n (check `n8n_manage_credentials`). |

Credential placeholders only — no secrets are committed here.

Newer mobile drafts (session 44, 2026-09-02) live in `../drafts/` — see `../drafts/README.md`.
`W-DAILY-BRIEF-skeleton.json` here is fully superseded by `../drafts/W-DAILY-BRIEF-v2.json`.
