# lzc-changedetection

Lazycat NAS `.lpk` wrapper for [changedetection.io](https://github.com/dgtlmoon/changedetection.io) with integrated [sockpuppetbrowser](https://github.com/dgtlmoon/sockpuppetbrowser) (Chrome via Playwright protocol).

Self-hosted website change monitoring — detect content changes, price drops, restock alerts, defacement. JavaScript-rendered pages supported via embedded Chrome browser.

## Architecture

```
Browser
   │ HTTPS
   ▼
Lazycat OIDC reverse proxy
   │
   ▼
┌──────────────────────────────────────────┐
│  Lazycat app: cloud.lazycat.app.         │
│                changedetection           │
│                                          │
│  ┌────────────────┐    ┌──────────────┐ │
│  │ changedetection│───▶│  sockpuppet  │ │
│  │   :5000        │ ws │   :3000      │ │
│  │   /datastore   │    │  Chrome CDP  │ │
│  └────────────────┘    └──────────────┘ │
└──────────────────────────────────────────┘
```

Two services in single `.lpk`:

- **changedetection** (`dgtlmoon/changedetection.io:0.55.3`) — main UI + scheduler, port 5000
- **sockpuppet** (`dgtlmoon/sockpuppetbrowser:0.0.3`) — Chrome browser sidecar, port 3000 (CDP via WebSocket)

`changedetection` reaches `sockpuppet` over Lazycat's internal service DNS:
`ws://sockpuppet.cloud.lazycat.app.changedetectionio.lzcapp:3000`

## Features unlocked by Chrome integration

- Visual Selector tool (point-and-click element targeting on JS-rendered pages)
- Browser Steps (login, click, fill forms before diff)
- Full-page screenshots with notifications
- Restock + price detection from JS-rendered product pages

## Limitations / notes

- Lazycat OIDC sits in front of the app — no per-user auth inside changedetection.io itself. Anyone with NAS access can view/edit watches.
- `cap_add: SYS_ADMIN` and seccomp profile from upstream `docker-compose.yml` are NOT applied. Sockpuppet launches Chrome with `--no-sandbox` (default), so this is fine but Chrome runs without kernel sandboxing.
- AI/LLM features (change summaries, smart filters) require an OpenAI/Anthropic/Gemini/Ollama API key configured inside the app — not bundled.

## Prereqs

- LCMD client running on Windows with Developer Tools enabled
- `lzc-cli` ≥ 2.0.5 (`npm i -g @lazycatcloud/lzc-cli@latest`)
- `gh` authenticated (only for repo push)

## Build & install

```bash
cd changedetection-lzc/
./install.sh
```

Then follow the printed PowerShell command:

```powershell
lzc-cli app install C:\Users\fede9\Desktop\cloud.lazycat.app.changedetectionio-v1.0.0.lpk
```

## Configuration

All env vars in `changedetection-lzc/lzc-manifest.yml` under `services.changedetection.environment`. Common toggles:

| Var | Default | Notes |
|-----|---------|-------|
| `LOGGER_LEVEL` | DEBUG | TRACE / DEBUG / INFO / SUCCESS / WARNING / ERROR / CRITICAL |
| `FETCH_WORKERS` | 10 | Parallel fetch threads |
| `MINIMUM_SECONDS_RECHECK_TIME` | 3 | Hard floor on recheck interval |
| `SCREENSHOT_MAX_HEIGHT` | 16000 | Cap full-page screenshots |
| `HIDE_REFERER` | true | Don't leak NAS hostname to monitored sites |
| `DISABLE_VERSION_CHECK` | true | No telemetry to upstream |
| `TZ` | _(unset)_ | Set to e.g. `Europe/Rome` for scheduler |
| `BASE_URL` | _(unset)_ | Set if notification URLs need absolute links |
| `EXTRA_PACKAGES` | _(unset)_ | Space-separated pip packages (plugins) |

Sockpuppet:

| Var | Default | Notes |
|-----|---------|-------|
| `MAX_CONCURRENT_CHROME_PROCESSES` | 10 | Cap parallel Chrome instances |
| `SCREEN_WIDTH` / `SCREEN_HEIGHT` | 1920×1080 | Default viewport |
| `LOG_LEVEL` | DEBUG | |

## Persistence

- `/datastore` (changedetection): SQLite + watches + screenshots → bound to `/lzcapp/var/datastore` on NAS
- Sockpuppet is stateless

## Useful commands

```bash
# Status
powershell.exe -Command "lzc-cli app status cloud.lazycat.app.changedetectionio"

# Log
powershell.exe -Command "lzc-cli app log cloud.lazycat.app.changedetectionio"

# Uninstall (DESTROYS /datastore)
powershell.exe -Command "lzc-cli app uninstall cloud.lazycat.app.changedetectionio"
```

## License

This wrapper: MIT (see `LICENSE`).

Upstream `changedetection.io` and `sockpuppetbrowser` are Apache License 2.0; redistribute under their original terms.
