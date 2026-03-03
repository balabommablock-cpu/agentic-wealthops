# Chapter 1: The Operations Landscape

> Where thousands of humans spend their days — mapped process by process, system by system

---

## Read this chapter if...

- You are a **product manager** who has been told to "bring AI to operations" but don't know what operations actually does
- You are a **CTO** evaluating where to apply AI first across your firm
- You are a **founder** trying to understand the market opportunity in BFSI operations automation
- You are an **ops veteran** who wants to see if this playbook actually understands your world

---

## The Numbers That Should Shock You

India's financial services operations ecosystem employs more people doing manual knowledge work than most people realize:

**Stockbroking industry:**
- 1,400+ SEBI-registered stockbrokers
- 15+ crore active demat accounts (and growing 20%+ YoY)
- Each broker with 5L+ clients employs 200-500 people in operations
- Industry-wide: estimated 50,000-80,000 ops staff

**Mutual fund distribution:**
- 1,20,000+ AMFI-registered distributors (ARN holders)
- Rs 65+ lakh crore AUM (as of early 2026)
- Large distributors (NJ, Prudent, IIFL Wealth, bank channels) each employ 100-500+ ops staff
- Industry-wide: estimated 30,000-50,000 ops staff across distributors + AMC back-offices

**Insurance distribution:**
- 35+ lakh insurance agents across life, general, and health
- 600+ corporate agents, 500+ insurance brokers
- Each large corporate agent / broker employs 50-300 ops staff
- TPAs alone employ thousands for claims processing
- Industry-wide: estimated 1,00,000+ ops staff

**Total: conservatively 2-3 lakh people in India doing financial services operations work that is partially or fully automatable with current AI technology.**

---

## The Systems They Touch Every Day

Before we talk about what these people do, you need to understand what they work with. This is the real-world technology landscape of Indian financial services ops:

### Exchange & Market Infrastructure

| System | Owner | What It Does | How Ops Interacts |
|--------|-------|-------------|-------------------|
| NSE NEAT+ / NOW | NSE | Trading terminal + member services | Download trade files, upload UCC, check circulars |
| BSE BOLT+ / BOW | BSE | Trading terminal + member services | Download trade files, settlement files |
| MCX Platform | MCX | Commodity trading | Same as above for commodity segment |
| NSCCL / ICCL | Clearing corps | Clearing & settlement | Download obligation files, manage margins |
| SEBI SCORES | SEBI | Complaint management | Respond to client complaints within SLA |
| SEBI SI Portal | SEBI | Intermediary reporting | File regulatory returns, respond to queries |

### Depository Infrastructure

| System | Owner | What It Does | How Ops Interacts |
|--------|-------|-------------|-------------------|
| CDSL Easiest / e-DIS | CDSL | Demat account operations | Process transfers, pledge/unpledge, corporate actions |
| NSDL Speed-e / IDeAS | NSDL | Demat account operations | Same as CDSL for NSDL accounts |
| CKYC portal (CERSAI) | CERSAI | Centralized KYC | Upload/verify KYC records |
| KRA portals (CVL, NDML, DotEx, CAMS KRA, KFintech KRA) | KRAs | KYC Registration | Register/update client KYC status |
| DigiLocker | MeitY | Document verification | Pull verified documents for KYC |

### Mutual Fund Infrastructure

| System | Owner | What It Does | How Ops Interacts |
|--------|-------|-------------|-------------------|
| BSE StAR MF | BSE | MF transaction platform | Place orders, register SIPs, manage mandates |
| MFU (MF Utilities) | AMFI | MF transaction platform | Alternative to BSE StAR MF for transactions |
| NSE NMF II | NSE | MF transaction platform | Third option for MF transactions |
| CAMS WealthServ / myCAMS | CAMS | RTA services | Download reverse feeds, check folio data, generate CAS |
| KFintech portal | KFintech | RTA services | Same as CAMS for KFintech-serviced AMCs |
| AMFI portal | AMFI | Distributor registration | ARN registration, exam scheduling, compliance |

### Insurance Infrastructure

