---
name: government-contract-management
description: "Manage government contracts and subcontractor workflows."
version: 0.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [government, contracts, procurement, notion, tts]
    related_skills: [notion, hermes-agent]
---

# Government Contract Management Skill

This skill provides workflows for managing government contracts from opportunity identification through award and execution, including subcontractor management.

## When to Use This Skill

Use this skill when:
- Identifying and tracking government contract opportunities on SAM.gov and other platforms
- Managing the bid/proposal process for government contracts
- Coordinating subcontractor relationships and work
- Tracking contract milestones, deliverables, and payments
- Integrating with Notion for task and project management

## Directory Structure Assumptions

This skill assumes the following directory structure:
```
government_contract_bot/
├── contracts/
│   ├── active/
│   ├── pending/
│   ├── awarded/
│   └── templates/
├── notion_integration/
├── skills/
├── memory/
├── logs/
���������������└── subagents/
```

## Core Workflows

### 1. Opportunity Identification and Tracking

```bash
# Search for new opportunities on SAM.gov
# (This would typically involve web scraping or API calls to SAM.gov)

# Create a new contract tracking directory
mkdir -p contracts/pending/{OPPORTUNITY_ID}

# Save opportunity details
echo "Opportunity ID: {OPPORTUNITY_ID}" > contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "Title: {TITLE}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "Agency: {AGENCY}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "Posted Date: {DATE}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "Response Due: {DUE_DATE}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "NAICS Code: {NAICS}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt
echo "Set Aside: {SET_ASIDE}" >> contracts/pending/{OPPORTUNITY_ID}/opportunity.txt

# Create Notion database entry for tracking
# (See Notion integration section below)
```

### 2. Bid/Proposal Management

```bash
# Move opportunity to active bidding phase
mv contracts/pending/{OPPORTUNITY_ID} contracts/active/

# Create proposal template
cp contracts/templates/government_proposal_template.md contracts/active/{OPPORTUNITY_ID}/proposal.md

# Schedule proposal development tasks
# (This would create tasks in Notion for team members)

# Track proposal progress
# Update opportunity.txt with completion percentages
```

### 3. Award and Contract Setup

```bash
# Move to awarded status upon win
mv contracts/active/{OPPORTUNITY_ID} contracts/awarded/

# Create contract file with key terms
echo "Contract Number: {CONTRACT_NUMBER}" > contracts/awarded/{OPPORTUNITY_ID}/contract.txt
echo "Award Date: {AWARD_DATE}" >> contracts/awarded/{OPPORTUNITY_ID}/contract.txt
echo "Period of Performance: {POP_START} to {POP_END}" >> contracts/awarded/{OPPORTUNITY_ID}/contract.txt
echo "Total Value: {VALUE}" >> contracts/awarded/{OPPORTUNITY_ID}/contract.txt
echo "Contracting Officer: {OFFICER_NAME}" >> contracts/awarded/{OPPORTUNITY_ID}/contract.txt
echo "POC Email: {OFFICER_EMAIL}" >> contracts/awarded/{OPPORTUNITY_ID}/contract.txt

# Create subcontractor management directory
mkdir -p contracts/awarded/{OPPORTUNITY_ID}/subcontractors

# Set up Notion project for contract execution
# (See Notion integration section below)
```

### 4. Subcontractor Management

```bash
# For each subcontractor:
# Create subcontractor profile
echo "Subcontractor: {SUBCONTRACTOR_NAME}" > contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "DUNS: {DUNS}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "CAGE Code: {CAGE}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "NAICS: {SUBCONTRACTOR_NAICS}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "Work Scope: {WORK_DESCRIPTION}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "Contract Value: {SUBCONTRACT_VALUE}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "Start Date: {START_DATE}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt
echo "End Date: {END_DATE}" >> contracts/awarded/{OPPORTUNITY_ID}/subcontractors/{SUBCONTRACTOR_ID}/profile.txt

# Create Notion tasks for subcontractor oversight
# (See Notion integration section below)
```

### 5. Contract Closeout

```bash
# Move to completed status
mv contracts/awarded/{OPPORTUNITY_ID} contracts/completed/

# Generate final report
# (Compile all documentation, payments, performance metrics)

# Archive contract files
# (Move to long-term storage if needed)
```

## Notion Integration

This skill integrates with Notion for task and project management. The following database structures are recommended:

### Contracts Database
- **Contract ID** (Title)
- **Opportunity ID** (Text)
- **Agency** (Select)
- **Status** (Select: Pending, Active, Awarded, Completed, Lost)
- **Posted Date** (Date)
- **Response Due** (Date)
- **Award Date** (Date)
- **Contract Value** (Number)
- **Prime Contractor** (Text)
- **Contracting Officer** (Text)
- **POC Email** (Email)
- **NAICS Code** (Text)
- **Set Aside** (Select)
- **Notes** (Text)

