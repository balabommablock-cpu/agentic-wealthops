# Chapter 5: Client Lifecycle Automation

> KYC, onboarding, account maintenance — the first touchpoint, automated

## The Client Onboarding Journey

### Step 1: KYC Document Collection
**Current state:** Client submits documents via email, physical, or app upload. Ops team manually checks for completeness.

**Agentic approach:** 
- WhatsApp/app-based collection with real-time feedback
- LLM-powered completeness check ("Your address proof is missing. Please share a utility bill or bank statement.")
- Auto-classification of document types

### Step 2: Document Verification
**Current state:** Manual review of PAN, Aadhaar, bank proof for authenticity and consistency.

**Agentic approach:**
- OCR + LLM extraction of key fields from all documents
- Cross-validation: Name on PAN matches name on Aadhaar matches name on bank statement
- Format validation: PAN format (AAAAA1234A), Aadhaar format, IFSC format
- DigiLocker/CKYC API verification where available
- Confidence scoring: auto-approve >95%, human review <95%

### Step 3: Risk Profiling
**Current state:** Standard questionnaire, manually scored.

**Agentic approach:** 
- Dynamic questionnaire that adapts based on responses
- AI-scored risk profile with explanation
- Auto-mapping to suitable product categories

### Step 4: Account Creation
**Current state:** Manual data entry into back-office, KRA, depository systems, exchange UCC registration.

**Agentic approach:**
- Single data entry with auto-propagation to all systems via APIs
- Error handling agent that retries failed submissions and routes persistent failures to humans

### Step 5: Welcome and Activation
**Current state:** Template welcome email/SMS.

**Agentic approach:**
- Personalized welcome journey based on client profile
- Guided first-trade experience
- Automated check-ins at day 3, 7, 30

## Common KYC Exceptions and How AI Handles Them

| Exception | Frequency | AI Resolution |
|-----------|-----------|--------------|
| Name mismatch across documents | 15% | LLM fuzzy matching with confidence score. Auto-resolve if minor (initials, middle name). Flag if major. |
| Expired document | 5% | Auto-detect expiry date, request updated document with specific instructions |
| Blurry/illegible scan | 10% | Image quality assessment, request re-upload with camera guidance |
| Address mismatch | 8% | Compare current vs permanent address logic, request clarification if needed |
| PAN not linked to Aadhaar | 3% | Flag and guide client through PAN-Aadhaar linking process |
| Minor applying | 2% | Detect age from DOB, auto-route to guardian account workflow |
| NRI/Foreign national | 2% | Detect from document type/address, route to specialized NRI onboarding |

## Metrics to Track

- **Straight-through processing rate:** % of applications requiring zero human intervention
- **Average onboarding time:** From first document submission to account activation  
- **Exception resolution time:** How quickly flagged items are resolved
- **Document re-request rate:** How often clients need to resubmit (lower = better AI guidance)
- **Cost per onboarding:** All-in cost including AI, human time, and system costs

---

**Next:** [Trade Operations](03-trade-ops.md)
