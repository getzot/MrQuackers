# Hermes Assist Backup — Prompt for Integrating Agent

Workspace: T&A Consulting Group LLC (ClickUp workspace 9006081395)
Space: T&A Consulting (space id: 90060199943)
Folder: Hermes_assist_bak (folder id: 901414656589)
Visibility: Public / team-visible (another Hermes agent connected here)
Created by: Hermes agent (this instance)

## What is backed up (individual files, not a zip; zip deleted)
- .hermes/config.yaml
- .hermes/.env (secrets included per user direction)
- .hermes/skills/ — all .md, .yaml, .json files (SKILL.md, references, templates, descriptions)
- .hermes/workspace/ docs (clickup plans, SOPs)
- Not included: memories/ folder (excluded per user)

## Access paths
- ClickUp folder: workspace 9006081395 -> space T&A Consulting -> folder Hermes_assist_bak
- Local workspace backup removed; rely on ClickUp folder or recreate from .hermes/

## Integration tasks for receiving agent
1. Read this prompt and the ClickUp folder contents.
2. Inspect .env for tokens/credentials (vault-backed, handle securely — do not log or leak).
3. Read config.yaml and relevant SKILL.md files to understand the Hermes workspace setup.
4. If integrating skills/config, prefer vault-only storage; don't write secrets to chat or unencrypted files.
5. Confirm workspace structure stays centralized (.hermes/) not per-business, per user's standing preference.

## Context
- User: Tim Casiglia (co-owner GetZot / Inc2Biz / FLSource)
- Workspace preference: centralized .hermes workspace; vault-only secrets; full autonomous execution
- ClickUp workspace: 9006081395; spaces per business
- ClickUp MCP URL: https://mcp.clickup.com/mcp

## Security note
- .env secrets included in this folder per explicit user instruction; treat as sensitive.
- Never expose tokens in chat or to external services.