| System | Owner | What It Does | How Ops Interacts |
|--------|-------|-------------|-------------------|
| Individual insurer portals (9-30+) | Each insurer | Policy management | Login to each portal separately for proposals, policies, claims |
| Bima Sugam (upcoming) | IRDAI | Insurance marketplace | Unified platform for policy comparison, purchase, servicing |
| IRDAI portal | IRDAI | Regulatory filing | Submit returns, manage licenses |
| TPA portals | Each TPA | Health claims | Submit claims, track status, handle queries |

### Back-Office & Internal Systems

| System | Typical Examples | What It Does |
|--------|-----------------|-------------|
| Back-office system (broking) | Advent/NSE, Geojit, Religare, in-house built | Trade processing, ledger management, reporting |
| Wealth management platform | Wealth Elite, IFA-Planet, AssetPlus, in-house | MF portfolio management, CRM, reporting |
| Accounting system | Tally, SAP, custom | Financial accounting, GST, TDS |
| CRM | Salesforce, Zoho, Leadsquared, custom | Client management, lead tracking |
| Communication tools | WhatsApp Business API, MSG91, Mailchimp | Client notifications, marketing |
| Document management | SharePoint, Google Drive, custom | Store KYC docs, compliance records |

**The key insight: an ops person at a typical firm logs into 5-12 different systems every day, manually moving data between them. This is the core inefficiency that agentic AI solves.**

---

## The Five Universal Patterns

Across broking, MF distribution, and insurance distribution, we have identified five patterns that account for 95%+ of all manual operations work. Every use case in this playbook maps to one or more of these patterns.

### Pattern 1: Data Transcription (35% of ops time)

**What it is:** A human reads information from Source A and types it into System B.

**Why it exists:** Systems don't talk to each other. Or they do, but the data formats don't match. Or there's a manual verification step in between.

**Real examples across verticals:**

| Vertical | Source A | System B | What Gets Transcribed |
|----------|----------|----------|----------------------|
| Broking | Scanned PAN card (image) | Back-office system (form fields) | Name, PAN number, DOB, father's name |
| Broking | Exchange trade file (CSV) | Reconciliation spreadsheet (Excel) | Trade ID, client code, quantity, price, settlement number |
| Broking | SEBI circular email (text) | Compliance calendar (CRM/tracker) | Circular number, effective date, action required, deadline |
| MF Dist | Client request email ("switch from Fund A to Fund B") | BSE StAR MF portal (form) | Folio number, source scheme, target scheme, amount/units |
| MF Dist | AMC commission PDF (table) | Accounting system (ledger entry) | Period, scheme, AUM, trail rate, trail amount |
| Insurance | Client's medical report (PDF) | Insurer proposal portal (form) | Height, weight, BP, blood sugar, pre-existing conditions |
| Insurance | Policy document from insurer (PDF) | Internal CRM (database) | Policy number, sum assured, premium, start date, nominee |

**What AI agents do here:**
1. **Document AI / Vision LLM** reads Source A (regardless of format — image, PDF, email, Excel)
2. **Extraction agent** pulls out structured fields using domain-trained prompts
3. **Validation agent** checks extracted data against rules (PAN format, date validity, amount ranges)
4. **Integration agent** pushes validated data into System B via API
5. **Exception handler** flags low-confidence extractions for human review

**AI readiness: 90-95%.** This is the most mature pattern. Modern LLMs with vision capability can read almost anything a human can read, and often more accurately.

**The 5-10% that stays manual:** Severely damaged documents, handwritten text in regional languages, novel document formats never seen before. Even these are decreasing as models improve.

### Pattern 2: Matching and Reconciliation (25% of ops time)

**What it is:** A human compares data from System A against data from System B, identifies mismatches, determines root cause, and applies corrections.

**Why it exists:** Multiple systems of record exist for the same underlying reality. They should agree but often don't, due to timing differences, format differences, errors, or legitimate business reasons.

**Real examples:**

