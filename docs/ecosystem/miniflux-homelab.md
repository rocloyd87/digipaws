# Miniflux on the Homelab (Orange Pi Zero2)

Self-hosted RSS for the daily brief pipeline. Sized for the Orange Pi Zero2 (1GB RAM, arm64): tight memory limits, alpine Postgres, polling throttled.

## docker-compose.yml

```yaml
services:
  miniflux:
    image: miniflux/miniflux:latest
    container_name: miniflux
    restart: unless-stopped
    depends_on:
      miniflux-db:
        condition: service_healthy
    ports:
      - "8085:8080"
    environment:
      - DATABASE_URL=postgres://miniflux:CHANGE_ME_DB_PASSWORD@miniflux-db/miniflux?sslmode=disable
      - RUN_MIGRATIONS=1
      - CREATE_ADMIN=1
      - ADMIN_USERNAME=CHANGE_ME_ADMIN_USER
      - ADMIN_PASSWORD=CHANGE_ME_ADMIN_PASSWORD
      # keep the Pi calm
      - POLLING_FREQUENCY=60
      - BATCH_SIZE=10
      - WORKER_POOL_SIZE=2
      - CLEANUP_ARCHIVE_READ_DAYS=30
      - CLEANUP_ARCHIVE_UNREAD_DAYS=90
    deploy:
      resources:
        limits:
          memory: 128M
    mem_limit: 128m

  miniflux-db:
    image: postgres:16-alpine
    container_name: miniflux-db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=miniflux
      - POSTGRES_PASSWORD=CHANGE_ME_DB_PASSWORD
      - POSTGRES_DB=miniflux
    volumes:
      - miniflux-db-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "miniflux"]
      interval: 20s
      timeout: 5s
      retries: 5
    command: postgres -c shared_buffers=16MB -c max_connections=20
    deploy:
      resources:
        limits:
          memory: 96M
    mem_limit: 96m

volumes:
  miniflux-db-data:
```

Notes:
- `mem_limit` works with plain `docker compose`; the `deploy.resources` block covers swarm-style tooling. Miniflux ~128MB, Postgres ~96MB — fine alongside HA + Pi-hole if total headroom allows; drop Miniflux to 96M/Postgres to 64M if memory pressure shows.
- After first login, create an API key: Settings > API Keys. n8n uses the `X-Auth-Token` header with that key.
- Port 8085 chosen to avoid clashes with existing homelab services; adjust as needed.

## feeds.opml

> Import via Miniflux UI: Settings > Import. **None of these URLs could be network-verified from the drafting environments (egress blocked on 2026-09-01 and again on 2026-09-02 — see the table below)** — they follow each site's standard WordPress/known feed convention. Verify each after import; Miniflux will flag dead ones immediately.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<opml version="2.0">
  <head><title>Homelab feeds</title></head>
  <body>
    <outline text="PH Business">
      <outline text="BusinessWorld" type="rss" xmlUrl="https://www.bworldonline.com/feed/" htmlUrl="https://www.bworldonline.com"/>
      <outline text="Inquirer Business" type="rss" xmlUrl="https://business.inquirer.net/feed" htmlUrl="https://business.inquirer.net"/>
      <outline text="Philstar Business" type="rss" xmlUrl="https://www.philstar.com/rss/business" htmlUrl="https://www.philstar.com/business"/>
    </outline>
    <outline text="Maritime">
      <outline text="gCaptain" type="rss" xmlUrl="https://gcaptain.com/feed/" htmlUrl="https://gcaptain.com"/>
      <outline text="Splash247" type="rss" xmlUrl="https://splash247.com/feed/" htmlUrl="https://splash247.com"/>
      <outline text="Seatrade Maritime News" type="rss" xmlUrl="https://www.seatrade-maritime.com/rss.xml" htmlUrl="https://www.seatrade-maritime.com"/>
    </outline>
    <outline text="PSE Tickers (Google News RSS)">
      <outline text="PSE: SM Investments" type="rss" xmlUrl="https://news.google.com/rss/search?q=%22SM%20Investments%22%20OR%20%22PSE%3A%20SM%22&amp;hl=en-PH&amp;gl=PH&amp;ceid=PH:en"/>
      <outline text="PSE: BDO" type="rss" xmlUrl="https://news.google.com/rss/search?q=%22BDO%20Unibank%22&amp;hl=en-PH&amp;gl=PH&amp;ceid=PH:en"/>
      <outline text="PSE: Ayala Corp" type="rss" xmlUrl="https://news.google.com/rss/search?q=%22Ayala%20Corporation%22%20OR%20%22PSE%3A%20AC%22&amp;hl=en-PH&amp;gl=PH&amp;ceid=PH:en"/>
      <outline text="PSE: ICTSI" type="rss" xmlUrl="https://news.google.com/rss/search?q=ICTSI%20OR%20%22International%20Container%20Terminal%22&amp;hl=en-PH&amp;gl=PH&amp;ceid=PH:en"/>
      <outline text="PSEi general" type="rss" xmlUrl="https://news.google.com/rss/search?q=PSEi%20OR%20%22Philippine%20Stock%20Exchange%22&amp;hl=en-PH&amp;gl=PH&amp;ceid=PH:en"/>
    </outline>
  </body>
