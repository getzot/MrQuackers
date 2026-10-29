# Educational Video Script: Getting Started with the Government‑Contract Bot

**Title:** Getting Started with the Government‑Contract Bot – From Opportunity Discovery to Meeting Assistance  
**Target Audience:** Project managers, business development leads, contract administrators (comfortable with terminal, optional coding)  
**Estimated Length:** 8‑10 minutes  
**Format:** Narrated screen‑capture (voice‑over) with on‑screen callouts, arrows, and highlights.

---

## 1. Opening (0:00‑0:30)

**Visual:**  
- Fade in from black to the Government‑Contract Bot logo (simple icon: shield + gear).  
- Light, upbeat background music (low volume).  

**Narration:**  
> “Welcome to the Government‑Contract Bot – your AI‑powered assistant for discovering, bidding on, and managing U.S. federal contracts. In this quick‑start guide we’ll walk through the core components, show you how to configure the system, and demonstrate a typical workflow from opportunity discovery to meeting assistance.”

---

## 2. System Overview (0:30‑1:30)

**Visual:**  
- Animated diagram of the directory structure (folders: contracts/, opportunities/, meetings/, notion_integration/, config/, scripts/).  
- Each folder gets a callout label as we mention it.  

**Narration:**  
> “The bot is organized into four main skill bundles:  
> 1. **Government Contract Management** – the core workflow that tracks opportunities from discovery to award and closeout.  
> 2. **Opportunity Monitor** – watches SAM.gov for new contracts that match your filters.  
> 3. **Meeting Assistant** – joins Google Meet, transcribes audio, extracts action items, and can speak responses via TTS.  
> 4. **Proposal Writer** – helps you build compliant proposal sections, past‑performance write‑ups, and compliance matrices.  
> All skills share a common configuration folder and write data into the `government_contract_bot` workspace in your home directory.”

---

## 3. Prerequisites & Notion Setup (1:30‑2:30)

**Visual:**  
- Terminal window: `nano ~/.hermes/.env` (highlight the line to be added).  

**Narration:**  
> “First, you need a Notion integration token. Go to notion.so/my‑integrations, create an integration, copy the token, and add it to your Hermes environment file:  
> `echo 'NOTION_API_KEY=your_token_here' >> ~/.hermes/.env`  
> Save the file.  

> Next, run the Notion‑setup helper (we’ll show it in a moment) to create the three databases: Contracts, Tasks, and Subcontractors. The helper reads the schema from `references/notion_setup.md` and creates the databases in your Notion workspace.”

**Visual:**  
- Terminal running the helper script:  
> `python ~/.hermes/skills/business/government-contract-management/scripts/notion_helper.py init-db`  

**Narration (continued):**  
> “You should see confirmation that the databases were created. Take note of the database IDs – they’ll appear in the output; you’ll need them later if you want to customize the helper.”

---

## 4. Configuring the Opportunity Monitor (2:30‑3:30)

**Visual:**  
- Open `~/government_contract_bot/config/opportunity_monitor/filters.json` in an editor (e.g., VS Code). Highlight each field as we discuss it.  

**Narration:**  
> “Open the filters file. Here you can define the NAICS codes you serve, the set‑aside types you’re eligible for, minimum and maximum contract values, target agencies, and keywords that must appear in the title or description.  

> Example: if you focus on IT services for the GSA, set NAICS to `541511`, set‑aside to `SB`, agency list to `GSA`, and keywords like `[\"IT support\",\"cloud\",\"cybersecurity\"]`.  
> Save the file.”

**Visual:**  
- Terminal running the monitor script:  
> `python ~/.hermes/skills/business/opportunity-monitor-subagent/scripts/opportunity_monitor.py`  

**Narration (continued):**  
> “To test the monitor, run the provided script. The script will load your filters, call the SAM.gov API (you’ll need to set `SAMGOV_API_KEY` in `.env` for live data), filter results, and create tracking directories under `opportunities/new/`.  
> Each opportunity gets a plain‑text `opportunity.txt` with all the key fields and a link back to SAM.gov.”

**Visual:**  
- Show a sample `opportunity.txt` (call out Opportunity ID, Title, Agency, Posted Date, Response Due, NAICS, Set Aside, Value, Link).  

**Narration (continued):**  
> “You can now review these files manually, or let the bot automatically move an opportunity to the `pending` folder when you decide to pursue it – we’ll see that in the next step.”

---

## 5. Moving an Opportunity into Active Tracking & Generating a Proposal (3:30‑4:30)

**Visual:**  
- Terminal commands (step‑by‑step) with annotations:  
> `mv ~/government_contract_bot/opportunities/new/SAM-2026-00123 ~/government_contract_bot/opportunities/pending/`  
> `mkdir -p ~/government_contract_bot/contracts/pending/SAM-2026-00123`  
> `cp ~/government_contract_bot/opportunities/pending/SAM-2026-00123/opportunity.txt ~/government_contract_bot/contracts/pending/SAM-2026-00123/`  
> `cp ~/government_contract_bot/contracts/templates/government_proposal_template.md ~/government_contract_bot/contracts/pending/SAM-2026-00123/proposal.md`  

**Narration:**  
> “When you’re ready to bid, move the opportunity from `new` to `pending`, create a contract tracking folder, copy the opportunity details, and drop in the government proposal template.  
> Open `proposal.md` – you’ll see a full outline with sections like Executive Summary, Technical Approach, Management Plan, Past Performance, and a Compliance Matrix placeholder.”

