---
name: meeting-assistant-subagent
description: "Assists in meetings with transcription and responses."
version: 0.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [meeting, transcription, tts, voice, google-meet]
    related_skills: [government-contract-management, notion]
---

# Meeting Assistant Subagent Skill

This skill provides a specialized subagent for listening to Google Meet meetings, transcribing audio, extracting action items, and providing real-time assistance including TTS responses when appropriate.

## When to Use This Skill

Use this skill when you need to:
- Listen to and transcribe Google Meet meetings in real-time
- Extract action items, decisions, and key points from meeting content
- Generate meeting minutes and summaries
- Provide contextual responses during meetings (with TTS capability)
- Integrate meeting outcomes with task and project management systems
- Track meeting attendance and participation

## Directory Structure Assumptions

This skill assumes the following directory structure within your government_contract_bot:
```
government_contract_bot/
├── meetings/
│   ├── upcoming/         # Scheduled meetings
│   ├── active/           # Currently running meetings
│   ├── completed/        # finished meetings with transcripts
│   └── templates/        # Meeting templates and agendas
├── config/
│   └── meeting_assistant/
│       ├── settings.json # Meeting assistant configuration
│       └── keywords.json # Action item detection keywords
├── transcripts/
│   └── {MEETING_ID}/     # Transcripts and audio files
├── logs/
│   └── meeting_assistant.log
���└── notion_integration/   # For syncing with Notion
```

## Core Functionality

### 1. Configuration Setup

Create configuration files for the meeting assistant:

```bash
# Create config directory
mkdir -p ~/government_contract_bot/config/meeting_assistant

# Create settings configuration
cat > ~/government_contract_bot/config/meeting_assistant/settings.json << 'EOF'
{
  "transcription_service": "whisper",  # or "google", "azure", etc.
  "tts_enabled": true,
  "tts_provider": "openai",
  "tts_voice": "alloy",
  "auto_join_meetings": false,
  "record_audio": true,
  "extract_action_items": true,
  "generate_summary": true,
  "sync_to_notion": true,
  "notification_channels": ["telegram", "slack", "email"],
  "meeting_platforms": ["google_meet", "zoom", "teams"]
}
EOF

# Create keywords configuration for action item detection
cat > ~/government_contract_bot/config/meeting_assistant/keywords.json << 'EOF'
{
  "action_indicators": [
    "action item",
    "todo",
    "need to",
    "will",
    "going to",
    "responsible for",
    "assign",
    "deliver",
    "complete",
    "finish",
    "by",
    "deadline",
    "due date",
    "follow up",
    "next steps"
  ],
  "decision_indicators": [
    "decided",
    "decision",
    "agreed",
    "approved",
    "concurred",
    "will proceed",
    "we will",
    "it is decided",
    "resolved"
  ],
  "question_indicators": [
    "question",
    "ask",
    "wonder",
    "unclear",
    "need clarification",
    "what about",
    "how will",
    "when will",
    "who will",
    "why"
  ]
}
EOF
```

### 2. Meeting Processing Workflow

The subagent performs these steps during a meeting:

#### Step 1: Join and Monitor Meeting
```bash
# This would typically involve:
# 1. Using browser automation to join Google Meet
# 2. Capturing audio from the meeting
# 3. Converting speech to text in real-time
# 4. Processing the transcript for insights

# Example pseudocode:
meeting_browser = join_google_meet(meeting_url)
audio_stream = capture_audio(meeting_browser)
transcript_stream = speech_to_text(audio_stream)

for transcript_chunk in transcript_stream:
    process_transcript_chunk(transcript_chunk)
    check_for_action_items(transcript_chunk)
    check_for_questions(transcript_chunk)
    generate_contextual_responses(transcript_chunk)
```

#### Step 2: Extract Action Items and Decisions
```bash
# When action items are detected:
if contains_action_indicators(transcript_chunk):
    action_item = extract_action_item(transcript_chunk)
    save_action_item(action_item, meeting_id)
    create_notion_task(action_item)  # If Notion integration enabled
    send_notification(action_item)   # Via configured channels

# When decisions are detected:
if contains_decision_indicators(transcript_chunk):
    decision = extract_decision(transcript_chunk)
    save_decision(decision, meeting_id)
    update_meeting_notes(decision, meeting_id)
```

#### Step 3: Generate Contextual Responses (Optional)
```bash
# When appropriate to respond (based on meeting context):
if should_respond_to_question(transcript_chunk) and tts_enabled:
    response = generate_meeting_response(transcript_chunk, meeting_context)
    audio_response = text_to_speech(response, tts_provider, tts_voice)
    play_audio_response(audio_response)  # Through meeting audio
```

#### Step 4: Post-Meeting Processing
```bash
# After meeting ends:
generate_meeting_summary(transcript, meeting_id)
create_meeting_minutes(transcript, attendees, decisions, action_items)
save_transcript_and_audio(transcript, audio_file, meeting_id)
sync_with_notion(meeting_data)  # If enabled
create_follow_up_tasks(action_items)
update_project_status(decisions, action_items)
```

## Integration with Government Contract Management

