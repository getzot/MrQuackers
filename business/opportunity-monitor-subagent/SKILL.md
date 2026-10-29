---
name: opportunity-monitor-subagent
description: "Monitors SAM.gov for government contract opportunities."
version: 0.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sam.gov, opportunity, monitoring, government-contracts]
    related_skills: [government-contract-management]
---

# Opportunity Monitor Subagent Skill

This skill provides a specialized subagent for continuously monitoring SAM.gov and other government contract platforms for new opportunities that match business criteria.

## When to Use This Skill

Use this skill when you need to:
- Automatically scan SAM.gov for new contract opportunities
- Filter opportunities based on NAICS codes, set-asides, value thresholds, etc.
- Create initial tracking records for new opportunities
- Receive notifications about relevant opportunities
- Integrate with the government contract management workflow

## Directory Structure Assumptions

This skill assumes the following directory structure within your government_contract_bot:
```
government_contract_bot/
├── opportunities/
│   ├── new/              # Recently discovered opportunities
│   ├── processed/        # Opportunities that have been reviewed
│   └── ignored/          # Opportunities that were filtered out
├── config/
│   └── opportunity_monitor/
│       ├── filters.json  # Monitoring criteria
│       └── agencies.txt  # Target agencies
���└── logs/
    └── opportunity_monitor.log
```

## Core Functionality

### 1. Configuration Setup

Create configuration files for the opportunity monitor:

```bash
# Create config directory
mkdir -p ~/government_contract_bot/config/opportunity_monitor

# Create filters configuration
cat > ~/government_contract_bot/config/opportunity_monitor/filters.json << 'EOF'
{
  "naics_codes": ["541511", "541512", "541513", "541519"],
  "set_asides": ["SB", "SDVOSB", "WOSB", "HUBZone", "8(a)"],
  "min_value": 50000,
  "max_value": 5000000,
  "agencies": ["GSA", "DoD", "HHS", "DHS", "VA"],
  "keywords": ["IT support", "software development", "cybersecurity", "cloud services"],
  "exclude_keywords": ["maintenance only", "resupply"]
}
EOF

# Create agencies list
cat > ~/government_contract_bot/config/opportunity_monitor/agencies.txt << 'EOF'
GSA
DoD
HHS
DHS
VA
NASA
DOE
USDA
EOF
```

### 2. Opportunity Monitoring Workflow

The subagent performs these steps:

#### Step 1: Scan SAM.gov for New Opportunities
```bash
# This would typically involve:
# 1. Querying SAM.gov API or scraping the website
# 2. Filtering results based on configuration
# 3. Creating tracking records for new opportunities

# Example pseudocode:
opportunities = scan_samgov(
    naics_codes=config.naics_codes,
    set_asides=config.set_asides,
    min_value=config.min_value,
    max_value=config.max_value,
    agencies=config.agencies,
    keywords=config.keywords,
    exclude_keywords=config.exclude_keywords
)

for opp in opportunities:
    if not already_tracked(opp.id):
        create_opportunity_record(opp)
        send_notification(opp)
```

#### Step 2: Create Opportunity Tracking Records
```bash
# When a new opportunity is found, create a tracking directory
mkdir -p ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}

# Save opportunity details
echo "Opportunity ID: {OPPORTUNITY_ID}" > ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Title: {TITLE}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Agency: {AGENCY}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Posted Date: {DATE}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Response Due: {DUE_DATE}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "NAICS Code: {NAICS}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Set Aside: {SET_ASIDE}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Value: {VALUE}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Description: {DESCRIPTION}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
echo "Link: {SAM_GOV_LINK}" >> ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID}/opportunity.txt
```

#### Step 3: Notify Relevant Team Members
```bash
# Send notifications via configured channels (email, Slack, Telegram, etc.)
# Could integrate with Hermes messaging capabilities
```

### 3. Integration with Government Contract Management

When opportunities are reviewed and accepted:
```bash
# Move from new to active tracking
mv ~/government_contract_bot/opportunities/new/{OPPORTUNITY_ID} \
   ~/government_contract_bot/opportunities/pending/{OPPORTUNITY_ID}

# Create full contract tracking directory
mkdir -p ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}

# Copy opportunity details
cp ~/government_contract_bot/opportunities/pending/{OPPORTUNITY_ID}/opportunity.txt \
   ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/opportunity.txt

# Create proposal using template
cp ~/government_contract_bot/contracts/templates/government_proposal_template.md \
   ~/government_contract_bot/contracts/pending/{OPPORTUNITY_ID}/proposal.md

# Create Notion database entry (if Notion configured)
# ntn api v1/pages ... (would be called here)
```

