# Video SOP Reference
Points to workspace SOP: `.hermes/workspace/youtube-video-sop.md`
Handout template: `.hermes/workspace/youtube-handoff/handoff-template.md`

Always-on rules (see SKILL.md):
- ClickUp workspace 9006081395; business spaces `<Business> - YouTube`
- Central workspace `.hermes/workspace/`; vault-only secrets (`.env`)
- Remote agent has NO ClickUp/MCP access — structured file handoff only
- Tailscale mesh (`tim-ubuntu1` 100.110.226.26; peers: `monster`, `newbot`, `ubuntu-claw`) for VM-to-VM shared drops
- User: autonomous best initial attempt; modifications after delivery; don't block for approval unless directed
- Rate: 1/min preserved for ClickUp batch creation
- Transcript: camofox yt-dlp (`/youtube/transcript`) preferred; script-based fallback
- Subtasks per video: Concept, Seed Images, Thumbnail, Description, Transcript, Remote Production, Review/Publish, Metrics

Pitfall: ClickUp task creation may return 400 with partial payloads. Fallback: save to workspace (`youtube-video-planning/` or `youtube-handoff/`) and retry or create manually via ClickUp UI.
Pitfall: SMB `.108` unreachable from `.123`; `mount | grep shared` + `ls /mnt/shared` are the only proof; file-based workspace is working fallback.
Pitfall: Remote agent Telegram prompt (`video-agent-telegram-prompt.md`) defines roles, paths, deliverables; not loose chat.
References: handoff template at workspace `.hermes/workspace/youtube-handoff/handoff-template.md`; remote agent prompt at `.hermes/workspace/video-agent-telegram-prompt.md`.
