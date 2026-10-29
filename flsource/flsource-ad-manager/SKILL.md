---
name: flsource-ad-manager
description: Connect FLSource ad images for storage, edit, and gateway.
---
Workspace: `.hermes/workspace/flsource-ads/`. Index: `.hermes/workspace/flsource-ads/index.json` (maps name -> PNG path).

RECALL PROCEDURE (for Telegram/natural-language):
1. Read `index.json` to find the named image.
2. Load the PNG path from the index.
3. To edit QR: use skill `flsource-ad-qr` with the source PNG, replacement QR PNG, and output path (new file, keep original).
4. To build postcard/community card: compose images using available image skills (combine named PNGs into final postcard).
5. Save new files in workspace with updated `index.json` entry.
When user asks via Telegram (e.g., "show postcard" or "update QR"), execute this recall-edit-save workflow.

PITFALL — Higgsfield source: FLSource ads are created in the Higgsfield workspace "FL ADS". The agent must export/save them to `.hermes/workspace/flsource-ads/` and update `index.json`; it must NOT assume they are already local. There is no automatic sync — check the index first; if the named image is missing, ask for export or verify the Higgsfield workspace before claiming the asset exists.
