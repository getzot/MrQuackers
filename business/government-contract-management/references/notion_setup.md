# Notion Database Setup for Government Contract Management

This guide shows how to set up the Notion databases needed for the government contract management system.

## Prerequisites
1. Notion integration token configured in Hermes (`NOTION_API_KEY`)
2. Basic understanding of Notion databases

## Database 1: Contracts Database

Create a new database in Notion with these properties:

| Property Name | Type | Description |
|---------------|------|-------------|
| Contract ID | Title | Unique identifier for the contract (e.g., OPPORTUNITY-ID or CONTRACT-NUMBER) |
| Opportunity ID | Text | Original SAM.gov opportunity ID |
| Agency | Select | Government agency (GSA, DoD, HHS, etc.) |
| Status | Select | Pending, Active, Awarded, Completed, Lost |
| Posted Date | Date | When the opportunity was posted |
| Response Due | Date | Bid/proposal submission deadline |
| Award Date | Date | When contract was awarded (if applicable) |
| Contract Value | Number | Total contract value in USD |
| Prime Contractor | Text | Your company name |
| Contracting Officer | Text | Government POC name |
| POC Email | Email | Contracting officer email |
| NAICS Code | Text | Primary NAICS code |
| Set Aside | Select | Small business set-aside type (None, SB, SDVOSB, WOSB, HUBZone, 8(a)) |
| Notes | Text | Additional notes and comments |

### Sample Entries
- Contract ID: `SAM-12345-2026-001`
- Opportunity ID: `SAM-12345-2026-001`
- Agency: `GSA`
- Status: `Active`
- Posted Date: `2026-01-15`
- Response Due: `2026-02-15`
- Award Date: `2026-03-01`
- Contract Value: `500000`
- Prime Contractor: `Your Company Inc.`
- Contracting Officer: `John Smith`
- POC Email: `john.smith@gsa.gov`
- NAICS Code: `541511`
- Set Aside: `SB`
- Notes: `IT support services for regional office`

## Database 2: Tasks Database

| Property Name | Type | Description |
|---------------|------|-------------|
| Task Name | Title | Descriptive task name |
| Related Contract | Relation | Links to Contracts database |
| Assigned To | Person | Team member responsible |
| Due Date | Date | When task should be completed |
| Status | Select | Not Started, In Progress, Blocked, Completed |
| Priority | Select | Low, Medium, High, Urgent |
| Task Type | Select | Proposal Development, Compliance, Reporting, Subcontractor Management, Meeting Preparation, etc. |
| Description | Text | Detailed task description and instructions |

### Sample Entries
- Task Name: `Complete technical approach section`
- Related Contract: `SAM-12345-2026-001`
- Assigned To: `Lead Engineer`
- Due Date: `2026-02-10`
- Status: `In Progress`
- Priority: `High`
- Task Type: `Proposal Development`
- Description: `Write detailed technical approach for software development effort, including architecture diagrams and risk mitigation`

## Database 3: Subcontractors Database

| Property Name | Type | Description |
|---------------|------|-------------|
| Subcontractor Name | Title | Name of subcontractor company |
| Related Contract | Relation | Links to Contracts database |
| DUNS Number | Text | DUNS number of subcontractor |
| CAGE Code | Text | CAGE code of subcontractor |
| NAICS Code | Text | Subcontractor's primary NAICS |
| Work Scope | Text | Description of work to be performed |
| Contract Value | Number | Subcontract value in USD |
| Start Date | Date | When subcontractor work begins |
| End Date | Date | When subcontractor work completes |
| Status | Select | Pending, Active, Completed, Terminated |
| Performance Rating | Select | Excellent, Good, Fair, Poor (evaluated periodically) |
| Notes | Text | Performance notes, issues, etc. |

### Sample Entries
- Subcontractor Name: `TechSupport LLC`
- Related Contract: `SAM-12345-2026-001`
- DUNS Number: `123456789`
- CAGE Code: `1ABC2`
- NAICS Code: `541512`
- Work Scope: `Provide help desk tier 1 support`
- Contract Value: `150000`
- Start Date: `2026-03-15`
- End Date: `2027-03-14`
- Status: `Pending`
- Performance Rating: `Not Rated Yet`
- Notes: `SB-certified, will need to verify insurance`

