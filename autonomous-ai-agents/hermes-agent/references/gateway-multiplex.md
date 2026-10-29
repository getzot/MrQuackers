# Gateway Multiplex — Profile Token Conflicts

When `hermes gateway migrate --multiplex` is run, it checks whether multiple profiles share the same platform token. One token can serve only one gateway adapter; duplicates cause the adapter for every profile except the first claim to be parked (silent bot).

Procedure:
1. Identify which profile should own the token (usually `default` for the gateway host, or the active bot profile like `coder`).
2. Remove the duplicate token variables from all other profile `.env` files (`TELEGRAM_BOT_TOKEN`, `TELEGRAM_ALLOWED_USERS`, `SLACK_BOT_TOKEN`, `SLACK_APP_TOKEN`, etc.).
3. If the host gateway (`default`) should keep them, ensure they remain in `/home/tim/.hermes/.env` and are removed from the subordinate profiles.
4. Re-run `hermes gateway migrate --multiplex`.

Pitfalls:
- Never invent a new bot token — if the token is rejected (`invalid or revoked`), ask the user to generate a fresh one via @BotFather and update `.env`, then `hermes gateway restart`.
- The `python-dotenv` parse errors on `.env` lines are harmless warnings but indicate malformed lines; clean duplicate removals with `sed` to avoid corrupting the file.
- After removing tokens from profiles, verify `hermes status` shows `Platforms: Slack, Telegram` and the gateway runs without `DEGRADED`.
