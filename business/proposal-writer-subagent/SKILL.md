---
name: proposal-writer-subagent
description: "Assists in developing government contract proposals."
version: 0.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [proposal, writing, government-contracts, sam.gov, tts]
    related_skills: [government-contract-management, opportunity-monitor-subagent]
---

# Proposal Writer Subagent Skill

This skill provides a specialized subagent for assisting with the development of government contract proposals, ensuring compliance with solicitation requirements and helping teams create winning proposals.

## When to Use This Skill

Use this skill when you need to:
- Develop proposals for government contract opportunities
- Ensure compliance with solicitation requirements (SOW, PWS, evaluation criteria)
- Create proposal outlines and section content
- Manage past performance references
- Coordinate proposal team efforts
- Generate compliance matrices
- Format proposals according to government standards

## Directory Structure Assumptions

This skill assumes the following directory structure within your government_contract_bot:
```
government_contract_bot/
├── contracts/
│   └── pending/
│       └── {OPPORTUNITY_ID}/
│           ├── proposal.md           # Main proposal document
│           ├── sections/             # Individual proposal sections
│           ├── past_performance/     # Past performance documentation
│           ├── cost_volume/          # Cost proposal documentation
│           └── compliance/           # Compliance tracking
├── templates/
│   ├── government_proposal_template.md
│   ├── section_templates/
│   └── past_performance_template.md
├── config/
│   └── proposal_writer/
│       ├── formatting_rules.json
│       └── section_requirements.json
├── logs/
│   └── proposal_writer.log
���└── notion_integration/   # For syncing with Notion
```

## Core Functionality

### 1. Configuration Setup

Create configuration files for the proposal writer:

```bash
# Create config directory
mkdir -p ~/government_contract_bot/config/proposal_writer

# Create formatting rules configuration
cat > ~/government_contract_bot/config/proposal_writer/formatting_rules.json << 'EOF'
{
  "font": "Times New Roman",
  "font_size": 12,
  "margins": {
    "top": 1,
    "bottom": 1,
    "left": 1,
    "right": 1
  },
  "spacing": "single",
  "page_numbering": "bottom_center",
  "header_format": "{CONTRACT_ID} - {SECTION_TITLE} - Page {PAGE} of {TOTAL_PAGES}",
  "section_templates": {
    "executive_summary": {
      "max_pages": 2,
      "required_elements": ["understanding", "approach", "value_proposition", "past_performance_summary"]
    },
    "technical_approach": {
      "max_pages": 15,
      "required_elements": ["understanding_of_requirements", "proposed_solution", "methodology", "work_breakdown_structure", "staffing_plan", "risk_mitigation"]
    },
    "management_plan": {
      "max_pages": 5,
      "required_elements": ["project_management_approach", "communication_plan", "safety_and_security"]
    },
    "past_performance": {
      "max_pages": 5,
      "required_elements": ["relevant_projects", "customer_references", "outcomes_and_metrics"]
    }
  }
}
EOF

# Create section requirements configuration
cat > ~/government_contract_bot/config/proposal_writer/section_requirements.json << 'EOF'
{
  "mandatory_sections": [
    "cover_page",
    "table_of_contents",
    "executive_summary",
    "technical_approach",
    "management_plan",
    "past_performance",
    "representations_and_certifications"
  ],
  "conditional_sections": {
    "small_business_subcontracting_plan": {
      "trigger": "contract_value_over_threshold",
      "threshold": 750000
    },
    "cost_proposal": {
      "trigger": "separate_volume_required"
    }
  },
  "evaluation_criteria_mapping": {
    "technical_competency": ["technical_approach", "past_performance"],
    "management_capability": ["management_plan", "past_performance"],
    "past_performance": ["past_performance"],
    "cost_realism": ["cost_proposal"]
  }
}
EOF
```

### 2. Proposal Development Workflow

The subagent assists with these steps:

#### Step 1: Initialize Proposal Development
```bash
# When moving opportunity to active phase:
mkdir -p ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/sections
mkdir -p ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/past_performance
mkdir -p ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/cost_volume
mkdir -p ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/compliance

# Copy master template
cp ~/government_contract_bot/contracts/templates/government_proposal_template.md \
   ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/proposal.md

# Create section templates
# (Would create individual section files based on configuration)
```

#### Step 2: Analyze Solicitation Requirements
```bash
# Extract and organize requirements from solicitation documents
# Create a requirements traceability matrix
# Identify evaluation criteria and submission instructions
```

