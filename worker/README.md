# Stock Stickies — Ask K Worker

Cloudflare Worker that powers the in-app Ask K portfolio assistant on
[stockstickies.com](https://stockstickies.com).

Single endpoint: `POST /api/ask-k`

## Setup

```bash
cd worker
npm install -g wrangler   # if not already installed
wrangler login
```

## Configure secrets

Run from this folder so the secrets attach to this worker (not the
eastern-shore-ai contact worker):

```bash
wrangler secret put STOCKSTICKIES_ASKK_API_KEY
wrangler secret put STOCKSTICKIES_ASKK_BASE_URL   # e.g. https://api.minimaxi.chat/v1
wrangler secret put STOCKSTICKIES_ASKK_MODEL      # e.g. MiniMax-Text-01
```

Mirror whatever values the existing eastern-shore-ai Ask K worker uses
(`ASKK_*` secrets there).

## Deploy

```bash
wrangler deploy
```

The deployed URL will look like:
`https://stock-stickies-askk.<account>.workers.dev`

After first deploy, set that URL in `src/App.jsx` (`ASKK_API_URL`) — or
override via `window.ASKK_API_URL` for local testing.

## Request shape

```json
{
  "message": "How concentrated is my portfolio?",
  "history": [{ "role": "user", "content": "..." }, { "role": "assistant", "content": "..." }],
  "portfolio": {
    "asOf": "2026-05-04T18:30:00Z",
    "nickname": "Red",
    "totals": { "marketValue": 12345.67, "cspObligation": 5000 },
    "positions": [{ "ticker": "AAPL", "shares": 10, "price": 220.5, "value": 2205, "category": "Core Holding" }],
    "cashSecuredPuts": [{ "ticker": "AMD", "strike": 100, "qty": 1, "expiry": "2026-06-20", "obligation": 10000 }],
    "watchList": ["MSFT", "NVDA"],
    "categories": [{ "label": "Core Holding", "color": "bg-blue-600" }]
  }
}
```

## Response

```json
{ "ok": true, "reply": "..." }
```

or `{ "ok": false, "error": "..." }` on failure.
