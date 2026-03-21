---
description: Context for App Launcher — macOS control panel for all local dev apps. Use when working in the app-launcher directory.
applyTo: "**/app-launcher/**"
---

# App Launcher — Project Context

macOS control panel that starts/stops all local dev apps from a Chrome app window. Auto-starts as a macOS LaunchAgent. Never deployed externally.

**Agent**: Use `@app-builder` for changes (minimal — keep it simple).

## Architecture
- **Backend**: Express.js (CommonJS), port 4321
- **Frontend**: Vanilla HTML/CSS/JS at `public/index.html`
- **Process management**: `child_process.spawn()` with detached process groups
- **Port detection**: `net.createServer()` probe
- **Auto-start**: macOS LaunchAgent plist

## Key Files
```
apps.config.js                             — App registry (source of truth)
server.js                                  — Express + process management
public/index.html                          — Dashboard UI
com.kylehansen.app-launcher.plist          — macOS LaunchAgent
start.sh                                   — Opens Chrome --app window
```

## Adding a New App
Edit `apps.config.js`:
```javascript
{
  id: 'new-app',
  name: 'New App Name',
  cmd: 'npm',
  args: ['run', 'dev'],
  dir: '/Users/kylehansen/repos/new-app',
  port: 3010,
  url: 'http://localhost:3010',
}
```
Then restart App Launcher and update `memory/app-registry.md`.

## LaunchAgent Commands
```bash
launchctl load ~/Library/LaunchAgents/com.kylehansen.app-launcher.plist
launchctl unload ~/Library/LaunchAgents/com.kylehansen.app-launcher.plist
launchctl list | grep app-launcher
```

## Rules
- Never add external-facing features — local macOS only
- Keep it simple — its only job is process management
- Do NOT add databases, auth, or external API calls
