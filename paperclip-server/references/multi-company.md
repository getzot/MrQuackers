---
name: paperclip-multi-company-setup
path: paperclip-server/references/multi-company.md
---
# Multi-Company Setup for Paperclip

Companies: GetZot (AI marketing), Inc2Biz (coaching/formation), FLSource (directory/postcards), YouTube Production Company.

Always-on rule: separate agent groups per company; never mix GHL IDs, ClickUp tokens, or budgets across companies.

Setup:
- Paperclip server: `/home/tim/workspace/paperclip` (`npm start`).
- DB: `.paperclip/instances/default/` (verify `postmaster.pid` before scaling).
- Adapter: `hermes_local` for same-host; `hermes_gateway` for remote.
- Per-company config: own org chart, budget cap, heartbeat, reporting line.

Improvement notes:
- Use `notebooklm-py` PRIMARY / `nlm` FALLBACK for all research across companies.
- YouTube company: separate pipeline, not tracked in ClickUp per user instruction.
- Budget governance: set per-company spend limits; Paperclip tracks costs.

Pitfall: don't claim multi-company isolation configured until each company has distinct agent group and separate DB schema/instance.
