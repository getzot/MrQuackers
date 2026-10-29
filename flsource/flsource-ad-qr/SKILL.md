---
name: flsource-ad-qr
description: Replace QR in FL Source ad with new PNG overlay.
---
Always-on rules:
- Before building, confirm: source ad image path, replacement QR PNG path, output PNG path. Clarify ambiguous input (e.g. "new ad" vs overlay) — ask rather than assume.
- The original ad image stays untouched; output is a new PNG.
- Use PIL (Pillow) to overlay the QR PNG onto the source image. Verify input files exist before composing.

Procedure:
1. Confirm inputs (ad image, QR PNG, output path, optional position/scale).
2. Check both source files exist and are readable.
3. Open source ad and QR PNG with PIL; overlay QR at specified position (default centered or user-provided).
4. Save result to output PNG; confirm file written.

Pitfalls:
- Ambiguous user phrasing (e.g. "new ad") can mean regenerate, overlay, or replace region. Always clarify before writing code or files.
- Gateway restart revealed profile conflicts (same Telegram token across default, coder, master_bot, research). Restarting a gateway with conflicting profile tokens can disconnect bots — resolve conflicts before restart.
