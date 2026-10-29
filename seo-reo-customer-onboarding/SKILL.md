---
name: seo-reo-customer-onboarding
description: Customer onboarding pipeline for SEO/REO automation (GHL → ClickUp + NotebookLM + audit + LLM visibility) under T&A workspace 9006081395.
version: 1.0
---
# SEO/REO Customer Onboarding

Always-on rules (for GetZot/Inc2Biz/FLSource clients via GHL, single workspace 9006081395):
- Receive GHL contact ID first — never invent; record in memory and workspace file.
- Create ClickUp List under business Space (rate ~1/min); clone from SEO/REO template; link GHL ID as custom field.
- NotebookLM research: PRIMARY: `notebooklm-py` (venv, no desktop auth required). FALLBACK: `nlm` CLI (requires desktop auth with `--remote-allow-origins=localhost:9222`) — only use if `notebooklm-py` fails. If both fail, or `composio link` hangs, report honestly — do not fabricate a notebook name.
- Audit pipeline: `Searchstack` (AI visibility), `OpenSEO`/`All-In-One-Free-SEO-Tool` (technical), `LLMrefs` (LLM tracking, citations, benchmarks across ChatGPT/Perplexity/Claude/Gemini).
- Automation: `cronjob_manage` for weekly audit + visibility report; `.env` `COMPOSIO_API_KEY` + `CLICKUP_TOKEN` (from `~/.hermes/.env` via vault, never chat); `composio` For You (`https://connect.composio.dev/mcp`, `ck_...` header) as secondary auth path.
- Remote/Telegram gateway: save remote steps (`workspace/seo-reo/onboard/anewara-clickup-remote.md` style) — list ClickUp commands, GHL link, auth gaps — so user can trigger from Telegram without desktop access.
- Never expose `.env` secrets, open redirect URLs, or auth codes in chat/workspace files.

Pitfalls:
- Don't mix OAuth redirect registration with direct-token auth (`CLICKUP_TOKEN` direct; `composio` OAuth). Separate paths.
- NotebookLM auth details: see `references/notebooklm-auth.md`. PRIMARY `notebooklm-py`; FALLBACK `nlm` (headless breaks; manual `--file` works but cookies expire). Never claim configured without passing import test.
- `nlm login` fails headless without `--remote-allow-origins`; now secondary fallback — prefer `notebooklm-py`; retry `nlm` with live desktop Chrome (`--remote-debugging-port=9222`, `~/.hermes/browser-profile`) only if primary fails.
- `composio` CLI (`/home/tim/.local/bin/composio`) hangs interactively headless; complete auth via desktop or use `notebooklm-py` fallback.
- Don't claim NotebookLM access configured until `notebooklm-py` import works in active venv (primary) and optionally `nlm auth status` confirms profile (fallback).