### Tasks Database
- **Task Name** (Title)
- **Related Contract** (Relation to Contracts database)
- **Assigned To** (Person)
- **Due Date** (Date)
- **Status** (Select: Not Started, In Progress, Blocked, Completed)
- **Priority** (Select: Low, Medium, High, Urgent)
- **Task Type** (Select: Proposal Development, Compliance, Reporting, Subcontractor Management, etc.)
- **Description** (Text)

### Subcontractors Database
- **Subcontractor Name** (Title)
- **Related Contract** (Relation to Contracts database)
- **DUNS Number** (Text)
- **CAGE Code** (Text)
- **NAICS Code** (Text)
- **Work Scope** (Text)
- **Contract Value** (Number)
- **Start Date** (Date)
- **End Date** (Date)
- **Status** (Select: Pending, Active, Completed, Terminated)
- **Performance Rating** (Select: Excellent, Good, Fair, Poor)
- **Notes** (Text)

## TTS Configuration

For text-to-speech functionality during meetings:

1. Configure TTS in Hermes:
   ```bash
   hermes config set tts.provider openai
   hermes config set tts.voice alloy
   hermes config set tts.speed 1.0
   ```

2. Use the `text_to_speech` tool for meeting responses:
   ```python
   from hermes_tools import text_to_speech
   
   response = text_to_speech(
       text="Your response here",
       provider="openai"
   )
   # Play or deliver the audio file
   ```

## Google Meet Listening Capabilities

To listen and respond in Google Meet meetings:

1. Use browser automation to join and monitor Google Meet
2. Extract audio from the meeting
3. Use speech-to-text to convert audio to text
4. Process the text for meeting content and action items
5. Generate appropriate responses
6. Use TTS to deliver responses (if appropriate)

## Specialized Subagents

Consider creating these subagents for specialized functions:

### 1. Opportunity Monitor Subagent
- Continuously monitors SAM.gov and other platforms for new opportunities
- Filters opportunities based on business criteria (NAICS, set-asides, value, etc.)
- Creates initial tracking records

### 2. Proposal Writer Subagent
- Assists in developing government proposals
- Ensures compliance with solicitation requirements
- Helps with pricing volume development
- Manages past performance references

### 3. Compliance Monitor Subagent
- Tracks contract compliance requirements
- Monitors deliverable due dates
- Ensures proper invoicing and reporting
- Manages government correspondence

### 4. Subcontractor Manager Subagent
- Manages subcontractor relationships
- Tracks supplier performance
- Handles payment applications
- Manages change orders and modifications

### 5. Meeting Assistant Subagent
- Listens to and processes meeting content
- Extracts action items and decisions
- Generates meeting minutes
- Provides real-time assistance during meetings

## Best Practices for Government Contracting

Based on research from SAM.gov and other sources:

1. **Registration Requirements**:
   - Maintain active SAM.gov registration
   - Keep UEI (Unique Entity Identifier) current
   - Maintain current representations and certifications

2. **Bid Preparation**:
   - Carefully read the entire solicitation
   - Attend pre-bid conferences when offered
   - Ask questions during the Q&A period
   - Ensure all required forms are completed
   - Price competitively but profitably

3. **Proposal Development**:
   - Follow the solicitation's format instructions exactly
   - Provide clear, concise responses
   - Reference past performance relevant to the requirement
   - Include all required certifications
   - Proofread for completeness and accuracy

4. **Subcontractor Management**:
   - Flow down required contract clauses to subcontractors
   - Monitor subcontractor performance regularly
   - Maintain proper documentation of payments
   - Ensure subcontractors maintain required certifications

5. **Contract Administration**:
   - Maintain open communication with the contracting officer
   - Submit invoices and reports on time
   - Track all contract modifications
   - Maintain adequate insurance coverage
   - Prepare for government audits

## Verification Steps

To verify this skill is working correctly:

1. Check that the directory structure is created
2. Verify Notion integration is configured
3. Test TTS functionality
4. Validate opportunity tracking workflow
5. Confirm proposal template creation
6. Verify contract award processing
7. Check subcontractor management functions

## Troubleshooting

- **Notion API errors**: Verify NOTION_API_KEY is correctly set
- **Directory permission issues**: Ensure proper read/write permissions
- **TTS not working**: Check TTS provider configuration
- **Meeting listening issues**: Verify browser automation setup

## References

- SAM.gov Contracting: https://sam.gov/contracting
- Federal Acquisition Regulation (FAR): https://www.acquisition.gov/far
- SBA Government Contracting Guide: https://www.sba.gov/business-guide/grow-your-business/government-contracting
- Notion API Documentation: https://developers.notion.com

## Example Usage

```bash
# Load this skill
skill_view(name='government-contract-management')

# Create a new opportunity tracking record
# (Would be called from opportunity monitoring)

# Update contract status in Notion
# (Would be called when status changes)

# Generate a meeting response using TTS
# (Would be called during Google Meet participation)
```

## Maintenance

- Review and update workflows quarterly
- Keep templates current with regulation changes
- Monitor Notion API for updates
- Backup contract records regularly
- Archive completed contracts appropriately