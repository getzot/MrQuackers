---
name: camofox-browser
description: "Use when configuring Camofox browser server. Native workspace, persistent login, secure keys."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
---

# Camofox Browser — Hermes Native Setup

Always-on rules:
- Run from `.hermes/workspace/` or `.hermes/profiles/default/`, never `.openclaw/`.
- Save secrets (`CAMOFOX_API_KEY`) to `.env` (workspace-level) or Hermes vault; never in chat or openclaw configs.
- Enable `persistent_context: true` and set `user_data_dir` when login sessions must survive restarts.

## Procedure

1. Clone / install to native workspace
```bash
mkdir -p /home/tim/.hermes/workspace/camofox
npm install  # inside workspace/camofox
```
2. Generate API key
```bash
openssl rand -hex 32 > .env
# format: CAMOFOX_API_KEY=<value>
```
3. Configure persistence (`camofox.config.json`)
```json
{
  "persistent_context": true,
  "user_data_dir": "/home/tim/.hermes/workspace/camofox/profiles"
}
```
4. Create profile directory
```bash
mkdir -p /home/tim/.hermes/workspace/camofox/profiles
```
5. Verify server (timeout expected for background process)
```bash
timeout 15 bash -c 'export CAMOFOX_API_KEY=$(cat .env | cut -d= -f2-); npm start'
```
6. Health check
```bash
curl http://localhost:9377/health
```
7. Capability verification (tab + snapshot + screenshot)
```bash
TAB=$(curl -s -X POST http://localhost:9377/tabs -H 'Content-Type: application/json' -d '{"userId":"agent1","sessionKey":"test","url":"https://example.com"}')
TAB_ID=$(echo $TAB | python3 -c "import sys,json;print(json.load(sys.stdin).get('tabId',''))")
# Snapshot (expect accessibility refs like [e1])
curl -s "http://localhost:9377/tabs/$TAB_ID/snapshot?userId=agent1"
# Screenshot
curl -s -o /tmp/test-screenshot.png -w "%{http_code}" "http://localhost:9377/tabs/$TAB_ID/screenshot?userId=agent1"
```

## Pitfalls

- Never reference `.openclaw/` paths in config, `.env`, or workspace. Migrate workspace/profile directories and delete `.openclaw/workspace/` contents.
- Remove `openclaw.plugin.json` from the native workspace — server runs standalone.
- Store key in `.env` or vault. If `browser_vault_save_login` declines (not a login form), treat as expected — `.env` is authoritative for this service key class.
- If Docker daemon unavailable, report honestly as blocker; don't fabricate healthcheck results.
- Grep workspace files for `.openclaw` references, patch `.env`/`camofox.config.json`, and delete old workspace contents before declaring native.
- Capability verification: expect `tabId`, `snapshot` with `[e1]` refs, and `200` screenshot; active tab/session counts in health should rise above 0. If health shows `browserConnected: false`, the server process started but Camoufox binary isn't running — check `npm start` output.

## References

None — full workflow in body.
