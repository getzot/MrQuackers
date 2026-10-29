# Profile migration: `.nlm` → `.notebooklm`

When `nlm auth status` shows "Profile not found" but `.nlm/env` exists with cookies/auth:

1. Read `.nlm/env` (do not `cat` in chat — protected). Extract `NLM_COOKIES` and `NLM_AUTH_TOKEN`.
2. Create `.notebooklm/profiles/default/storage_state.json`:
```json
{
  "cookies": "<NLM_COOKIES value, quotes stripped>",
  "auth_token": "<NLM_AUTH_TOKEN value, quotes stripped>",
  "profile": "default",
  "workspace": "/home/tim/.hermes/workspace/notebooklm",
  "migrated_from_nlm": true
}
```
3. Copy chat profiles from `.nlm/chat-*.json` to profile dir if needed.
4. Confirm workspace native (`.hermes/`), no `.openclaw/` references.
5. Note: headless `nlm login` / `notebooklm login` blocked by CDP 403 — use profile restore, not re-auth, when cookies are valid.