## Using Notion with Hermes

### Creating Database Entries via Hermes

You can use the `ntn` CLI or HTTP API to create entries. Here are examples:

#### Create a Contract Entry
```bash
ntn api v1/pages \
  parent[database_id]=YOUR_CONTRACTS_DATABASE_ID \
  properties[Contract ID][title][0][text][content]="SAM-12345-2026-001" \
  properties[Opportunity ID][rich_text][0][text][content]="SAM-12345-2026-001" \
  properties[Agency][select][name]="GSA" \
  properties[Status][select][name]="Pending" \
  properties[Posted Date][date][start]="2026-01-15" \
  properties[Response Due][date][start]="2026-02-15" \
  properties[Contract Value][number]=500000 \
  properties[Prime Contractor][rich_text][0][text][content]="Your Company Inc." \
  properties[Contracting Officer][rich_text][0][text][content]="John Smith" \
  properties[POC Email][email]="john.smith@gsa.gov" \
  properties[NAICS Code][rich_text][0][text][content]="541511" \
  properties[Set Aside][select][name]="SB"
```

#### Create a Task Entry
```bash
ntn api v1/pages \
  parent[database_id]=YOUR_TASKS_DATABASE_ID \
  properties[Task Name][title][0][text][content]="Complete technical approach section" \
  properties[Related Contract][relation][0][id]="PAGE-ID-OF-CONTRACT" \
  properties[Assigned To][people][0][object]="user" \
  properties[Due Date][date][start]="2026-02-10" \
  properties[Status][select][name]="In Progress" \
  properties[Priority][select][name]="High" \
  properties[Task Type][select][name]="Proposal Development" \
  properties[Description][rich_text][0][text][content]="Write detailed technical approach for software development effort, including architecture diagrams and risk mitigation"
```

## Automation Ideas

### Cron Jobs for Regular Updates
Set up Hermes cron jobs to:
1. **Daily**: Check for overdue tasks and send reminders
2. **Weekly**: Generate contract status reports
3. **Monthly**: Review subcontractor performance
4. **Before Deadlines**: Send proposal preparation reminders

### Webhook Integration
Consider setting up Notion webhooks to:
- Trigger Hermes actions when contract status changes
- Update Hermes memory when tasks are completed
- Notify team members of assignment changes

## Sample Hermes Commands for Notion Interaction

```python
# In a Hermes skill or script:
from hermes_tools import web_search, web_extract

def create_contract_tracking(opportunity_id, title, agency, due_date, naics, set_aside):
    """Create a new contract tracking entry in Notion"""
    # This would use the ntn API or HTTP calls
    pass

def update_contract_status(contract_id, status, award_date=None, value=None):
    """Update contract status in Notion"""
    pass

def create_task(task_name, contract_id, assigned_to, due_date, priority, task_type, description):
    """Create a new task in Notion"""
    pass

def get_upcoming_tasks(days_ahead=7):
    """Get tasks due in the next N days"""
    pass
```

## Best Practices

1. **Consistent Namoring**: Use clear, consistent naming conventions for contracts and tasks
2. **Regular Updates**: Update status fields regularly to keep information current
3. **Use Relations**: Leverage database relations to connect contracts → tasks → subcontractors
4. **Views and Filters**: Create different views in Notion for:
   - My Tasks (filtered by Assigned To)
   - Upcoming Deadlines (sorted by Due Date)
   - Contract Status Kanban (grouped by Status)
   - Subcontractor Performance (sorted by Performance Rating)
5. **Templates**: Create Notion templates for common task types (proposal sections, compliance checks, etc.)

## Troubleshooting

- **Permission Errors**: Ensure the Notion integration has been invited to the databases
- **Connection Issues**: Verify `NOTION_API_KEY` is correctly set in Hermes environment
- **Rate Limits**: Notion API has limits (~3 requests/second average); add delays in automation
- **Property Type Mismatches**: Double-check that property types match when creating/updating entries

With these databases set up, you'll be able to track government contracts through their entire lifecycle, manage tasks and deadlines, and monitor subcontractor performance—all integrated with your Hermes agent for automated reminders and updates.