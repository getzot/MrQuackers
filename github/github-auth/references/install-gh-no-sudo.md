---
title: gh CLI install without sudo
description: Binary download method when apt is unavailable.
---

When `apt install gh` fails (no sudo / no package), download from GitHub releases:

```bash
mkdir -p ~/bin
curl -L -o ~/bin/gh.tar.gz https://github.com/cli/cli/releases/download/v2.74.0/gh_2.74.0_linux_amd64.tar.gz
tar -xzf ~/bin/gh.tar.gz -C ~/bin
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
```

Then add `~/bin` to PATH in current session (`export PATH="$HOME/bin:$PATH"`).
