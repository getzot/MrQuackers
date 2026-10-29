---
name: meet-evaluator
description: "Evaluate prospects from Google Meet transcripts."
version: 1.0.0
platforms: [linux, macos, windows]
author: Hermes Agent
---

# Meet Evaluator — Prospect / Customer Evaluation from Transcripts

Read a Google Meet transcript (or any meeting transcript file) and produce a
decision-ready evaluation: **Summary** + **Recommendations**.

## When to use
- After a sales/discovery call, when the principal asks "what did we talk
  about and should we proceed?"
- Evaluating a new lead or an existing customer against GetZot / Inc2Biz /
  FLSource fit.

## Input sources for transcripts
1. **Local file** — `.vtt`, `.txt`, `.srt`, `.docx`. Read directly.
2. **Google Drive** — Meet auto-transcripts are `.vtt` files in the organizer's
   Drive. Use the google-workspace skill (`google_api.py drive search/download`)
   when OAuth is configured. Looks for names like `*Meet Transcript*` or
   `Recording & transcript`.
3. **Pasted text** — user drops the transcript text inline.

## Output template
Always return BOTH blocks:

```
## Summary
- Call/Meeting: <title or file>
- Date / Participants (if known)
- What was discussed (bullets, paraphrased but grounded)
- Decisions made
- Open items / questions

## Recommendations
1. <top priority action> — <why, grounded in transcript>
2. ...
- Fit signal: Go / Proceed-with-conditions / Reconsider / Not-a-fit
- Suggested next step: (e.g. book Inc2Biz startup consult, to FLSource,
  assign follow-up task, qualify further)
```

## Business fit guidance
- **GetZot**: fit = prospect needs digital/AI marketing help; recommend funnel
  stage + marketing package.
- **Inc2Biz**: fit = new/small business needing formation, domain, business
  credit, or training; recommend Startup Consult calendar.
- **FLSource**: fit = local FL small business wanting directory listing or
  co-op postcard; recommend joining directory / postcard program.

## Rules
- NEVER fabricate transcript content. If the file is unreadable/missing, state
  it and ask for the path.
- Ground each recommendation in the transcript; quote sparingly.
- Keep it concise and Telegram-friendly.

## Helper: transcripts dir
The profile keeps ingested transcripts under
`~/.hermes/profiles/eval_bot/workspace/transcripts/`. Save copies there when you
process them (organize by date).

## Profile setup (eval_bot)
- Profile: `eval_bot` (wrapper `eval_bot`), model `nvidia/nemotron-3-super-120b-a12b`
  (paid — the `:free` variant 429s on rate limits).
- Keys copied into `~/.hermes/profiles/eval_bot/.env` (OPENROUTER_API_KEY,
  OPENAI_API_KEY) from the default profile env.
- Google Workspace OAuth NOT yet configured (no google_token.json). Meet
  auto-transcripts live in Drive; until OAuth is set, rely on local transcript
  files or pasted text.
- Verify profile: `eval_bot chat -Q -q "test"` from `$HOME`.