### Creating Meeting Records
```bash
# When a government contract meeting is scheduled:
mkdir -p ~/government_contract_bot/meetings/upcoming/{MEETING_ID}

# Save meeting details
echo "Meeting ID: {MEETING_ID}" > ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Title: {MEETING_TITLE}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Contract ID: {CONTRACT_ID}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Date: {MEETING_DATE}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Time: {MEETING_TIME}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Attendees: {ATTENDEES_LIST}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Agenda: {MEETING_AGENDA}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
echo "Purpose: {MEETING_PURPOSE}" >> ~/government_contract_bot/meetings/upcoming/{MEETING_ID}/meeting_info.txt
```

### Linking Meetings to Contracts
```bash
# In the contract tracking directory, reference meeting records
echo "Related Meetings:" >> ~/government_contract_bot/contracts/active/{CONTRACT_ID}/meetings.txt
echo "- {MEETING_ID}: {MEETING_TITLE} on {MEETING_DATE}" >> ~/government_contract_bot/contracts/active/{CONTRACT_ID}/meetings.txt
```

### Creating Follow-up Tasks from Meetings
```bash
# After meeting, create tasks in Notion or local tracking
# For each action item:
ntn api v1/pages \
  parent[database_id]=YOUR_TASKS_DATABASE_ID \
  properties[Task Name][title][0][text][content]="Follow-up from meeting: {ACTION_ITEM}" \
  properties[Related Contract][relation][0][id]="PAGE-ID-OF-CONTRACT" \
  properties[Assigned To][people][0][object]="user" \
  properties[Due Date][date][start="{DUE_DATE}"] \
  properties[Status][select][name]="Not Started" \
  properties[Priority][select][name]="{PRIORITY}" \
  properties[Task Type][select][name]="Meeting Follow-up" \
  properties[Description][rich_text][0][text][content]="Action item from meeting {MEETING_ID}: {ACTION_ITEM_DETAILS}"
```

## Technical Implementation Notes

### Audio Processing Pipeline
1. **Audio Capture**: Use browser automation or system audio capture
2. **Speech-to-Text**: Options include:
   - OpenAI Whisper (local or API)
   - Google Cloud Speech-to-Text
   - Azure Speech Services
   - Mozilla DeepSpeech (local)
3. **Text Processing**: Natural language processing for insight extraction
4. **Response Generation**: Context-appropriate responses using LLM
5. **Text-to-Speech**: OpenAI TTS, ElevenLabs, or local TTS engines

### Browser Automation for Google Meet
```bash
# Example approach using selenium or playwright:
# 1. Launch Chrome/Firefox with appropriate permissions
# 2. Navigate to Google Meet URL
# 3. Handle authentication if needed
# 4. Join meeting with audio enabled
# 5. Capture audio stream from the meeting
# 6. Process audio through speech-to-text pipeline
```

### Privacy and Security Considerations
- Obtain consent before recording meetings
- Securely store transcripts and audio files
- Comply with organizational recording policies
- Encrypt sensitive meeting content
- Provide options to disable recording/transcription

## Best Practices for Meeting Assistance

1. **Selective Participation**: Only join meetings where assistance is truly needed
2. **Clear Indicators**: Make it obvious when the bot is listening/recording
3. **Action Item Clarity**: Ensure extracted action items have clear owners and deadlines
4. **Meeting Summaries**: Generate concise summaries that capture key points
5. **Follow-up Tracking**: Ensure action items from meetings are properly tracked
6. **Context Awareness**: Understand meeting purpose and provide relevant assistance
7. **Response Appropriateness**: Only respond when it adds value to the discussion
8. **Audio Quality**: Ensure good audio capture for accurate transcription
9. **Language Support**: Configure for appropriate languages and accents
10. **Backup Systems**: Have fallback transcription methods if primary fails

## Verification Steps

To verify this skill is working correctly:

1. Check that configuration files are created in ~/government_contract_bot/config/meeting_assistant/
2. Verify the directory structure for meeting tracking exists
3. Confirm that the meeting processing logic would correctly join meetings
4. Validate that action item detection would work with sample transcripts
5. Check that integration points with task management are present
6. Test TTS functionality (if enabled)
7. Verify audio capture and processing pipeline concepts

## Maintenance

- Review and update action/decision/question keywords quarterly
- Test transcription accuracy with different accents and audio qualities
- Update TTS settings based on voice preferences and quality
- Monitor meeting assistant performance and adjust sensitivity
- Keep up with changes to Google Meet UI/API
- Backup meeting transcripts and audio according to retention policies
- Train on domain-specific vocabulary (government contracting terms)

## Example Usage

```bash
# Load the meeting assistant skill
skill_view(name='meeting-assistant-subagent')

# Join and assist in a specific meeting
delegate_task(
    goal='Join Google Meet meeting and provide real-time transcription and assistance',
    context='Meeting URL: https://meet.google.com/xxx-yyy-zzz; TTS enabled for responding to direct questions'
)

# Or set up automated meeting assistance for scheduled meetings
cronjob(
    action='create',
    name='meeting-assistant-scheduled',
    schedule='0 9 * * 1-5',  # Weekdays at 9 AM (adjust as needed)
    prompt='Check for and join scheduled government contract meetings',
    skills=['meeting-assistant-subagent', 'government-contract-management']
)
```

## References

- Google Meet API: https://developers.google.com/meet
- Web Speech API: https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API
- OpenAI Whisper: https://github.com/openai/whisper
- OpenAI TTS: https://platform.openai.com/docs/guides/text-to-speech
- Browser Automation: https://playwright.dev/ or https://www.selenium.dev/
- Meeting Transcription Best Practices: Various accessibility and productivity resources