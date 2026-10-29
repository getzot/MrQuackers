# Improvement Plan for Government‑Contract Bot System

## Identified Shortcomings

| Area | Shortcoming | Impact |
|------|-------------|--------|
| **SAM.gov Integration** | Opportunity Monitor uses a simulated scan; no real API calls or web‑scraping. | Limits real‑time opportunity discovery; requires manual data entry. |
| **Meeting Assistant – Audio Capture** | No concrete implementation for joining Google Meet and capturing audio. | Users cannot yet leverage real‑time transcription and TTS responses. |
| **Speech‑to‑Text / TTS Provider Lock‑in** | Scripts hard‑code OpenAI Whisper and TTS. | Prevents use of local/private models, increases cost, and may violate data‑privacy policies. |
| **Notion Integration Automation** | Only a guide exists; no ready‑to‑run script for creating/updating Notion databases or entries. | Increases setup friction and risk of misconfiguration. |
| **Error Handling & Logging** | Minimal try/except; reliance on print statements. | Hard to diagnose issues in production; no structured logs or alerting. |
| **User Interface / Interaction** | All interaction via command line or Hermes chat; no UI for non‑technical users. | Limits adoption by project managers, executives, or clients who prefer a visual dashboard. |
| **Version Control & Deployability** | Skills and scripts copied into ~/.hermes/skills/ with no clear versioning or CI/CD pipeline. | Makes updates, rollbacks, and collaboration difficult. |
| **Training & Documentation** | Users must read raw Markdown to understand how to run each subagent. | Increases learning curve and support burden. |

## Improvement Plan

| Priority | Improvement | Description | Implementation Steps |
|----------|-------------|-------------|----------------------|
| **High** | **Real SAM.gov Connector** | Replace simulated scan with a robust API client (API key‑based, fallback to HTML scraping). Include pagination, rate‑limit handling, and local caching to avoid duplicate processing. | 1. Create `samgov_client.py` under `opportunity-monitor-subagent/scripts/`.<br>2. Use the official SAM.gov Opportunities API: `https://api.sam.gov/prod/opportunities/v2/search`.<br>3. Store API key in `.env` (e.g., `SAMGOV_API_KEY`).<br>4. Implement retry/back‑off (exponential) and SQLite cache.<br>5. Update the monitor script to call this client. |
| **High** | **Meeting Audio Capture & STT/TTS Abstraction** | Provide a concrete meeting‑joiner that can launch Chrome/Firefox with proper permissions, join a Google Meet URL, capture system audio, and feed it to a pluggable STT engine. Abstract STT/TTS behind interfaces to allow alternative providers (Vosk, Coqui TTS, Azure, etc.). | 1. Write `meeting_join.py` (under `meeting-assistant-subagent/scripts/`).<br>2. Use Playwright for browser control.<br>3. Use `pyaudio` or `sounddevice` to capture loopback audio.<br>4. Define base classes `STTProvider` and `TTSProvider` in `utils/tts_stt.py`.<br>5. Implement `OpenAIWhisperSTT`, `VoskSTT`, `OpenAITTS`, `CoquiTTS`.<br>6. Add configuration options in `config/meeting_assistant/settings.json` (`stt_provider`, `tts_provider`).<br>7. Update the meeting assistant script to use the abstractions. |
| **Medium** | **Notion Automation Helper** | Ship a ready‑to‑run script that reads the database schemas from `references/notion_setup.md` (or a JSON schema) and creates the databases/pages via the Notion API. Also provide functions for syncing tasks, contracts, and subcontractors. | 1. Create `notion_helper.py` under `government-contract-management/scripts/` (if not exists) or expand the existing helper.<br>2. Parse the schema (JSON or Markdown) to define required databases and properties.<br>3. Use the Notion API (via the existing `notion` skill or direct HTTP) to create databases if they don’t exist.<br>4. Provide CLI commands: `notion-helper init-db`, `notion-helper sync-contract <contract-id>`, `notion-helper sync-task <task-id>`. |
| **Medium** | **Structured Logging & Alerting** | Replace `print` with the `logging` module (JSON‑format logs). Add rotating file handlers and optional alerts (e.g., send a Telegram message on critical errors). | 1. Configure logging in each subagent’s `__main__` block (log to `~/government_contract_bot/logs/<subagent>.log`).<br>2. Add a `notify_on_error` wrapper that uses the `send_message` tool (Telegram/Slack) if configured.<br>3. Log key events: opportunity discovered, meeting started/ended, proposal section generated, etc. |
| **Low** | **Lightweight Web Dashboard** | Build a simple web interface (Flask/FastAPI) that reads from the local SQLite/JSON store and Notion (if synced) to show: opportunity pipeline, contract status, upcoming meetings, and task lists. | 1. Create a `dashboard/` folder with a minimal Flask app.<br>2. Define routes for `/opportunities`, `/contracts`, `/meetings`, `/tasks`.<br>3. Secure with basic auth or integrate with Hermes OAuth.<br>4. Provide a `docker-compose.yml` for easy deployment (optional). |
| **Low** | **Version Control & CI/CD** | Add a `Makefile` or `justfile` that runs tests, lints, and pushes updates to the Hermes skill repo (if using a fork). Include a `requirements.txt` for each subagent. | 1. Standardize each subagent with a `setup.py` or `pyproject.toml`.<br>2. Add a `requirements.txt` listing dependencies (e.g., `requests`, `playwright`, `pyaudio`, `openai`, `notion-sdk`).<br>3. Create a CI workflow (GitHub Actions) that runs `pytest` and `flake8` on push to `main`.<br>4. Document the release process (tag, bump version in SKILL.md). |
| **Low** | **Enhanced Training Materials** | In addition to the video script, produce a one‑page cheat sheet and a FAQ. | 1. Create `references/cheat_sheet.md` with common commands and workflows.<br>2. Create `references/faq.md` addressing typical issues (API keys, audio capture, Notion permissions).<br>3. Ensure the video script is used to produce a short tutorial video (as already planned). |

## Success Metrics

After implementing the improvements, we expect:

- **Opportunity Discovery**: >90% of relevant SAM.gov opportunities are captured automatically within 15 minutes of posting.
- **Meeting Assistance**: Real‑time transcription latency <2 seconds; action‑item extraction accuracy >85% (measured against manual review).
- **Notion Sync**: Zero manual entry required for contract/task creation; all data flows bidirectionally.
- **System Reliability**: <1% error rate in logs over a month of operation; alerts fire within 30 seconds of a failure.
- **User Adoption**: Positive feedback from pilot users; reduction in time spent on opportunity tracking and meeting follow‑up by at least 30 %.

## Next Steps

1. **Prioritize** the High‑priority items (SAM.gov connector and meeting audio/STT‑TTS abstraction) to achieve a functional MVP.
2. **Implement** the Medium‑priority items (Notion helper, logging) to improve robustness and usability.
3. **Roll out** the Low‑priority items (dashboard, CI/CD, training) as time and resources allow.
4. **Gather Feedback** from a small pilot team after each iteration and adjust the plan accordingly.

--- 

*This improvement plan is intended to be a living document; update it as new shortcomings are discovered or as priorities shift.* 
