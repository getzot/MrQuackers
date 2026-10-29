---
name: notebooklm-auth-patterns
path: seo-reo-customer-onboarding/references/notebooklm-auth.md
---
# NotebookLM Auth Patterns (GetZot / FLSource / Inc2Biz / YouTube)

Always-on rule: `notebooklm-py` is PRIMARY; `nlm` CLI is FALLBACK. Never fabricate a notebook name.

Primary path (`notebooklm-py`):
- Import test: `python3 -c "import notebooklm; print('OK')"`
- Works in active venv (`/home/tim/.local/lib/python3.14/site-packages`); no desktop auth needed.
- If import fails, check package: `pip install --upgrade notebooklm-py` or `pip install notebooklm-py`.

Fallback path (`nlm` CLI):
- Installed via uv: `/home/tim/.local/bin/nlm` -> `/home/tim/.local/share/uv/tools/notebooklm-cli/`
- Requires desktop Chrome with `--remote-debugging-port=9222 --remote-allow-origins=localhost:9222`
- Profile path: `~/.local/share/nlm/profiles/default/`; can restore from `.notebooklm/profiles/default/`
- Manual cookie import works: `nlm login --manual --profile default --file /tmp/nlm_cookies.txt`
- Profile restoration: `mkdir -p ~/.local/share/nlm/profiles/default/` then `cp -r .notebooklm/profiles/default/* ~/.local/share/nlm/profiles/default/`
- Profile cookies live as JSON: `~/.local/share/nlm/profiles/default/cookies.json` (key-value pairs, not a cookie-string file)
- Manual cookie file (`--file`) expects cookie-string format (`name=value; ...`), not JSON; clean pasted DevTools data first (remove dates, `✓`, domain columns) or convert JSON cookies to string format before import
- Partial cookie sets fail: `SID`, `SSID`, `HSID`, `APISID`, `SAPISID`, `NID`, `OSID`, `__Secure-*` must all be present; `.nlm/env` cookies alone are often expired
- Note: saved profile cookies may expire; `nlm auth status` can return "cookies have expired" even after `--manual`
- If headless `nlm login` times out, retry with live desktop Chrome profile (`.hermes/browser-profile`) rather than `/tmp/chrome-...`

Pitfall (generalizable rule):
- Never claim NotebookLM configured until `notebooklm-py` import works. `nlm auth status` is secondary confirmation only; an expired cookie profile is not sufficient proof.
- When `nlm` fails headless, prefer `notebooklm-py` rather than inventing a working `nlm` session.