## Automation with Hermes

### Cron Job for Regular Monitoring
Set up a Hermes cron job to run the opportunity monitor regularly:

```bash
# This would be set up via the cronjob tool
cronjob(
    action='create',
    name='opportunity-monitor',
    schedule='every 2h',  # Check every 2 hours
    prompt='Run opportunity monitoring scan for new government contracts',
    skills=['opportunity-monitor-subagent', 'government-contract-management']
)
```

### One-Time Monitoring Run
```bash
# Run the monitor immediately using delegate_task
delegate_task(
    goal='Scan SAM.gov for new IT contract opportunities and create tracking records',
    context='Monitoring configuration is in ~/government_contract_bot/config/opportunity_monitor/; opportunities should be stored in ~/government_contract_bot/opportunities/new/'
)
```

## Technical Implementation Notes

### Data Sources
The opportunity monitor can use:
1. **SAM.gov API**: https://api.sam.gov (requires API key)
2. **SAM.gov Website Scraping**: For when API access is limited
3. **Other Platforms**: Grants.gov, FedBizOpps (historical), agency-specific portals

### Example API Usage (SAM.gov)
```bash
# SAM.gov Opportunities API endpoint
# https://api.sam.gov/prod/opportunities/v2/search
# Requires API key parameter

# Sample request:
# curl "https://api.sam.gov/prod/opportunities/v2/search?api_key=YOUR_KEY&q=IT%20support&agency=GSA&fromPostedDate=01/01/2026&toPostedDate=01/31/2026"
```

### Error Handling and Robustness
- Handle API rate limits and timeouts
- Retry failed requests with exponential backoff
- Log all activities to ~/government_contract_bot/logs/opportunity_monitor.log
- Avoid duplicate processing of the same opportunity
- Handle changes to opportunity details (updates, cancellations)

## Best Practices for Opportunity Monitoring

1. **Regular Scanning**: Check for new opportunities frequently (every 1-4 hours during business hours)
2. **Precise Filtering**: Use specific NAICS codes and keywords to reduce noise
3. **Value Assessment**: Consider both minimum and maximum contract values relevant to your business
4. **Agency Focus**: Target agencies that historically contract for your services
5. **Keyword Evolution**: Update search keywords based on industry trends and past success
6. **Review Process**: Establish a regular review process for new opportunities (daily or twice-daily)
7. **Documentation**: Keep detailed logs of why opportunities were pursued or ignored
8. **Competitive Analysis**: When possible, research incumbents and past award values

## Verification Steps

To verify this skill is working correctly:

1. Check that configuration files are created in ~/government_contract_bot/config/opportunity_monitor/
2. Verify the directory structure for opportunities tracking exists
3. Confirm that the monitoring logic would correctly filter opportunities
4. Validate that opportunity tracking records are created with all required fields
5. Check that integration points with government contract management are present
6. Test notification mechanisms (if configured)

## Maintenance

- Review and update search filters quarterly
- Update agency lists based on contracting trends
- Adjust value thresholds as business capabilities grow
- Monitor success rates and adjust criteria accordingly
- Keep track of which sources yield the best opportunities
- Archive old opportunities according to retention policies

## Example Usage

```bash
# Load the opportunity monitor skill
skill_view(name='opportunity-monitor-subagent')

# Run a monitoring scan (would be implemented as a delegated task)
delegate_task(
    goal='Monitor SAM.gov for new IT services opportunities',
    context='Use filters in ~/government_contract_bot/config/opportunity_monitor/ to find relevant opportunities'
)

# Or set up automated monitoring
cronjob(
    action='create',
    name='opportunity-monitor-scheduled',
    schedule='every 2h',
    prompt='Run opportunity monitoring scan',
    skills=['opportunity-monitor-subagent']
)
```

## References

- SAM.gov API Documentation: https://api.sam.gov/
- SAM.gov Help Center: https://help.sam.gov/
- NAICS Code Search: https://www.census.gov/naics/
- SBA Size Standards: https://www.sba.gov/size-standards