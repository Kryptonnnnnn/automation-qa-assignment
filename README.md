# Automation & QA Developer Assessment

| Deliverable | File |
|---|---|
| Task 1 — QA & debug report | `Task1_QA_Report_Nikhil.pdf` |
| Task 2 — n8n API workflow | `Task2_Workflow_Nikhil.json` |
| Bonus — uptime monitor | `Bonus_UptimeMonitor.json` |
| Screenshots | `screenshot/` |
| Loom walkthrough | _add link here_ |

---

## Task 1 — QA & Debug Report

**App tested:** TodoMVC (React) — https://todomvc.com/examples/react/dist/
(I tried the RealWorld demo first, but `demo.realworld.io` returns 404 and `api.realworld.io` returns Cloudflare error 1016, so it is offline.)

**Method:** exercised every flow (add / complete / edit / delete / filter / clear / refresh) in a real Chromium browser via Playwright, plus keyboard-only, touch and 320 px mobile checks, and inspected the shipped JS bundle.

**8 issues found (2 High, 3 Medium, 3 Low)** — headline ones:
1. **High** — all todos are lost on refresh (the bundle has zero `localStorage` references; state lives only in memory). This is the root-cause analysis in the report.
2. **High** — the delete button is `display:none` until mouse-hover, so it is unusable on touch devices and skipped by keyboard Tab.
3. **Medium** — Esc does not cancel an edit; todos cannot be edited by keyboard; completion checkbox has no accessible name.

Full table (steps, expected vs actual, severity, suspected cause) is in the PDF.

---

## Task 2 — n8n "Morning Brief" workflow

```
Every hour → Fetch Markets (CoinGecko) → Top 5 Gainers → Enrich: 24h Chart (CoinGecko)
          → Build Digest → IF top gainer > 5% → Discord: Hot Alert / Discord: Normal Digest
          (any failure) → Format Error → Discord: Failure Alert
```

- **APIs:** CoinGecko `/coins/markets` (no key needed, free) and CoinGecko `/coins/{id}/market_chart` for enrichment; Discord webhook for output. Chosen because they are free, keyless and return data that changes hourly.
- **Transformation (Code node):** drops coins without a 24h change, sorts by 24h % change descending, keeps the top 5 and shapes them into `{name, symbol, price, change24h}`.
- **Enrichment:** the top gainer's 24h price series is fetched and reduced to a 24h low–high range.
- **IF threshold:** top gainer `> 5 %` is posted as a "Hot move" alert, otherwise as a normal digest. 5 % is a typical "notable daily move" for large-cap coins.
- **Error handling:** both HTTP nodes retry 3× (3 s apart) and then use *On Error → Continue (error output)*; the Code node does the same. Failures go to **Format Error → Discord: Failure Alert**, so nothing fails silently. If only the *enrichment* call fails, the digest is still sent with a "chart unavailable" line (graceful degradation).
- **Credentials:** no secrets in the JSON. The Discord nodes reference an n8n **Discord Webhook credential**.

## Bonus — Uptime monitor

```
Every 5 min → Start Timer → Ping App → Evaluate → Status 200? ─ no → Wait 15 s → Re-check → Still down? → Discord: Down Alert
Daily 09:00 → Build Daily Summary → Discord: Daily Summary
```

- Checks the **HTTP status code** (`neverError` + full response, 10 s timeout); a timeout/DNS failure counts as status 0.
- **Retry logic:** a non-200 is re-checked after 15 s; only a repeated failure alerts (avoids false alarms).
- **Response-time tracking** per check; stats (checks, avg/max ms, non-200 count, confirmed outages) accumulate in workflow static data.
- **Daily summary** posts availability and response times, then resets the counters. (Static data persists only while the workflow is **active**.)

---

## How to run

1. In n8n: **Credentials → New → Discord Webhook**, paste your Discord webhook URL, save.
2. **Import from file** each JSON. Open every *Discord* node and select your credential (the JSON carries a placeholder id).
3. Task 2: click **Execute workflow**. Bonus: activate it (static-data stats need an active workflow).
4. Test the error path: change the CoinGecko URL to a bad one and execute — a ❌ alert should arrive.

> **Security note:** an earlier version of this repo committed a Discord webhook URL in plain text. That webhook should be deleted in Discord and replaced; it must not be reused.
