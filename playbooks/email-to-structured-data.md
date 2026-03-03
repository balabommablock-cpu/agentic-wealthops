# Playbook: Email-to-Structured-Data Agent

> Turning the inbox chaos into actionable, structured data

## The Problem

Financial services firms receive hundreds to thousands of emails daily that contain operationally critical information:

- **Exchange circulars** from NSE/BSE/MCX about rule changes, corporate actions, trading holidays
- **AMC communications** about scheme changes, dividend declarations, NFO launches
- **Client requests** for account modifications, fund transfers, portfolio reports
- **Insurer notifications** about policy status, claim updates, commission statements
- **Regulatory updates** from SEBI/IRDAI about new guidelines, filing deadlines
- **Internal reports** from branches, franchisees, relationship managers

Today, human ops staff read each email, figure out what it is, extract the relevant data, and enter it into the appropriate system. This is pure waste.

## Agent Architecture

```
[Email Inbox Monitor]
    |
    v
[Classification Agent]
    - What type of email is this?
    - Which department/system should it go to?
    - What urgency level?
    |
    v
[Extraction Agent]
    - Extract structured data based on email type
    - Handle attachments (PDF, Excel, images)
    - Normalize data formats (dates, amounts, identifiers)
    |
    v
[Validation Agent]
    - Cross-check extracted data against master data
    - Flag inconsistencies
    - Rate confidence
    |
    v
[Action Router]
    - High confidence: Auto-push to target system via API
    - Medium confidence: Queue for quick human review
    - Low confidence: Route to specialist
    |
    v
[Confirmation and Logging]
    - Log all actions taken
    - Send confirmation to relevant stakeholders
    - Update audit trail
```

## Email Categories and Extraction Templates

### Exchange Circulars
**Extract:** Circular number, date, subject, affected segments, effective date, action required, deadline
**Action:** Update compliance calendar, notify affected teams, flag if action needed before deadline

### Corporate Action Notices
**Extract:** Company name, ISIN, action type (dividend/bonus/split/rights), record date, ex-date, ratio/amount, payment date
**Action:** Update corporate action calendar, adjust client positions, generate notifications

### AMC Scheme Updates
**Extract:** AMC name, scheme name, scheme code, change type (name change/merger/NFO/dividend), effective date, details
**Action:** Update scheme master, notify distributors, adjust reporting

### Client Requests (via email)
**Extract:** Client ID/PAN, request type, specific details, supporting documents attached
**Action:** Create service request ticket, route to appropriate team, auto-acknowledge to client

### Commission Statements
**Extract:** Period, AMC/insurer name, total amount, line-item details (policy/folio wise)
**Action:** Parse into structured format, push to reconciliation engine

## Implementation Details

**Email Access:** IMAP/Gmail API/Microsoft Graph API
**LLM:** Claude Sonnet for complex extraction, Claude Haiku for classification
**Attachment Processing:** PDF extraction via document AI, Excel parsing via pandas/openpyxl
**Output:** JSON structured data pushed to target systems via REST APIs
**State Management:** PostgreSQL for tracking processed emails and actions taken

## Metrics

| Metric | Target |
|--------|--------|
| Classification accuracy | >95% |
| Data extraction accuracy | >90% |
| Processing time per email | <30 seconds |
| Auto-resolution rate | >70% |
| Human review time (for flagged items) | <2 minutes per item |

---

**Back to:** [README](../README.md)
