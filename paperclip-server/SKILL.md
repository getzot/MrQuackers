---
name: paperclip-server
description: Launch Paperclip server and Hermes adapter.
version: 1.0.0
---

# Launching Paperclip Server

Paperclip (paperclipai/paperclip) is the open-source AI agent orchestration platform — Node.js server + React UI managing AI agents. Launch from workspace, verify PostgreSQL instance (`/home/tim/.paperclip/instances/default/`), and connect via Hermes adapter (`hermes_local`/`hermes_gateway`).

Always-on rules:
- Workspace: `/home/tim/workspace/paperclip/` (ESM module `@paperclipai/server`); `npm start` has NO script — launch via `node launch.js` that imports `startServer()`.
- DB: `/home/tim/.paperclip/instances/default/db`; verify `postmaster.pid` and port (54329) before launch. Postgres is embedded; reuse running instance.
- Agent JWT (`PAPERCLIP_AGENT_JWT_SECRET` or `BETTER_AUTH_SECRET`) is REQUIRED — missing it causes "Internal server error" at agent creation during `npx paperclipai onboard`.
- Health: `http://127.0.0.1:3100/api/health`; UI at `http://127.0.0.1:3100`.
- Adapter docs: `node_modules/@paperclipai/hermes-paperclip-adapter/README.md`.

Procedure:
1. Confirm workspace exists and node_modules installed (`@paperclipai/server`).
2. Check DB health: `postmaster.pid` in `.paperclip/instances/default/db` AND verify postgres process on port 54329. If missing, DB not initialized.
3. Set `PAPERCLIP_AGENT_JWT_SECRET` (or `BETTER_AUTH_SECRET`) before onboarding — without it agent creation fails with "Internal server error".
4. Launch server with `node launch.js` (ESM import of `startServer()`), NOT `npm start`. Run in background (`background=true`) and verify health endpoint returns 200.
5. Verify adapter connection (`hermes_local` same-host; `hermes_gateway` remote) and adapter docs.
6. If onboarding: `PAPERCLIP_AGENT_JWT_SECRET=*** npx paperclipai onboard`.

Pitfalls:
- `npm start` fails — package.json has no start script; the server is an ESM module.
- Check workspace before assuming missing install; DB and server binary are separate.
- Missing agent JWT is the #1 cause of "Internal server error" during wizard agent creation — set env before onboarding.
- Health endpoint at `/api/health`; server binds loopback 127.0.0.1:3100 by default.