</opml>
```

Google News RSS pattern for any ticker/company:
`https://news.google.com/rss/search?q=<url-encoded query>&hl=en-PH&gl=PH&ceid=PH:en`

### Feed verification status

Two attempts, both blocked before reaching the sites:

| Attempt | Where | Result |
|---|---|---|
| 2026-09-01 (mobile, session 42) | claude.ai drafting sandbox | egress blocked — no URL fetched |
| 2026-09-02 (mobile, session 44) | claude.ai Code sandbox | all 7 hosts refused by the egress proxy (`EGRESS_BLOCKED`, 403 at CONNECT) — no URL fetched |

| 2026-09-02 night (desktop, session 45) | Lloyd's desktop, Python urllib with the Miniflux UA | **all seven primaries fetched and parsed as RSS**; two fallbacks return 403 (table below) |

Verified from the desktop, not yet from the Pi (same public hosts; a difference there would be a
Pi egress issue). The old note follows for history: verify from a machine with open egress (the VPS or
the Pi) with either:

1. `drafts/W-FEED-PROBE.json` — import into n8n, press *Run once*: it fetches every URL below plus
   its fallback, reports HTTP status, feed type, item count and newest publish date, and posts the
   table to Telegram. Paste the verdicts into this table, then delete the workflow.
2. Or on the Pi, one line per feed:
   `curl -sSL -m 20 -A "Mozilla/5.0" -o /tmp/f -w "%{http_code} %{content_type}\n" "<url>"; grep -c "<item>" /tmp/f`

| Feed | URL | Status | If dead, try |
|---|---|---|---|
| BusinessWorld | https://www.bworldonline.com/feed/ | **OK 2026-09-02** — RSS, 30 items, latest 2026-09-02 (301 → https://bworldonline.com/feed/; use the bare host) | https://www.bworldonline.com/rss/ (also OK, same redirect) |
| Inquirer Business | https://business.inquirer.net/feed | **OK 2026-09-02** — RSS, 40 items, latest 2026-09-02 | https://newsinfo.inquirer.net/feed (OK, 40 items) |
| Philstar Business | https://www.philstar.com/rss/business | **OK 2026-09-02** — RSS, 20 items, latest 2026-09-02 10:00 PHT | https://www.philstar.com/rss/headlines (OK, 10 items) |
| gCaptain | https://gcaptain.com/feed/ | **OK 2026-09-02** — RSS, 12 items, latest 2026-09-02 | https://gcaptain.com/feed/?post_type=post (OK) |
| Splash247 | https://splash247.com/feed/ | **OK 2026-09-02** — RSS, 10 items, latest 2026-09-02 12:00 UTC | ~~https://splash247.com/rss~~ (HTTP 403) |
| Seatrade Maritime | https://www.seatrade-maritime.com/rss.xml | **OK 2026-09-02** — RSS, 50 items, latest 2026-09-02 | ~~https://www.seatrade-maritime.com/rss~~ (HTTP 403) |
| Google News RSS | https://news.google.com/rss/search?q=PSEi+OR+%22Philippine+Stock+Exchange%22&hl=en-PH&gl=PH&ceid=PH:en | **OK 2026-09-02** — RSS, 100 items (PH top stories feed also OK, 38 items) | — |

Miniflux itself is the last resort: import the OPML and it flags unreachable feeds on its first
refresh (Feeds → filter "with errors").

## Miniflux API — the 3 calls n8n will make

Base URL example: `http://192.168.0.35:8085`. Auth header: `X-Auth-Token: $MINIFLUX_API_KEY`.

1. Fetch unread entries (newest first, capped at 50):

```bash
curl -s -H "X-Auth-Token: $MINIFLUX_API_KEY" \
  "http://192.168.0.35:8085/v1/entries?status=unread&direction=desc&order=published_at&limit=50"
```

2. Mark entries as read (batch, by entry IDs from call 1):

```bash
curl -s -X PUT -H "X-Auth-Token: $MINIFLUX_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"entry_ids": [123, 456, 789], "status": "read"}' \
  "http://192.168.0.35:8085/v1/entries"
```

3. Unread entries for one category (get category IDs via `GET /v1/categories` first):

```bash
curl -s -H "X-Auth-Token: $MINIFLUX_API_KEY" \
  "http://192.168.0.35:8085/v1/categories/2/entries?status=unread&limit=50"
```

n8n HTTP Request node equivalents: same URLs, header `X-Auth-Token` from an n8n credential or `{{ $env.MINIFLUX_API_KEY }}`.
