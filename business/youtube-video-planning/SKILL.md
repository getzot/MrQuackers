---
name: youtube-video-planning
description: "Plan YouTube videos using ClickUp + remote agent."
version: 1.0
---

# YouTube Video Planning (Multi-Agent)

Always-on rules for Tim Casiglia (co-owner GetZot/Inc2Biz/FLSource):

- Workspace: `.hermes/workspace/` centralized (not per-business). ClickUp workspace ID 9006081395.
- ClickUp spaces: `<Business> - YouTube` (e.g. `GetZot - YouTube`). YouTube is plan-only; creation handled by separate Hermes agent on same Proxmox/network.
- Remote agent has NO ClickUp/MCP access. Always provide structured handoff file; never assume remote agent reads tasks.
- Same bare metal/network: use tailscale for private VM-to-VM file access or shared drops; don't invent remote-network workarounds.
- Buzz (Nostr multi-agent identity via Block) is optional; file-based handoff is default and works without Buzz setup.
- User wants autonomous best initial attempt; modifications after initial delivery only; don't block goal for approval unless explicitly directed.
- Video elements tracked as ClickUp subtasks: Concept, Seed Images, Thumbnail, Description, Transcript, Remote Production, Review/Publish, Metrics.
- Transcript source: camofox yt-dlp plugin (`/youtube/transcript`) preferred for caption extraction; script-based generation as fallback.

Procedure (in order):

1. Confirm business + format (long/short/reel) with user.
2. Find/create ClickUp task in business `- YouTube` space. Add all 8 subtasks.
3. Draft concept/script; collect seed image prompts/references.
4. Write thumbnail specs (dimensions, brand colors, text overlay, variants).
5. Draft description with SEO keywords, CTAs, chapters, links.
6. Determine transcript source: existing video URL (camofox yt-dlp) or generate from script.
7. Write handoff file `.hermes/workspace/youtube-handoff/<business>-<video-id>-handoff.md` using the reference template.
8. Deliver handoff to remote agent via shared path / tailscale / message. Update ClickUp status to `In Production`.
9. Receive assets; update ClickUp task and link assets back to subtasks.
10. Confirm final assets with user; update ClickUp to `Review` then `Published`.
11. After publish, record video URL + thumbnail link + metrics targets; schedule follow-up.

Pitfalls:
- Don't put video generation steps inside ClickUp as if this agent executes them — remote agent handles creation; this agent tracks.
- Don't save secrets to chat/memory — `.env` only; applies to ClickUp MCP and remote agent credentials.
- Don't create a new reference file per session; extend existing `references/video-sop.md` or `references/handoff-template.md`.
- Don't confuse Buzz identity setup with file-based coordination — they are complementary, not replacements.
- Don't fabricate ClickUp success: space creation may return 400; fall back to existing list (`Content ideas` for Inc2Biz) and simpler payload (`name`, `description`, `status` only). Confirm created task IDs before proceeding.
- Don't fabricate mount or network status: `mount | grep shared` + `ls /mnt/shared` are the only proof of shared workspace; SMB `.108` is unreachable from this VM (`.123`) — file-based workspace is the working fallback.
- Handout files live at `.hermes/workspace/youtube-handoff/` (`inc2biz-*`) and `.hermes/workspace/youtube-video-sop.md`; remote agent prompt saved at `.hermes/workspace/video-agent-telegram-prompt.md`.
- Remote agent has NO ClickUp access; the Telegram prompt (`video-agent-telegram-prompt.md`) defines roles, file paths, and deliverable format.

References: `references/video-sop.md` (SOP), `references/handoff-template.md` (template), `.hermes/workspace/youtube-handoff/` (live handoffs), `.hermes/workspace/video-agent-telegram-prompt.md` (remote agent prompt).