| Vertical | System A | System B | What Gets Matched | Common Mismatch Reasons |
|----------|----------|----------|-------------------|------------------------|
| Broking | NSE trade file | Back-office trade ledger | Every trade: client, scrip, qty, price | Client code errors, partial fills, late trades, auction trades, corporate action adjustments |
| Broking | Bank statement | Client ledger | Every credit and debit | Timing (T+1 vs T+2), cheque clearing delays, rejected transfers, bank charges |
| Broking | CDSL/NSDL holding | Back-office holding | Per-client per-scrip holding | Pending deliveries, pledge status, corporate actions not processed |
| MF Dist | Internal AUM tracker | CAMS/KFintech reverse feed | Per-folio per-scheme holding + NAV | NAV date mismatch, switched units not reflected, STP/SWP pending |
| MF Dist | Expected trail commission (AUM x rate) | AMC commission statement | Per-scheme trail amount | Rate changes, AUM computation date differences, clawbacks, new categories |
| Insurance | Premium collected (bank/payment gateway) | Insurer acknowledgment | Per-policy premium status | Payment processing delays, mode change (annual to monthly), partial payments |
| Insurance | Expected commission (premium x rate) | Insurer commission statement | Per-policy commission | Persistency-based rate changes, clawbacks for lapsed policies, group policy adjustments |

**What AI agents do here:**
1. **Data collection agent** pulls data from both sources (API, file download, email attachment)
2. **Normalization agent** standardizes formats (dates, identifiers, amounts) to enable comparison
3. **Matching engine** performs exact and fuzzy matching on key fields
4. **Exception analysis agent (LLM)** categorizes each mismatch, determines root cause, suggests resolution
5. **Auto-resolution engine** applies corrections for known, high-confidence exception types
6. **Human queue** shows remaining exceptions with AI's analysis pre-filled

**AI readiness: 85-95%.** The matching itself is straightforward. The value of AI is in exception analysis — understanding WHY two records don't match and what to do about it.

**The hard part:** Some reconciliation exceptions require judgment calls ("Is this a genuine discrepancy or a timing issue that will self-resolve tomorrow?"). AI can learn common patterns but new exception types will need human classification first.

### Pattern 3: Form Filling and Portal Submission (15% of ops time)

**What it is:** A human takes information from an internal system, logs into an external portal (exchange, regulator, depository, insurer, RTA), and enters the information into forms on that portal.

**Why it exists:** External systems (especially regulators) don't always have APIs. Even when APIs exist, firms may not have integrated them. Portal UIs change frequently.

**Real examples:**

| Vertical | Internal Source | External Portal | What Gets Filled |
|----------|---------------|-----------------|-----------------|
| Broking | Client master data | SEBI intermediary portal | Quarterly regulatory returns (20+ fields per return) |
| Broking | Trade data + client data | Exchange UCC portal | Client code registration and modifications |
| Broking | Client KYC data | KRA portal (CVL, NDML, etc.) | KYC registration and updates |
| MF Dist | Client details + investment choice | BSE StAR MF | SIP registration form (scheme, amount, dates, mandate) |
| MF Dist | Distributor details | AMFI portal | ARN renewal application |
| Insurance | Client details + needs analysis | LIC portal | Proposal form (50-80 fields, medical questions) |
| Insurance | Client details + needs analysis | HDFC Life portal | Different proposal form (different fields, different UI) |
| Insurance | Claims data | Insurer claims portal | Claim intimation form + document uploads |

**What AI agents do here:**
1. **Data assembly agent** compiles all needed information from internal systems
2. **Form mapping agent** maps internal field names to portal field names (this is per-portal configuration)
3. **Portal automation** fills forms via API (preferred) or browser automation (fallback)
4. **Validation agent** checks filled data against portal validation rules before submission
5. **Submission + confirmation** submits and captures confirmation/reference number

**AI readiness: 75-90%.** High where APIs exist. Medium where browser automation is needed (portals change UI). The Anthropic Claude computer use capability is a game-changer here — it can interact with portal UIs directly.

**The risk:** Browser automation can break when portals update their UI. API-first is always preferred. For portals without APIs, maintain a monitoring layer that alerts when automation fails.

### Pattern 4: Communication and Follow-up (15% of ops time)

**What it is:** A human sends messages to clients, partners, or regulators — often triggered by a business event, following a template but requiring some personalization.

**Real examples:**

