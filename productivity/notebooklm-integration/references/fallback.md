# Fallback order (user-preference embedded):
# 1. PRIMARY: `notebooklm-py` (`python3 -c "import notebooklm"`, binary `notebooklm`). Works headless; no desktop auth required.
# 2. FALLBACK: `nlm` CLI (`nlm auth status`). Only with live desktop Chrome (`--remote-debugging-port=9222`, `--remote-allow-origins=`); headless `nlm login` times out.
# 3. SECONDARY: Composio (`composio` skill / `composio-cli`) when both NotebookLM paths fail.
# Always report which path is active; never fall back silently. Confirm with one safe real call before claiming access.
