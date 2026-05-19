---
name: pake-cli
description: >
  Use Pake CLI to turn any webpage into a lightweight desktop application.
  Use when the user wants to package a web app, create a desktop wrapper for a
  website, build a native-like app from a URL, or customize window behavior,
  icons, injection, and build targets for packaged web apps.
---

# Pake CLI

Turn any webpage into a desktop app using Rust and Tauri. Pake packages a URL
(or local HTML file) into a native `.app`/`.dmg` (macOS), `.msi`/`.exe`
(Windows), or `.deb`/`.AppImage`/`.rpm` (Linux).

## Usage Pattern

```bash
pake <url> [options]
```

- `url` — The web URL or local HTML file path to package. Required.
- Output lands in the current working directory by default.

## Common Commands

### Basic packaging

```bash
pake https://github.com --name GitHub
```

### Custom window size and immersive header (macOS)

```bash
pake https://weekly.tw93.fun --name Weekly \
  --width 1200 --height 800 --hide-title-bar
```

### Local static file packaging

```bash
pake ./my-app/index.html --name "My App" --use-local-file
```

### Inject custom CSS/JS

```bash
pake https://example.com --name Example \
  --inject ./tools/adblock.css,./tools/hotkey.js
```

### System tray + start hidden

```bash
pake https://github.com --name GitHub \
  --show-system-tray --start-to-tray
```

### Debug build (fast, no installer)

```bash
pake https://example.com --name Example --iterative-build --debug
```

### macOS: build and install directly to /Applications

```bash
pake https://github.com --name GitHub --install
```

## Options Reference

| Option | Description | Default |
|--------|-------------|---------|
| `--name <string>` | Application name | Prompted |
| `--icon <path>` | Custom icon (local or remote); auto-fetches if omitted | Auto |
| `--width <number>` | Window width | `1200` |
| `--height <number>` | Window height | `780` |
| `--min-width <number>` | Minimum resizable width | — |
| `--min-height <number>` | Minimum resizable height | — |
| `--zoom <number>` | Initial page zoom (50-200) | `100` |
| `--hide-title-bar` | Immersive header (macOS only) | `false` |
| `--fullscreen` | Launch in fullscreen | `false` |
| `--maximize` | Launch maximized | `false` |
| `--always-on-top` | Keep window on top | `false` |
| `--show-system-tray` | Show in system tray | `false` |
| `--system-tray-icon <path>` | Tray icon (`.ico` or `.png`, 32-256px) | — |
| `--hide-on-close` | Hide window on close instead of quitting | Platform default |
| `--start-to-tray` | Start minimized to tray (needs `--show-system-tray`) | `false` |
| `--title <string>` | Window title bar text | — |
| `--activation-shortcut <string>` | Global activation shortcut (e.g. `CmdOrControl+Shift+P`) | — |
| `--disabled-web-shortcuts` | Disable original web shortcuts | `false` |
| `--enable-find` | Enable in-page Find UI (`Cmd/Ctrl+F`) | `false` |
| `--force-internal-navigation` | Open all links inside the app window | `false` |
| `--internal-url-regex <pattern>` | Regex for URLs treated as internal | Domain-based |
| `--new-window` | Allow sites to open new windows/popups | `false` |
| `--multi-instance` | Allow multiple app processes | `false` |
| `--multi-window` | Allow multiple windows in one process | `false` |
| `--incognito` | Private browsing mode (no cookies/history) | `false` |
| `--wasm` | Enable WASM support with cross-origin isolation | `false` |
| `--enable-drag-drop` | Enable native drag-and-drop | `false` |
| `--inject <files>` | Inject local CSS/JS files (comma-separated or repeated) | — |
| `--proxy-url <url>` | Proxy server (`http://` / `https://` / `socks5://`) | — |
| `--user-agent <string>` | Custom browser user agent | — |
| `--app-version <string>` | App version (semver) | `1.0.0` |
| `--dark-mode` | Force dark mode (macOS packaging) | `false` |
| `--use-local-file` | Recursively copy local file folder into static bundle | `false` |
| `--ignore-certificate-errors` | Ignore TLS certificate errors | `false` |
| `--debug` | Enable dev tools and verbose logging | `false` |
| `--iterative-build` | Rapid build (app only, no installer) | `false` |
| `--install` | Build and copy `.app` into `/Applications` (macOS only) | `false` |
| `--keep-binary` | Keep raw standalone binary alongside installer | `false` |
| `--targets <target>` | Build target / architecture (see below) | Auto-detect |
| `--multi-arch` | macOS universal binary (Intel + Apple Silicon) | `false` |
| `--installer-language <lang>` | Windows Installer language | `en-US` |