| Trigger Event | Recipient | Channel | Message Type |
|--------------|-----------|---------|-------------|
| Margin shortfall detected | Client | SMS + WhatsApp + Email | "Dear [Name], your margin shortfall is Rs [X]. Please deposit by [time] to avoid square-off." |
| SIP payment bounced | Client | WhatsApp + Call | "Your SIP of Rs [X] in [Scheme] bounced on [Date]. Please ensure sufficient balance." |
| Policy renewal due in 30 days | Client | Email + WhatsApp | "Your [Insurer] [Product] policy [Number] is due for renewal on [Date]. Premium: Rs [X]." |
| Commission discrepancy found | AMC/Insurer | Email | "We have identified a shortfall of Rs [X] in trail commission for [period]. Please review." |
| Regulatory filing due in 7 days | Internal team | Email + Slack | "Reminder: SEBI [Return Name] is due on [Date]. Data compilation status: [Status]." |
| Client complaint received | Client | Email | "We acknowledge your complaint [ID]. Our team will resolve within [X] days." |

**What AI agents do here:**
1. **Event detection** — trigger based on system events (margin breach, SIP bounce, date-based)
2. **Context assembly** — pull relevant client/transaction data for personalization
3. **Message generation** — LLM generates personalized message following compliant templates
4. **Multi-channel dispatch** — send via appropriate channel(s) based on client preference and urgency
5. **Response handling** — process replies, route to appropriate next action
6. **Follow-up scheduling** — if no response, schedule reminder in [X] days

**AI readiness: 90-95%.** Communication is one of the easiest patterns to automate. The key challenge is ensuring regulatory compliance in all generated messages (SEBI/IRDAI disclosure requirements).

### Pattern 5: Decision Support and Triage (10% of ops time)

**What it is:** A human reviews information, applies domain expertise and judgment, and makes a decision — approve/reject, escalate/resolve, categorize/route.

**Real examples:**

| Decision | Information Reviewed | Judgment Applied | Possible Outcomes |
|----------|---------------------|-----------------|-------------------|
| KYC approval | Documents, cross-checks, CKYC status | Is this person who they claim to be? Are documents genuine? | Approve / Reject / Request more info |
| Surveillance alert triage | Trading pattern, client history, market context | Is this genuinely suspicious or a false positive? | Genuine (escalate) / False positive (close with notes) |
| Claim document completeness | Claim form, supporting documents | Is everything needed present and valid? | Complete (forward to insurer) / Incomplete (request specific docs) |
| Client complaint severity | Complaint text, client history, trade records | How serious is this? Who should handle it? | Low (auto-resolve) / Medium (ops team) / High (compliance head) |
| Trade exception resolution | Mismatch data, possible causes | What caused this mismatch and how to fix it? | Auto-correct / Manual adjust / Escalate / Hold for next day |

**What AI agents do here:**
1. **Information assembly** — gather all relevant data from multiple systems
2. **Rule-based pre-filtering** — apply deterministic rules to handle obvious cases
3. **LLM-based analysis** — for ambiguous cases, apply reasoning to determine likely outcome
4. **Confidence scoring** — rate how confident the agent is in its assessment
5. **Routing** — high confidence auto-resolve; low confidence route to human with analysis pre-filled

**AI readiness: 75-85%.** This is the hardest pattern because it requires domain judgment. But the key insight is: AI doesn't need to make every decision. It needs to correctly handle the easy 80% and intelligently triage the hard 20% to humans. That alone eliminates most of the human time.

---

## Putting It Together: A Day in the Life (Before vs After AI)

### Before AI: A Tuesday at XYZ Broking Pvt Ltd (100L+ clients)

