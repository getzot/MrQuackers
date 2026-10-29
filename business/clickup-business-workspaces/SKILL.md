---
name: clickup-business-workspaces
description: "Use when organizing ClickUp by business under one workspace."
version: 1.0
---

# ClickUp Business Workspace Organization

Always-on: single T&A workspace (9006081395), spaces per business (Space->Folder->List), direct `CLICKUP_TOKEN` (pk_... in .env), rate-limit space creation (~1/min), Fallback models: openrouter/nex-agi/nex-n2.5-pro:free, nex-n2.5-mini:free, nvidia/nemotron-3-super-120b-a12b:free, gemma-4-26b-a4b-it:free, cohere/north-mini-code:free, nvidia/nemotron-3.5-lightning:free. Offload long batches to cron (cronjob_manage with prompt); never block goal on user input. SEO/REO automation framework: space created, KB + SOP tasks saved to workspace/, process iterative. YouTube plan-only in ClickUp (creation handled by separate Hermes agent). OAuth redirect URL registration requires exact-match registered URLs; never expose open redirector. Pitfall: don't mix OAuth redirect registration with direct-token auth.

ClickUp connector (`clickup_mcp`) auth: use `manage_connections` with `action=connect` and `connectors=["clickup_mcp"]`. Do NOT use `authorize` (that is for local MCP servers); `clickup_mcp` is a hosted connector account, not a local MCP server. Once connected, verify with `clickup_mcp` tool calls.

Pitfall: ClickUp connector does NOT support space creation — only folder creation (`CLICKUP_CREATE_FOLDER`). Do not invent a space-creation result or claim a space was created. If the user asks for a new space, either (a) create a folder within an existing space and clarify, or (b) ask the user to create the space manually via ClickUp UI, then proceed with folder/task setup inside it.

User preference: `.hermes/` backups (skills, config, memory, agents) should exclude `.env` secrets (vault-only storage). Workspace is 9006081395; visibility for integration spaces should be public/visible to team so other Hermes agents can access assets.

Backup/file-upload procedure: upload individual files directly (not as a zip archive). User prefers space-level organization: user creates spaces manually in ClickUp UI (connector has no space-creation tool), then agent creates docs/tasks inside them. Include `.env` (secrets explicitly included by user direction for integration with other agents), `config.yaml`, and all `.md`/`.yaml` skill/config files. Do not include `memories/` unless explicitly requested. For ClickUp: prefer `CLICKUP_CREATE_DOCUMENT` for docs rather than task attachments when possible. Never bundle files into a zip for ClickUp uploads.
