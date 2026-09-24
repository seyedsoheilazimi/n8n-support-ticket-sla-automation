# n8n Support Ticket & SLA Automation

A practical support-operations automation built with **n8n**, **Google Sheets**, and **Gmail**.

The project automates support-ticket intake, categorization, priority detection, SLA deadline calculation, ticket tracking, overdue monitoring, escalation, and owner notification.

## Project Overview

This project is split into two workflows:

1. **Ticket Intake Workflow**  
   Receives a new support request, validates it, classifies it, assigns an owner, calculates an SLA deadline, generates a ticket ID, and stores the ticket in Google Sheets.

2. **SLA Monitor Workflow**  
   Runs on a schedule, checks open tickets, detects overdue SLA deadlines, updates the ticket status, and emails the responsible owner.

## Workflow 1 — Ticket Intake

```text
Webhook
  ↓
Normalize Request Data
  ↓
Validate Email
  ↓
Category Routing
  ├── Billing
  ├── Technical
  └── General
  ↓
Set Owner + Owner Email
  ↓
Priority Routing
  ├── High
  ├── Medium
  └── Low
  ↓
Set SLA Hours
  ↓
Generate Ticket Metadata
  ↓
Append Ticket to Google Sheets
```

### Categories

The workflow uses keyword-based routing to classify requests.

**Billing**
- payment
- invoice
- refund
- charge
- subscription

**Technical**
- login
- password
- account
- authentication
- error
- bug
- technical

**General**
- question
- information
- help
- general
- feedback

### Priority Rules

**High**
- urgent
- critical
- immediately
- asap
- cannot access
- down
- blocked

**Medium**
- problem
- issue
- slow
- not working
- failed

**Low**
- Used when no High or Medium rule matches.

### SLA Rules

| Priority | SLA |
|---|---:|
| High | 4 hours |
| Medium | 24 hours |
| Low | 48 hours |

The workflow calculates the due date automatically from the current time and the SLA value.

### Generated Ticket Data

Each ticket includes:

- Ticket ID
- Customer Name
- Email
- Subject
- Message
- Category
- Priority
- Owner
- Owner Email
- Status
- Created At
- Due At
- Escalated

Example ticket ID:

```text
TCK-20260922184530
```

Initial ticket state:

```text
Status: Open
Escalated: No
```

## Workflow 2 — SLA Monitor

```text
Schedule Trigger
  ↓
Get Tickets from Google Sheets
  ↓
Filter:
Status = Open
AND
Escalated = No
  ↓
Check:
Due At < Current Time
  ↓
Update Ticket
  ↓
Status = Escalated
Escalated = Yes
  ↓
Send Gmail Alert to Owner
```

The second workflow periodically checks the tracker and only processes tickets that are still open and have not already been escalated.

When an SLA deadline is exceeded:

```text
Status: Open
→
Status: Escalated
```

and:

```text
Escalated: No
→
Escalated: Yes
```

The responsible owner then receives an email containing the ticket details.

## Google Sheets Tracker

The project uses Google Sheets as a lightweight support-ticket database.

Recommended columns:

| Ticket ID | Customer Name | Email | Subject | Message | Category | Priority | Owner | Owner Email | Status | Created At | Due At | Escalated |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

## Example Request

```json
{
  "name": "Anita",
  "email": "anita@example.com",
  "subject": "Critical login authentication error",
  "message": "I cannot access my account because the authentication process keeps failing. I am completely blocked and need help immediately."
}
```

Expected routing:

```text
Category: Technical
Priority: High
SLA: 4 hours
Owner: Technical Support
```

## Technologies

- n8n
- Webhooks
- Google Sheets
- Gmail
- JSON
- Conditional routing
- Regex / keyword matching
- Date and time calculations
- Scheduled workflows
- SLA monitoring
- Workflow automation

## Key Concepts Practiced

- Event-driven automation
- Data normalization
- Input validation
- Multi-branch routing
- Dynamic field mapping
- SLA calculation
- Date/time comparison
- Ticket lifecycle management
- Scheduled monitoring
- State synchronization
- Escalation logic
- Automated email notifications

## Tested Scenarios

- ✅ Billing ticket routing
- ✅ Technical ticket routing
- ✅ General ticket routing
- ✅ High priority routing
- ✅ Medium priority routing
- ✅ Low priority routing
- ✅ SLA deadline calculation
- ✅ Ticket creation in Google Sheets
- ✅ Owner email storage
- ✅ Overdue ticket detection
- ✅ Ticket status escalation
- ✅ Gmail notification to ticket owner

## Possible Improvements

Future versions could include:

- AI-based ticket classification
- Duplicate ticket detection
- Reopened ticket handling
- SLA rules based on customer tier
- Automatic ticket resolution emails
- Google Sheets conditional formatting for escalated tickets
- Slack or Microsoft Teams notifications
- Dashboard reporting
- Escalation levels
- Business-hours-aware SLA calculation

## Repository Structure

```text
README.md
sample-requests.json
workflow-ticket-intake.json      # optional exported n8n workflow
workflow-sla-monitor.json        # optional exported n8n workflow
workflow-ticket-intake.png       # optional workflow screenshot
workflow-sla-monitor.png         # optional workflow screenshot
```

> Exported n8n workflow files should be reviewed before publishing to make sure no sensitive values, tokens, credentials, personal emails, or private IDs are included.

## Project Status

✅ Working prototype completed