| Time | Team | What They Do | Pattern |
|------|------|-------------|---------|
| 8:00 AM | Settlement team (8 people) | Download yesterday's trade files from NSE/BSE. Import into back-office. Start reconciliation. | Matching |
| 8:30 AM | KYC team (12 people) | Open queue of 200+ pending KYC applications. Start verifying documents. | Transcription + Decision |
| 9:00 AM | Margin team (5 people) | Check pre-market margin positions. Send margin shortfall alerts. | Communication |
| 9:15 AM | Market opens | Trading team monitors orders. Ops supports with client issues. | — |
| 10:00 AM | Email team (3 people) | Start reading today's exchange circulars, AMC communications, client emails. | Transcription |
| 11:00 AM | Client service (6 people) | Handle client calls/emails: "Where is my dividend?" "Why was I squared off?" | Decision |
| 1:00 PM | Commission team (4 people) | Continue monthly AMC commission reconciliation. | Matching |
| 2:00 PM | DP team (4 people) | Process demat transfer instructions, pledge requests, corporate actions. | Form filling |
| 3:30 PM | Market closes | Rush to process end-of-day obligations. | — |
| 4:00 PM | Contract note team (3 people) | Generate, verify, and dispatch contract notes. | Transcription |
| 5:00 PM | Compliance team (3 people) | Review surveillance alerts, prepare regulatory responses. | Decision |
| 7:00 PM | Settlement team | Final reconciliation check. Sign off. | Matching |

**Total: ~50 ops staff. Average productivity: 60-70%. Rest is waiting, context-switching, and error correction.**

### After AI: The Same Tuesday at XYZ Broking (Same 100L+ clients)

| Time | What Happens | Human Involvement |
|------|-------------|-------------------|
| 7:30 PM (previous day) | Trade Reconciliation Agent downloads files, matches 96% of trades, auto-resolves 80% of exceptions, queues 4% for human review | Zero |
| 8:00 AM | Ops lead reviews 200 flagged exceptions (pre-analyzed with root cause). Approves 180 with one click, investigates 20. | 1 person, 45 minutes |
| 8:00 AM | KYC Agent processes overnight queue of 200 applications. Auto-approves 170, flags 30 with specific reasons. | 2 people review 30 flags, 2 hours |
| 8:30 AM | Margin Monitor Agent sends 340 personalized margin alerts via WhatsApp + email. Auto-generated, compliant. | Zero (1 person monitors) |
| 9:00 AM | Email Parser Agent has already classified 89 emails, extracted data, routed actions. Compliance calendar updated. | 1 person reviews action items |
| Continuous | Client Query Agent handles 80% of queries via WhatsApp/chat. Escalates complex ones to humans with full context. | 2 people handle escalations |
| 3:30 PM | Contract Note Agent generates, validates, and dispatches all contract notes within 30 minutes of market close. | Zero (QA sample check) |
| 5:00 PM | Surveillance Triage Agent has analyzed 450 alerts. Closed 380 as false positives with documented reasoning. Escalated 70 with analysis. | 2 people review 70 genuine alerts |
| Monthly | Commission Reconciliation Agent processes all 28 AMC statements in 12 minutes. Flags 7 discrepancies with evidence. | 1 person reviews discrepancies |

**Total: 8-12 ops staff (down from 50). Average productivity: 90%+. Time spent on judgment and relationships, not copy-paste.**

---

## The Regulatory Reality Check

Before you automate everything, understand the regulatory guardrails:

**SEBI requires:**
- KYC verification must have human accountability (the "designated compliance officer" is ultimately responsible)
- Surveillance responses must be reviewed by a qualified person before submission to the exchange
- Client fund handling requires human authorization above certain thresholds
- Algorithmic/AI-based systems used in trading or operations should be disclosed and auditable

**IRDAI requires:**
- Insurance advice must come from a licensed person (AI can assist, not replace the advisor)
- Claims decisions must have human oversight
- Customer grievances must be handled with human escalation capability
- Data handling must comply with DPDPA and IRDAI cybersecurity guidelines

**AMFI requires:**
- Investment suitability assessment must be documented
- Communications must follow advertising code (no guaranteed returns, no misleading claims)
- Client consent must be explicitly obtained for automated processing

**The bottom line:** AI can do 90% of the work, but a human must be accountable for 100% of the output. Design your systems with this in mind. Every AI decision should be auditable, explainable, and have a human override.

See [Chapter 33-36 (Regulatory section)](../08-regulatory/) for deep dives on each regulator.

---

**Next:** [Why Now: The Agentic AI Moment](02-why-now.md)