#### Step 3: Develop Proposal Outline and Content
```bash
# For each section:
# 1. Review section requirements from configuration
# 2. Gather necessary information (technical details, past performance, etc.)
# 3. Generate section content using templates and prompts
# 4. Review for compliance with formatting rules
# 5. Save to appropriate section file
```

#### Step 4: Create Compliance Matrix
```bash
# Map solicitation requirements to proposal sections
# Track compliance status for each requirement
# Ensure all mandatory elements are addressed
```

#### Step 5: Manage Past Performance
```bash
# Select relevant past performance examples
# Format past performance descriptions
# Obtain customer contact information and permission references
```

#### Step 6: Coordinate Team Efforts
```bash
# Assign section owners
# Set internal review deadlines
# Manage version control
# Conduct color team reviews (pink, red, gold)
```

#### Step 7: Final Quality Assurance
```bash
# Check page limits
# Verify all required forms and certifications are included
# Ensure proper formatting and pagination
# Validate that cost proposal is separate if required
# Confirm submission instructions are followed
```

## Integration with Other Systems

### Notion Integration
```bash
# Track proposal development tasks in Notion
ntn api v1/pages \
  parent[database_id]=YOUR_TASKS_DATABASE_ID \
  properties[Task Name][title][0][text][content]="Complete technical approach section for {OPPORTUNITY_ID}" \
  properties[Related Contract][relation][0][id]="PAGE-ID-OF-CONTRACT" \
  properties[Assigned To][people][0][object]="user" \
  properties[Due Date][date][start="{SECTION_DUE_DATE}"] \
  properties[Status][select][name]="In Progress" \
  properties[Priority][select][name]="High" \
  properties[Task Type][select][name]="Proposal Writing" \
  properties[Description][rich_text][0][text][content]="Write the technical approach section for opportunity {OPPORTUNITY_ID}, including understanding of requirements, proposed solution, methodology, WBS, staffing plan, and risk mitigation."
```

### Meeting Assistant Integration
```bash
# Use meeting assistant during proposal development meetings
# Extract action items and decisions from proposal team meetings
# Create follow-up tasks from meeting outcomes
```

## Technical Implementation Notes

### Proposal Section Generation
The subagent can assist with generating content for each section using prompts and templates:

#### Executive Summary Prompt
```
Write an executive summary for a government contract proposal with the following details:
- Opportunity ID: {OPPORTUNITY_ID}
- Title: {TITLE}
- Agency: {AGENCY}
- NAICS: {NAICS}
- Set Aside: {SET_ASIDE}
- Value Estimate: ${VALUE}
- Key Requirements: {REQUIREMENTS}
- Our Solution: {SOLUTION_DESCRIPTION}
- Relevant Past Performance: {PAST_PERFORMANCE_SUMMARY}

The executive summary should:
1. Demonstrate clear understanding of the requirements
2. Present our proposed solution approach
3. Highlight key differentiators and value proposition
4. Summarize relevant past performance
5. Provide high-level cost and schedule information
6. Be limited to 2 pages
```

#### Technical Approach Prompt
```
Write the technical approach section for a government contract proposal addressing:
- Statement of Work: {SOW_DETAILS}
- Performance Work Statement: {PWS_DETAILS}
- Applicable Standards: {STANDARDS_LIST}
- Government-Furnished Property/Information: {GFP_LIST}
- Security Requirements: {SECURITY_REQS}

The technical approach should include:
1. Understanding of Requirements: Show we've thoroughly read and comprehend the solicitation
2. Proposed Solution: Describe our technical solution in detail
3. Methodology and Processes: Explain how we'll execute the work
4. Work Breakdown Structure: Break down the work into manageable components
5. Staffing Plan: Show organizational chart, key personnel, labor categories
6. Risk Identification and Mitigation: Identify risks and our mitigation strategies
7. Quality Assurance Procedures: Describe our QA processes
8. Deliverables Description: Detail what we'll deliver and acceptance criteria
```

### Past Performance Management
```bash
# Template for past performance entries
cat > ~/government_contract_bot/templates/past_performance_template.md << 'EOF'
# Past Performance Example

**Customer:** [Customer Name and Agency]
**Contract Number:** [Contract Number]
**Contract Value:** [Value]
**Period of Performance:** [Start Date] to [End Date]
**NAICS Code:** [Primary NAICS]
**Relevance to Current Opportunity:** [Explain relevance]

**Scope of Work:**
[Detailed description of work performed]

**Key Accomplishments and Results:**
- [Accomplishment 1 with metrics]
- [Accomplishment 2 with metrics]
- [Accomplishment 3 with metrics]

**Customer Reference:**
- Name: [Reference Name]
- Title: [Reference Title]
- Phone: [Phone Number]
- Email: [Email Address]
- Availability: [Best times to contact]

**Relevance Factors:**
- Similar scope of work: [Percentage match]
- Similar technologies used: [Percentage match]
- Similar environment: [Percentage match]
- Similar contract value: [Percentage match]
- Recent completion: [Years since completion]
EOF
```