**Visual:**  
- Open the proposal template, highlight the Technical Approach section and its prompts.  

**Narration (continued):**  
> “Each section includes prompts and required elements. For example, the Technical Approach expects you to describe your understanding of the requirements, proposed solution, methodology, work breakdown structure, staffing plan, and risk mitigation.  
> Fill in the sections using your team’s expertise. The Proposal Writer subagent can help generate draft text: just run the helper script with the section name and any notes you provide.”

**Visual:**  
- Terminal running the proposal writer helper:  
> `python ~/.hermes/skills/business/proposal-writer-subagent/scripts/proposal_writer.py --section technical_approach --notes \"Use microservices, AWS GovCloud, zero‑trust networking\"`  

**Narration (continued):**  
> “The script will output a draft paragraph that you can paste into the proposal file. Repeat for each section, then run a final compliance check (the helper also generates a compliance matrix based on the solicitation’s Section L/M).”

---

## 6. Using the Meeting Assistant (4:30‑5:30)

**Visual:**  
- Open `~/government_contract_bot/config/meeting_assistant/settings.json`. Highlight `tts_enabled`, `tts_provider`, `transcription_service`, and the `keywords.json` reference.  

**Narration:**  
> “Open the meeting assistant settings. Set `tts_enabled` to `true` if you want the bot to speak during meetings, choose your TTS provider (OpenAI is default), and select a transcription service – Whisper works offline, or you can use Google Cloud Speech‑to‑Text for higher accuracy.  
> Also list the keywords you want the bot to listen for (action items, decisions, questions) – the default list covers most business meetings.”

**Visual:**  
- Show a sample Google Meet URL (e.g., `https://meet.google.com/abc-defg-hij`).  

**Narration (continued):**  
> “To start a meeting, copy the Google Meet link from your calendar. Then run the meeting assistant script, passing the URL:  
> `python ~/.hermes/skills/business/meeting-assistant-subagent/scripts/meeting_join.py --url https://meet.google.com/abc-defg-hij`  
> The script will launch Chrome, join the meeting (you may need to sign in), capture the system audio, stream it to Whisper for live transcription, and display the transcript in the terminal.”

**Visual:**  
- Show live transcript with a highlighted action‑item sentence (e.g., “We will deliver the prototype by Friday”).  
- Call out the extracted action item: responsible party, due date.  

**Narration (continued):**  
> “Whenever the bot detects an action‑item keyword (e.g., ‘we will’, ‘action item’, ‘deadline’), it logs the sentence, extracts the responsible party and due date if present, and creates a task entry in your Notion Tasks database.  
> If a decision is detected, it’s added to the meeting minutes.  
> If you’ve enabled TTS and the bot detects a direct question addressed to it (e.g., ‘Bot, what’s our timeline?’), it will generate a short response using the LLM and speak it through your speakers.”

**Visual:**  
- Show the Notion task created (title, related contract, description, link to meeting recording).  

**Narration (continued):**  
> “After the meeting ends, you’ll find a new task in Notion under the related contract, with the description pulled from the transcript and a link back to the meeting recording (if you enabled recording).”

---

## 7. Automating with Cron Jobs (5:30‑6:00)

**Visual:**  
- Terminal showing a cronjob creation command:  
> `cronjob create --name 'opp-monitor' --schedule 'every 2h' --prompt 'Run opportunity monitor scan' --skills opportunity-monitor-subagent,government-contract-management`  

**Narration:**  
> “To keep the opportunity monitor running without manual intervention, add a cron job:  
> `cronjob create --name 'opp-monitor' --schedule 'every 2h' --prompt 'Run opportunity monitor scan' --skills opportunity-monitor-subagent,government-contract-management`  
> Similarly, you can schedule the meeting assistant to check for upcoming Google Meet events from your calendar and join them automatically.”

---

## 8. Closing & Next Steps (6:00‑6:30)

**Visual:**  
- Bot logo again, with a checklist of features appearing with checkmarks.  

**Narration:**  
> “You now have a fully functional government‑contract bot that can:  
> • Discover and filter SAM.gov opportunities automatically.  
> • Track each opportunity through the full contract lifecycle.  
> • Generate compliant proposal drafts and compliance matrices.  
> • Join Google Meet meetings, transcribe speech, extract action items, and speak responses via TTS.  
> • Sync all tasks, contracts, and subcontractor data to Notion for project‑management visibility.  

> From here, you can tailor the filters, add your own past‑performance templates, integrate additional data sources (e.g., beta.SAM.gov, DSBS), or expand the meeting assistant to handle Zoom or Teams.  

> Thank you for watching – happy contracting!”

**Visual:**  
- Fade out with music and a link to the internal repo / documentation (e.g., `https://internal.repo/govcontract-bot`).  

---

### Notes for Production

- **Audio Capture:** The meeting assistant script uses `pyaudio` to capture system output; ensure the appropriate loopback device is configured on your OS.  
- **SAM.gov API:** To enable live data, add `SAMGOV_API_KEY=your_key` to `~/.hermes/.env`.  
- **TTS/STT Alternatives:** Edit `config/meeting_assistant/settings.json` to switch providers (see the README in the skill folder).  
- **Error Logs:** Check `~/government_contract_bot/logs/` for each subagent’s log file if something doesn’t work as expected.  

--- 

*End of Script*