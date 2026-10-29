SUMMARY / PROMPT FOR VIDEOBOT HERMES AGENT

GOAL: Connect FLSource postcard/ad image creation, storage, recall, edit (QR), and gateway-independent processing.

WHAT EXISTS:
- Skills: flsource-ad-qr (QR overlay), flsource-ad-manager (connector), create-ad, community-card
- Workspace: .hermes/workspace/flsource-ads/ with index.json (name -> PNG path mapping)
- Skill flsource-ad-manager updated with RECALL PROCEDURE (read index.json, load PNG, edit QR, compose postcard, save/update index)

WHAT WAS FIXED / BUILT:
- Created workspace folder + index.json
- Created connector skill flsource-ad-manager linking create/store/recall/edit
- Updated skill with explicit recall instructions for agent
- Confirmed no actual FLSource PNG images exist yet in workspace (only placeholder index entry sample-ad-001)
- Confirmed no Higgsfield skill/MCP exists locally (user acknowledged this is for videobot instance)

WHAT REMAINS FOR VIDEOBOT:
- Import/export Higgsfield-created images into workspace with index entries
- Execute full workflow: create ad image -> save named PNG -> recall by name -> edit QR via flsource-ad-qr -> build community card/postcard -> update index.json
- Ensure agent reads index.json on every recall/edit request

COPY/PASTE PROMPT:
"I need you to manage FLSource postcard/ad images. Use workspace .hermes/workspace/flsource-ads/ and index.json (name -> PNG). Skills: flsource-ad-qr (overlay new QR PNG onto ad, output new PNG), flsource-ad-manager (connector), create-ad, community-card. Workflow: create/save named PNG -> recall by reading index.json -> load image -> edit QR with flsource-ad-qr -> compose postcard/community card -> update index.json -> respond. There is no Higgsfield MCP locally — import images from Higgsfield into workspace manually or via videobot's own access. Confirm workspace state before each operation."