### Compliance Matrix Generation
```bash
# Template for compliance matrix
cat > ~/government_contract_bot/templates/compliance_matrix_template.md << 'EOF'
# Compliance Matrix

| Requirement Reference | Requirement Description | Proposal Section | Page Number | Compliance Status | Comments |
|----------------------|------------------------|------------------|-------------|-------------------|----------|
| [Example: L.12.3.4]  | [Example: Submit resumes for all key personnel] | [Example: 3.2 Staffing Plan] | [Example: 18] | [Example: Fully Compliant] | [Example: All resumes included in Appendix B] |
|                      |                        |                  |             |                   |          |
|                      |                        |                  |             |                   |          |
|                      |                        |                  |             |                   |          |

**Compliance Summary:**
- Total Requirements: [COUNT]
- Fully Compliant: [COUNT]
- Partially Compliant: [COUNT]
- Non-Compliant: [COUNT] (Explain any deviations)
- Not Applicable: [COUNT]
EOF
```

## Best Practices for Government Proposal Writing

1. **Requirements-First Approach**: Always trace back to the solicitation requirements
2. **Clear and Concise Writing**: Government evaluators review many proposals - be clear
3. **Proof Points Over Claims**: Provide evidence and metrics, not just statements
4. **Formatting Compliance**: Follow formatting instructions exactly (fonts, margins, page limits)
5. **Mandatory Sections**: Never skip mandatory sections, even if page limits are tight
6. **Past Performance Relevance**: Ensure past performance is directly relevant to the requirement
7. **Small Business Planning**: If required, develop a credible small business subcontracting plan
8. **Cost Realism**: Ensure costs are realistic and supported by documentation
9. **Multiple Reviews**: Conduct pink, red, and gold team reviews
10. **Read Aloud**: Read the proposal aloud to catch awkward phrasing
11. **Fresh Eyes**: Have someone unfamiliar with the project review for clarity
12. **Page Count Management**: Watch page limits closely throughout development
13. **Reference Checking**: Verify all customer references are willing and able to speak
14. **Certification Currency**: Ensure all representations and certifications are current
15. **Submission Readiness**: Prepare for submission well before the deadline

## Verification Steps

To verify this skill is working correctly:

1. Check that configuration files are created in ~/government_contract_bot/config/proposal_writer/
2. Verify the directory structure for proposal development exists
3. Confirm that the proposal writing logic would correctly analyze solicitations
4. Validate that section generation would work with sample requirements
5. Check that integration points with task management and meeting systems are present
6. Test template generation and formatting concepts
7. Verify compliance matrix creation process

## Maintenance

- Review and update formatting rules based on latest government standards
- Update section requirements based on common solicitation types
- Maintain past performance template with current best practices
- Keep compliance matrix templates aligned with evaluation criteria
- Track win/loss reasons to improve future proposals
- Update past performance examples as new work is completed
- Monitor proposal development timelines to improve estimating

## Example Usage

```bash
# Load the proposal writer skill
skill_view(name='proposal-writer-subagent')

# Assist with developing a proposal for a specific opportunity
delegate_task(
    goal='Develop technical approach section for government contract proposal',
    context='Opportunity ID: SAM-12345-2026-001; Solicitation details in ~/government_contract_bot/contracts/pending/SAM-12345-2026-001/; Use templates and configuration in ~/government_contract_bot/config/proposal_writer/'
)

# Or set up automated proposal development reminders
cronjob(
    action='create',
    name='proposal-development-scheduled',
    schedule='0 9 * * 1-5',  # Weekdays at 9 AM
    prompt='Check for and work on pending government contract proposals',
    skills=['proposal-writer-subagent', 'government-contract-management']
)
```

## References

- Federal Acquisition Regulation (FAR) Part 15: Contracting by Negotiation
- OMB Uniform Guidance (2 CFR Part 200)
- Agency-specific procurement guidelines (NASA FAR Supplement, DoD Grant and Agreement Regulations, etc.)
- SBIR/STTR Policy Directive (if applicable)
- GSA Schedule Contracting procedures (if applicable)
- Proposal Writing Guides: APMP, Shipley Associates, Lohfeld Consulting