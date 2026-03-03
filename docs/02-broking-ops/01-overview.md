# Chapter 4: Broking Operations — The Complete Taxonomy

> Every operation that keeps a stockbroking firm running, mapped and classified

## The Broking Ops Stack

A stockbroking firm's operations can be organized into six functional layers:

```
Layer 6: Franchisee / AP Management
Layer 5: Risk, Compliance & Regulatory
Layer 4: Finance & Accounting
Layer 3: Trade Operations & Settlement
Layer 2: Client Lifecycle Management
Layer 1: Infrastructure & Data (exchange connectivity, back-office systems)
```

## Layer 2: Client Lifecycle Management

### 2.1 Account Opening / Client Onboarding
**Current process:**
1. Client fills physical or digital form with personal details
2. KYC documents collected (PAN, Aadhaar, bank proof, address proof, income proof)
3. In-person verification (IPV) or video KYC
4. Data entered into back-office system + KRA (KYC Registration Agency)
5. CKYC record created/updated via CERSAI
6. Demat account opened via CDSL/NSDL
7. Trading account activated on exchange segments (NSE/BSE cash, F&O, commodity, currency)
8. Welcome kit / communication sent

**Where humans spend time:**
- Verifying document authenticity and consistency (PAN name matches bank statement matches Aadhaar)
- Handling exceptions (name mismatch, expired documents, non-standard document formats)
- Data entry across multiple systems (back-office, KRA, depository, exchange UCC)
- Chasing clients for missing or unclear documents

**Agentic AI opportunity:**
- Document AI extracts and validates all KYC fields automatically
- LLM cross-checks consistency across documents (name, address, DOB matching)
- Auto-submission to KRA/CKYC via APIs
- Exception handling agent that intelligently requests specific corrections from clients
- Human reviews only flagged exceptions (est. 5-10% of total volume)

### 2.2 Client Data Modification
**Current:** Client emails or calls requesting address change, bank update, nominee change, etc. Ops team manually verifies supporting documents, updates across systems.

**AI approach:** Email parsing agent extracts request type and details. Document verification agent validates supporting docs. Auto-update in back-office with human approval for critical changes (bank details, nominee).

### 2.3 Account Closure / Deactivation
**Current:** Manual processing of closure requests, settlement of pending obligations, deactivation across systems.

**AI approach:** Automated pre-closure checks (pending settlements, open positions, fund balances). Auto-generation of closure documentation. Multi-system deactivation with confirmation.

## Layer 3: Trade Operations and Settlement

### 3.1 Order Management and Execution
Largely automated via OMS/EMS already. AI opportunity is in exception handling — failed orders, price discrepancies, margin-related rejections.

### 3.2 Trade Reconciliation
**Current:** Daily reconciliation of trades between the exchange trade file, the clearing corporation settlement file, and the internal back-office system. A team of 5-20 people at a mid-size broker doing this daily.

**Where mismatches occur:**
- Trade ID discrepancies
- Quantity or price differences (partial fills, auction trades)
- Client code modifications
- Late trade reporting
- Corporate action adjustments

**AI approach:** Automated three-way matching. LLM-powered analysis of exceptions to categorize root cause. Auto-resolution for known exception types. Escalation queue for genuine discrepancies.

### 3.3 Settlement and Pay-in/Pay-out
**Current:** Daily fund obligation calculations, bank transfer initiation for pay-in, verification of pay-out credits.

**AI approach:** Automated obligation calculation. Smart batching of fund transfers. Reconciliation of actual vs expected settlements. Exception alerting.

### 3.4 Running Account Settlement
**Current:** Quarterly/monthly calculation as per SEBI norms. Computation of net balances, generation of statements, fund transfer to clients.

**AI approach:** Fully automated — compute, validate, generate statements, initiate transfers, send notifications.

## Layer 4: Finance and Accounting

### 4.1 Client Ledger Management
Maintaining accurate ledger for every client — credits, debits, margins, settlements.

### 4.2 Contract Note Generation
Daily generation of contract notes for all executed trades. Largely template-driven but quality checks are manual.

### 4.3 DP Operations
Demat account operations — transfers, pledging/unpledging, corporate action processing, DIS (Delivery Instruction Slips) processing.

### 4.4 GST and Tax Compliance
TDS on securities transactions, GST on brokerage, quarterly and annual tax filings.

## Layer 5: Risk, Compliance and Regulatory

### 5.1 Margin Monitoring
Real-time tracking of client margins vs exposure. Auto-square-off triggers. Margin shortfall notifications.

### 5.2 Surveillance
Monitoring for unusual trading patterns, front-running, circular trading, price manipulation. Exchange sends alerts; ops team must investigate and respond.

### 5.3 Regulatory Returns
Multiple returns filed to SEBI, exchanges, depositories — monthly, quarterly, annually. Each requires data compilation from multiple sources.

### 5.4 Internal and External Audit Support
Preparing samples, documentation, and responses for SEBI inspections, exchange audits, and internal audits.

## Layer 6: Franchisee / AP Management

### 6.1 AP Onboarding and Compliance
Authorized Person registration, compliance verification, ongoing monitoring.

### 6.2 Revenue Sharing and Brokerage Computation
Complex multi-tier commission structures, monthly calculations, reconciliation with APs.

### 6.3 AP Oversight and Reporting
Monitoring AP activities for compliance, generating performance reports, handling AP grievances.

---

**Next:** [Client Lifecycle Automation](02-client-lifecycle.md)
