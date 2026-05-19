---
name: cloakbrowser
description: >
  Use CloakBrowser (stealth Chromium) instead of plain Playwright whenever a
  task involves scraping or automating sites protected by bot detection:
  Cloudflare Turnstile, reCAPTCHA v3, FingerprintJS, DataDome, Akamai, Kasada,
  or any page that blocks headless Chrome. Triggers: user mentions "blocked",
  "bot detected", "Cloudflare", "CAPTCHA", "anti-bot", "stealth browser",
  "undetected", or asks to scrape a site that typically blocks automation.
  Also use when user asks to write Playwright code and the target site is
  known to use aggressive bot detection.
  Prerequisite: installed via `uv tool install git+https://github.com/CloakHQ/CloakBrowser`.
---

# CloakBrowser

Stealth Chromium with source-level C++ fingerprint patches — not JS injection.
Drop-in Playwright replacement. Same API, swap one import line.

**Passes:** reCAPTCHA v3 (score 0.9), Cloudflare Turnstile, FingerprintJS,
BrowserScan, DataDome. 30/30 bot-detection tests.

## Prerequisites

```bash
# Already installed globally via uv tool:
uv tool install git+https://github.com/CloakHQ/CloakBrowser

# Verify binary
python -c "from cloakbrowser import binary_info; print(binary_info())"
```

On first `launch()`, the stealth Chromium binary (~200 MB) auto-downloads to
`~/.cloakbrowser/`. Subsequent launches are instant.

## Migration from Playwright

```python
# Before
from playwright.sync_api import sync_playwright
pw = sync_playwright().start()
browser = pw.chromium.launch()

# After — one line change, everything else identical
from cloakbrowser import launch
browser = launch()
```

## Core API

### Sync

```python
from cloakbrowser import launch

browser = launch()                          # headless, stealth on
browser = launch(headless=False)            # headed (see window)
browser = launch(proxy="http://user:pass@host:8080")
browser = launch(args=["--window-size=1920,1080"])

page = browser.new_page()
page.goto("https://protected-site.com")
print(page.title())
browser.close()
```

### Async

```python
import asyncio
from cloakbrowser import launch_async

async def main():
    browser = await launch_async()
    page = await browser.new_page()
    await page.goto("https://protected-site.com")
    content = await page.content()
    await browser.close()

asyncio.run(main())
```

### Context helper (recommended for multi-page sessions)

```python
from cloakbrowser import launch_context

ctx = launch_context(
    user_agent="Mozilla/5.0 ...",
    viewport={"width": 1920, "height": 1080},
    locale="en-US",
    timezone_id="America/New_York",
)
page = ctx.new_page()
```

## Common patterns

### Save and restore session (cookies / localStorage)

```python
ctx = launch_context()
page = ctx.new_page()
page.goto("https://example.com/login")
# ... perform login ...
ctx.storage_state(path="session.json")   # save

# Next run — reuse session, skip login
browser = launch()
ctx = browser.new_context(storage_state="session.json")
```

### Check bot-detection score

```python
browser = launch()
page = browser.new_page()
page.goto("https://www.browserscan.net/bot-detection")
page.wait_for_timeout(3000)
print(page.content())
browser.close()
```

### Headed mode with virtual display (Linux / Docker)

```bash
sudo apt install xvfb
Xvfb :99 -screen 0 1920x1080x24 &
export DISPLAY=:99
```

```python
browser = launch(headless=False)
```

## Environment variables

| Variable | Default | Effect |
|---|---|---|
| `CLOAKBROWSER_BINARY_PATH` | — | Skip download; use local Chromium binary |
| `CLOAKBROWSER_CACHE_DIR` | `~/.cloakbrowser` | Binary cache location |
| `CLOAKBROWSER_DOWNLOAD_URL` | GitHub Releases | Custom binary download URL |

## Utility functions

```python
from cloakbrowser import binary_info, clear_cache, ensure_binary

binary_info()     # {'version': '...', 'platform': 'linux-x64', 'installed': True}
ensure_binary()   # pre-download during Docker build
clear_cache()     # force re-download
```

## Pitfalls

- Do NOT run `playwright install chromium` — CloakBrowser ships its own binary.
  Only system deps: `playwright install-deps chromium`
- Do NOT use uvloop inside a web server — use `uvicorn app:app --loop asyncio`
- Do NOT set `stealth_args=False` unless supplying your own fingerprint flags

## Troubleshooting

**Binary download fails**
```bash
export CLOAKBROWSER_BINARY_PATH=/path/to/local/chrome
```

**Site still detects bot (Kasada / Akamai canvas checks)**
```bash
sudo apt install -y fonts-noto-color-emoji fonts-freefont-ttf fonts-unifont \
  fonts-ipafont-gothic fonts-wqy-zenhei fonts-tlwg-loma-otf
```

**uvloop hang inside uvicorn / FastAPI**
```bash
uvicorn app:app --loop asyncio
```

## References

- Source: https://github.com/CloakHQ/CloakBrowser
- PyPI: https://pypi.org/project/cloakbrowser/
- Examples: https://github.com/CloakHQ/CloakBrowser/tree/main/examples
