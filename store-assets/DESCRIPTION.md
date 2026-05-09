# Store Descriptions (English)

## Short description (tagline, 1 line)

> Self-hosted website change monitoring with embedded Chrome — track price drops, restocks, content edits, and get instant alerts.

## Short description (2 lines, expanded)

> Monitor any website for changes — content, prices, stock, JSON APIs, PDFs.
> Embedded Chrome (Playwright) handles JavaScript-heavy pages, full-page screenshots, and login flows.

## Extended description

**changedetection.io** is a powerful, self-hosted website change monitoring service. Watch any web page or JSON API and get notified the moment something you care about changes — a price drops, a product comes back in stock, a document is updated, a job posting appears.

This Lazycat package bundles the official `changedetection.io` server with the `sockpuppetbrowser` Chrome sidecar, wired together so JavaScript-rendered pages work out of the box. No external services, no API keys, no telemetry — everything runs on your NAS.

### What you can do

- **Track web pages** — monitor any URL for content, structure, or visual changes
- **Track prices and stock** — restock alerts and price-drop notifications for product pages
- **Track JSON APIs** — filter and parse with JSONPath or jq
- **Track PDFs** — detect text changes inside PDF documents
- **Visual Selector** — point and click on a rendered page to pick exactly what to watch
- **Browser Steps** — log in, click, fill forms, accept cookies before the diff is taken
- **Conditional alerts** — only notify when a price crosses a threshold or a keyword appears
- **Schedules** — run only on weekdays, business hours, or any timezone
- **Notifications** — email, Discord, Slack, Telegram, Webhook, Apprise (100+ services)

### Why this build

- **Two services in one package** — the Chrome browser is bundled, no extra setup
- **Persistent storage** — all watches, history and screenshots live in `/lzcapp/var/datastore`
- **Privacy-first defaults** — referer hidden, version-check telemetry disabled
- **Pinned to immutable image digests** — reproducible installs, no silent upstream changes
- **Open source** — Apache 2.0 upstream, MIT wrapper

### Access model

The Lazycat reverse proxy (OIDC) sits in front of the app, so anyone with NAS access can use it. Optional API keys, notification URLs and AI/LLM credentials are configured inside the app's settings page.

### Use cases

- Price tracking on e-commerce sites (Amazon, eBay, custom shops)
- Restock alerts (collectibles, rare items, sale drops)
- Government and legal document monitoring
- News and competitor watch
- Job board polling
- Software release pages
- API regression detection
- Compliance-grade page integrity monitoring

### Resources

- Upstream project: https://github.com/dgtlmoon/changedetection.io
- Wrapper repo: https://github.com/f-liva/lzc-changedetection
- Tutorials: https://changedetection.io/tutorials
