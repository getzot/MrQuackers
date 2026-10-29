# Auth paths
- `nlm login`; `.env` protected (credential store — never `cat`); vault preferred (`notebooklm-python-cli`, `tim@inc2biz.com`).
- See `references/profile-migration.md` for `.nlm/env` → `.notebooklm/profiles/default/storage_state.json` restore when headless login blocked by 403.
- Chrome 403 mechanism: `nlm/utils/cdp.py` launches Chrome with `--remote-allow-origins=*` (line 130). Blocker is an EXISTING Chrome instance (e.g., `.hermes/browser-profile` or stale `nlm` profile) on port 9222 launched WITHOUT the flag. Fix: kill the blocking Chrome PID (`ss -tlnp | grep 9222`), retry `nlm login` — new instance will have the flag.