### `--targets` values

- **macOS**: `intel`, `apple`, `universal` (auto-detects host arch)
- **Windows**: `x64`, `arm64`
- **Linux**: `deb`, `appimage`, `rpm`, `deb-arm64`, `appimage-arm64`, `rpm-arm64`

Default Linux targets are `deb` + `appimage`.

## macOS-specific Notes

- Default output is a `.dmg` installer. Set `PAKE_CREATE_APP=1` to emit a plain
  `.app` bundle instead.
- `--install` builds `.app`, copies to `/Applications`, and cleans up the local
  bundle on success.
- `--hide-title-bar` creates an immersive header. If the site has a fixed header,
  you may need to add `padding-top` via injected CSS to avoid overlap.
- `--multi-arch` requires `rustup` (not Homebrew Rust). Add the foreign target:
  - Intel -> M1: `rustup target add aarch64-apple-darwin`
  - M1 -> Intel: `rustup target add x86_64-apple-darwin`

## Docker (Linux)

```bash
docker run --rm --privileged \
  --device /dev/fuse --security-opt apparmor=unconfined \
  -v ./packages:/output \
  ghcr.io/tw93/pake \
  https://example.com --name myapp --targets appimage
```

## Typical Workflows

### 1. Quick prototype

```bash
pake https://todoist.com --name Todoist --iterative-build
```

### 2. Polished daily-driver app

```bash
pake https://linear.app --name Linear \
  --width 1400 --height 900 \
  --hide-title-bar --show-system-tray --hide-on-close
```

### 3. Local dev server -> desktop app

```bash
pake http://localhost:3000 --name MyApp --iterative-build --debug
```

### 4. Privacy-focused wrapper

```bash
pake https://example.com --name Example \
  --incognito --force-internal-navigation \
  --ignore-certificate-errors
```

## Global Shortcuts (packaged apps)

| macOS | Windows/Linux | Action |
|-------|---------------|--------|
| `Cmd + [` | `Ctrl + Left` | Back |
| `Cmd + ]` | `Ctrl + Right` | Forward |
| `Cmd + Up` | `Ctrl + Up` | Scroll to top |
| `Cmd + Down` | `Ctrl + Down` | Scroll to bottom |
| `Cmd + r` | `Ctrl + r` | Refresh |
| `Cmd + w` | `Ctrl + w` | Hide window (not quit) |
| `Cmd + -` | `Ctrl + -` | Zoom out |
| `Cmd + +` | `Ctrl + +` | Zoom in |
| `Cmd + =` | `Ctrl + =` | Zoom in |
| `Cmd + 0` | `Ctrl + 0` | Reset zoom |

Also supports double-click header to toggle fullscreen, and dragging the header
to move the window.

## Tips & Gotchas

- **First build is slow** — Rust/Tauri environment setup happens automatically;
  subsequent builds are fast.
- **Icons** — Pake auto-fetches the website favicon if `--icon` is omitted.
  For custom icons, provide `.icns` (macOS), `.ico` (Windows), or `.png`
  (Linux / fallback). Pake converts automatically.
- **Name handling** — macOS/Windows preserve spaces and case (e.g. "Google
  Translate"). Linux converts to lowercase with hyphens ("google-translate").
- **Auth popups** — Some providers (especially Google) block sign-in inside
  embedded webviews. Try `--new-window` or `--multi-window`, but success is not
  guaranteed.
- **Proxy on macOS** — `--proxy-url` requires macOS 14+.
- **WASM / Flutter Web** — Enable `--wasm` to set the required COOP/COEP headers
  and flags for `SharedArrayBuffer`.
