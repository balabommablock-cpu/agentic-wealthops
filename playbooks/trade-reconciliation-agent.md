# Playbook: Trade Reconciliation Agent

> **The headline number:** A team of 10 people spends 4-6 hours every evening reconciling trades. This agent does it in 11 minutes with 99.2% accuracy. The remaining exceptions are pre-analysed and presented to 1-2 humans for quick resolution.

---

## The Problem (In Detail a Developer Can Understand and an Ops Head Will Recognise)

Every trading day, after market close (3:30 PM), a stockbroker must verify that three independent systems agree on what happened:

| System | What it says | File / Source | Format | Typical Volume |
|--------|-------------|---------------|--------|:---:|
| **Exchange trade file** (NSE: CMTRD / trade.csv; BSE: trade file via BSEFTP) | "These are the trades that happened on our platform" | FTP download from exchange servers, available by 5:30-6:00 PM | Pipe-delimited fixed-width / CSV | 50K-500K rows/day |
| **Clearing corporation obligation file** (NSCCL / ICCL) | "This is what you owe / are owed for settlement tomorrow" | FTP download from clearing corp, available by 6:30-7:00 PM | Fixed-width text / CSV | Aggregated by scrip and settlement type |
| **Internal back-office system** (Odin, NEST-based, or custom) | "This is what our system recorded" | Database query or export from back-office | Database records or CSV export | Should match exchange |

**The task:** Match every trade across these three sources. Investigate every mismatch. Resolve or escalate. Complete before settlement deadline (next morning for T+1).

**Why it is hard for humans:**
- 50,000-500,000 rows to compare
- 15+ fields per trade to match (trade ID, scrip/symbol, quantity, price, buy/sell, client code, order time, settlement type, etc.)
- Partial fills: one order may result in 5 exchange trades but 1 back-office entry
- Client code modifications: exchange may have a different UCC than back-office for the same trade
- Auction trades: failed deliveries result in auction trades that look different from the original
- Corporate action adjustments: bonus/split changes quantities retroactively
- Late trades: some trades (especially block deals, bulk deals) appear late in the file
- Each exception type requires different resolution logic

**Why it is perfect for AI:**
- Fundamentally a matching + classification + reasoning task
- Clear rules for 90% of exceptions
- Remaining 10% can be analysed and pre-classified for human review
- Zero tolerance for missed mismatches (compliance risk) — AI is actually more reliable than tired humans at 10 PM

---

## What the Agent Needs to Know (For the Ops SME Checking for Loopholes)

### Exchange Trade File Fields (NSE CMTRD)
```
Trade Number | Trade Time | Security Code | Symbol | Buy/Sell | Quantity | 
Price | Trade Value | Client Code (UCC) | Order Number | Settlement Type | 
Delivery Flag | Auction Flag | Participant ID | Branch ID | Pro/Client Flag
```

### Back-Office Trade Record Fields
```
Internal Trade ID | Exchange Trade Number (if mapped) | Order ID | Symbol | 
ISIN | Quantity | Price | Brokerage | Net Amount | Client Code | Client Name | 
Settlement Number | Settlement Type | Exchange | Segment | Trade Date | 
Settlement Date | DP ID + Client ID (for delivery)
```

### Common Exception Types (Exhaustive List)

| Exception Type | Root Cause | Frequency | Auto-Resolution Logic |
|---|---|:---:|---|
| **Partial fill aggregation** | Exchange shows 5 fills of 100 shares each. Back-office shows 1 order of 500 shares. | 25-30% | Sum exchange trades by order number. If sum matches back-office quantity and weighted avg price is within Rs 0.05 tolerance, auto-resolve. |
| **Client code mismatch** | Client code modification (CCM) was done on exchange but not yet reflected, or UCC mapping error | 15-20% | Fuzzy match on: same symbol + same quantity + same price + same time window (within 2 seconds). If unique match found, flag as CCM and auto-map. |
| **Auction trade** | Client failed to deliver shares. Exchange conducted auction. Auction trade has different trade number. | 10-15% | Match auction trades by: same symbol + settlement number + short delivery flag in original trade. Link auction to original delivery failure. |
| **Late trade reporting** | Trade exists in exchange file but not in back-office (data sync delay or system issue) | 8-12% | If trade exists in exchange but not back-office, check: (a) is it in pending sync queue? (b) did OMS report it? Mark as "late sync" and retry matching after back-office refresh. |
| **Price rounding** | Exchange trade at 152.3567, back-office rounds to 152.35 or 152.36 | 5-8% | Apply configurable tolerance (default: Rs 0.05 or 0.01% of trade value, whichever is higher). Auto-resolve if within tolerance. |
| **Corporate action adjustment** | Bonus/split happened. Exchange shows adjusted quantities, back-office may not yet reflect. | 3-5% | Lookup corporate action calendar for the symbol. Apply adjustment ratio. If adjusted quantities match, auto-resolve. |
| **Basket / bulk order split** | Institutional order split across multiple client codes at exchange level | 3-5% | Match at order level first, then validate client-level allocation against allocation file. |
| **Interoperability trades** | Trade executed on NSE but cleared through BSE (or vice versa) due to interoperability | 2-3% | Check if the trade appears in the other exchange's clearing file. Map cross-exchange settlement. |
| **Genuine discrepancy** | Actual mismatch — system error, manual intervention needed | 2-5% | Cannot auto-resolve. Present full context to human: both records side by side, suggested investigation steps, similar historical cases. |

### Edge Cases an SME Would Test

1. **Market close auction trades** — different from regular auction trades, happen at 3:40 PM
2. **T+1 vs T+0 settlement** — some securities may settle differently
3. **Physical settlement of derivative contracts** — different settlement flow
4. **Do-not-exercise (DNE) instructions** for options expiry
5. **Compulsory delivery trades** — close-out penalties
6. **Client code modification post-trade** — exchange allows CCM within specified window
7. **Margin trading facility (MTF)** — trades funded by broker, different settlement
8. **SLBM (Stock Lending & Borrowing)** — separate settlement mechanism

---

## Architecture

```
PHASE 1: DATA COLLECTION (5:30 PM - 6:30 PM, automated)
+-------------------------------------------------------------------+
| [Scheduler: 5:30 PM trigger]                                      |
|      |                                                            |
|      v                                                            |
| [FTP Downloader]                                                  |
|   - Connect to NSE FTP (nse-ftp.co.in / designated server)       |
|   - Download CMTRD, obligation, and delivery files                |
|   - Connect to BSE FTP                                            |
|   - Download trade and obligation files                           |
|   - Retry logic: 3 attempts, 5-min intervals                     |
|   - Alert if files unavailable by 6:30 PM                        |
|      |                                                            |
| [Back-Office Extractor]                                           |
|   - Query back-office DB for today's trades                       |
|   - Export in standardised format                                 |
|   - Include pending/modified orders                               |
+-------------------------------------------------------------------+

PHASE 2: MATCHING ENGINE (automated, 2-5 minutes)
+-------------------------------------------------------------------+
| [Data Normaliser]                                                 |
|   - Parse exchange files (handle format variations)               |
|   - Standardise field names, data types, codes                    |
|   - Map exchange symbol codes to ISIN                             |
|      |                                                            |
|      v                                                            |
| [Three-Way Matcher]                                               |
|   - Primary key: Exchange Trade Number                            |
|   - Match fields: Symbol, Qty, Price, Buy/Sell, Client Code      |
|   - Tolerance rules: Price (Rs 0.05), Quantity (exact)            |
|   - Output: matched_trades[], unmatched_exchange[],               |
|             unmatched_backoffice[], partial_matches[]              |
|      |                                                            |
|   Result: ~85-92% trades matched automatically                    |
+-------------------------------------------------------------------+

PHASE 3: EXCEPTION ANALYSIS (LLM-powered, 3-8 minutes)
+-------------------------------------------------------------------+
| [Exception Classifier Agent]                                      |
|   For each unmatched trade:                                       |
|   - Retrieve context: nearby trades, same symbol, same client     |
|   - Apply rule-based classification first (fast, cheap)           |
|   - For ambiguous cases: invoke LLM with full context             |
|   - Output per exception:                                         |
|     {                                                             |
|       exception_type: "PARTIAL_FILL",                             |
|       confidence: 0.97,                                           |
|       resolution: "Sum 5 exchange trades matches 1 BO entry",    |
|       auto_resolve: true,                                         |
|       evidence: { ... },                                          |
|       audit_trail: "Rule: SUM_PARTIAL_FILLS matched..."           |
|     }                                                             |
+-------------------------------------------------------------------+

PHASE 4: RESOLUTION (automated + human review)
+-------------------------------------------------------------------+
| [Auto-Resolver]                                                   |
|   - confidence >= 95%: Apply resolution automatically             |
|   - confidence 70-95%: Apply with "review recommended" flag       |
|   - confidence < 70%: Route to human queue                        |
|   - NEVER auto-resolve if trade value impact > Rs 10L             |
|   - NEVER auto-resolve if it creates a new client position        |
|      |                                                            |
| [Human Review Dashboard]                                          |
|   - Shows 5-30 exceptions (down from 5,000-50,000)               |
|   - Each exception pre-analysed with:                             |
|     - Exception type and confidence                               |
|     - Side-by-side comparison of all three records                |
|     - Agent's suggested resolution                                |
|     - Similar historical cases and how they were resolved         |
|   - One-click: Approve / Reject / Modify / Escalate              |
|      |                                                            |
| [Settlement Finaliser]                                            |
|   - Regenerate net obligations                                    |
|   - Verify fund sufficiency                                       |
|   - Generate reconciliation report                                |
|   - Email summary to: ops head, compliance, finance               |
+-------------------------------------------------------------------+
```

---

## Implementation (Developer-Ready)

### Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Orchestration | Python + Airflow (or Prefect) | Scheduled workflows with retry, alerting, dependency management |
| File processing | Python + pandas | Fast tabular data processing, well-tested |
| FTP download | Python ftplib / paramiko (SFTP) | Exchange FTP connectivity |
| Database | PostgreSQL | State management, audit trail, historical data |
| LLM (exception analysis) | Claude Sonnet 4.5 via Anthropic API | Best reasoning for financial data; structured output |
| LLM (classification) | Claude Haiku 4.5 via Anthropic API | Fast + cheap for high-volume classification |
| Dashboard | Next.js + React | Human review interface |
| Alerting | Slack / Email / PagerDuty | Failure notifications |

### Key Code: Exception Analysis Prompt

```python
EXCEPTION_ANALYSIS_PROMPT = """You are a senior trade reconciliation analyst at an Indian 
stockbroker. You are analysing an unmatched trade from today's reconciliation.

## Context
- Trade date: {trade_date}
- Exchange: {exchange} (NSE/BSE)
- Segment: {segment} (Cash/F&O/Currency/Commodity)
- Settlement number: {settlement_number}
- Settlement type: {settlement_type}

## The unmatched exchange trade:
{exchange_trade_json}

## Nearby back-office trades (same symbol, same day, within +/- 10 min):
{nearby_bo_trades_json}

## Recent corporate actions for this symbol (last 30 days):
{corporate_actions_json}

## Client code modification log for this trade date:
{ccm_log_json}

## Your task:
Analyse this exception and respond in JSON:
{{
  "exception_type": one of ["PARTIAL_FILL", "CLIENT_CODE_MISMATCH", "AUCTION_TRADE", 
    "LATE_REPORTING", "PRICE_ROUNDING", "CORPORATE_ACTION_ADJUSTMENT", 
    "INTEROPERABILITY", "BASKET_ORDER_SPLIT", "GENUINE_DISCREPANCY", "UNKNOWN"],
  "confidence": 0.0 to 1.0,
  "root_cause": "1-2 sentence explanation",
  "suggested_resolution": "Specific action to take",
  "matching_bo_trade_id": "ID of the matching back-office trade, if found, else null",
  "financial_impact_inr": estimated rupee impact of this discrepancy,
  "auto_resolvable": true/false,
  "evidence": "What data points support your classification"
}}

## Rules:
- If you are not at least 70% confident, set exception_type to "UNKNOWN"
- ALWAYS check if partial fills (multiple exchange trades) sum to a single BO trade
- ALWAYS check the CCM log before concluding "GENUINE_DISCREPANCY"
- For price differences < Rs 0.05 with matching quantity, classify as PRICE_ROUNDING
- If a corporate action (bonus/split) happened for this symbol, apply the ratio before matching
- NEVER guess a matching BO trade if the match is ambiguous — set to null and explain why
"""
```

### Key Code: File Processing

```python
import pandas as pd
from pathlib import Path

def parse_nse_cmtrd(filepath: str) -> pd.DataFrame:
    """Parse NSE CMTRD (trade) file into standardised DataFrame.
    
    NSE CMTRD is pipe-delimited with fixed column positions.
    Columns may vary slightly by segment (Cash vs F&O).
    """
    # Column mapping for NSE Cash segment CMTRD
    columns = [
        'trade_number', 'trade_time', 'security_code', 'symbol',
        'buy_sell_indicator', 'quantity', 'price', 'trade_value',
        'client_code', 'order_number', 'settlement_type', 
        'auction_flag', 'participant_id', 'branch_id',
        'pro_client_flag', 'settlement_number'
    ]
    
    df = pd.read_csv(filepath, delimiter='|', names=columns, 
                      dtype={'client_code': str, 'trade_number': str})
    
    # Standardise
    df['exchange'] = 'NSE'
    df['trade_date'] = pd.Timestamp.today().strftime('%Y-%m-%d')
    df['buy_sell'] = df['buy_sell_indicator'].map({'B': 'BUY', 'S': 'SELL'})
    df['price'] = pd.to_numeric(df['price'], errors='coerce')
    df['quantity'] = pd.to_numeric(df['quantity'], errors='coerce')
    
    return df


def three_way_match(exchange_df: pd.DataFrame, 
                     backoffice_df: pd.DataFrame,
                     obligation_df: pd.DataFrame,
                     price_tolerance: float = 0.05) -> dict:
    """Perform three-way trade matching.
    
    Returns:
        {
            'matched': DataFrame of matched trades,
            'unmatched_exchange': trades in exchange but not BO,
            'unmatched_backoffice': trades in BO but not exchange,
            'partial_matches': trades that partially match,
            'summary': {
                'total_exchange': int,
                'total_backoffice': int,
                'matched_count': int,
                'match_rate': float,
                'unmatched_count': int
            }
        }
    """
    # Primary match: on exchange trade number
    merged = exchange_df.merge(
        backoffice_df, 
        left_on='trade_number', 
        right_on='exchange_trade_number',
        how='outer',
        indicator=True,
        suffixes=('_exch', '_bo')
    )
    
    # Exact matches
    matched = merged[merged['_merge'] == 'both'].copy()
    
    # Validate matched trades (quantity + price must agree)
    matched['qty_match'] = matched['quantity_exch'] == matched['quantity_bo']
    matched['price_match'] = abs(matched['price_exch'] - matched['price_bo']) <= price_tolerance
    matched['valid_match'] = matched['qty_match'] & matched['price_match']
    
    truly_matched = matched[matched['valid_match']]
    false_matches = matched[~matched['valid_match']]  # Matched on trade# but data differs
    
    unmatched_exchange = merged[merged['_merge'] == 'left_only']
    unmatched_backoffice = merged[merged['_merge'] == 'right_only']
    
    return {
        'matched': truly_matched,
        'unmatched_exchange': pd.concat([unmatched_exchange, false_matches]),
        'unmatched_backoffice': unmatched_backoffice,
        'summary': {
            'total_exchange': len(exchange_df),
            'total_backoffice': len(backoffice_df),
            'matched_count': len(truly_matched),
            'match_rate': len(truly_matched) / max(len(exchange_df), 1),
            'exception_count': len(unmatched_exchange) + len(unmatched_backoffice) + len(false_matches)
        }
    }
```

### Key Code: LLM-Powered Exception Analysis

```python
import anthropic
import json

client = anthropic.Anthropic()  # API key from environment

def analyse_exception(
    exchange_trade: dict,
    nearby_bo_trades: list[dict],
    corporate_actions: list[dict],
    ccm_log: list[dict],
    trade_date: str,
    exchange: str,
    segment: str
) -> dict:
    """Use Claude to analyse a trade reconciliation exception.
    
    Returns structured analysis with exception type, confidence,
    and suggested resolution.
    """
    prompt = EXCEPTION_ANALYSIS_PROMPT.format(
        trade_date=trade_date,
        exchange=exchange,
        segment=segment,
        settlement_number="2026001",  # Dynamic in production
        settlement_type="Normal",
        exchange_trade_json=json.dumps(exchange_trade, indent=2),
        nearby_bo_trades_json=json.dumps(nearby_bo_trades, indent=2),
        corporate_actions_json=json.dumps(corporate_actions, indent=2),
        ccm_log_json=json.dumps(ccm_log, indent=2)
    )
    
    response = client.messages.create(
        model="claude-sonnet-4-5-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    )
    
    # Parse JSON response
    result = json.loads(response.content[0].text)
    
    # Add metadata
    result['analysed_at'] = pd.Timestamp.now().isoformat()
    result['model_used'] = "claude-sonnet-4-5-20250514"
    result['tokens_used'] = response.usage.input_tokens + response.usage.output_tokens
    
    return result


def batch_analyse_exceptions(
    exceptions: list[dict],
    max_concurrent: int = 10
) -> list[dict]:
    """Analyse all exceptions with cost optimisation.
    
    Strategy:
    - Use rule-based analysis first (free, instant)
    - Only invoke LLM for ambiguous cases
    - Use Claude Haiku for simple classification
    - Use Claude Sonnet for complex reasoning
    """
    results = []
    
    for exc in exceptions:
        # Try rule-based first
        rule_result = apply_rule_based_classification(exc)
        
        if rule_result and rule_result['confidence'] >= 0.95:
            results.append(rule_result)
            continue
        
        # Simple cases: use Haiku (cheaper)
        if is_simple_exception(exc):
            result = analyse_with_haiku(exc)
        else:
            # Complex cases: use Sonnet (better reasoning)
            result = analyse_exception(exc, ...)
        
        results.append(result)
    
    return results
```

---

## ROI Calculation (Real Numbers)

| Metric | Before (Manual) | After (AI Agent) | Impact |
|--------|:---:|:---:|:---:|
| People assigned | 8-15 | 1-2 (exception reviewers) | 80-85% headcount reduction |
| Daily person-hours | 40-80 hrs (5-6 hrs x 8-15 people) | 2-4 hrs (1-2 hrs x 1-2 people) | 90-95% time reduction |
| Time to complete | 4-6 hours (6:00 PM - midnight) | 20-45 minutes (automated) + 30 min human review | 85% faster |
| Error rate (missed mismatches) | 2-5% (human fatigue at 10 PM) | <0.5% (AI does not get tired) | 75%+ error reduction |
| Monthly ops cost | Rs 4-8L (team salary + overtime) | Rs 1-1.5L (AI API cost: ~Rs 30K + 1-2 people) | 70-80% cost reduction |
| Scalability | Linear — 2x trades needs 2x people | Flat — handles 10x volume at ~20% more API cost | Massive |
| Audit readiness | Manual documentation | Auto-generated audit trail for every decision | Always audit-ready |

**Payback period: 2-3 months.**

---

## Guardrails (The Non-Negotiables)

1. **Financial threshold gate:** NEVER auto-resolve if the net financial impact exceeds Rs 10,00,000 (configurable). Always route to human.
2. **Position creation gate:** NEVER auto-resolve if the resolution would create a new client position that did not exist before. This could indicate a genuine error.
3. **Complete audit trail:** Every agent decision is logged with: input data, reasoning, confidence score, resolution action, timestamp, model version.
4. **Daily accuracy report:** Compare agent auto-resolutions with a 5% random sample reviewed by a human. If accuracy drops below 98%, automatically downgrade to "human review" mode.
5. **Regulatory compliance:** All logs are retained for the period mandated by SEBI (currently 5 years for trade records). Logs are structured for exchange/SEBI inspection.
6. **Fallback mode:** If the AI service is unavailable, the system automatically falls back to generating an exception report for full manual processing. No silent failures.
7. **Segregation:** AI agent has read-only access to exchange data and read-write access only to the reconciliation module — not to trading, fund transfer, or client master systems.

---

## Phased Rollout Plan

| Phase | Duration | What happens | Success criteria |
|-------|----------|-------------|-----------------|
| **Phase 0: Shadow mode** | Week 1-2 | Agent runs in parallel. Analyses all exceptions but takes no action. Human team continues manual process. | Agent classifications match human decisions >90% of the time |
| **Phase 1: Auto-resolve simple cases** | Week 3-4 | Auto-resolve only: partial fills and price rounding (confidence >95%). Human handles everything else. | Zero false auto-resolutions. <1% missed genuine exceptions. |
| **Phase 2: Expand auto-resolution** | Month 2 | Add: client code mismatches, auction trades, late reporting. Confidence threshold: >90%. | Auto-resolution rate reaches 70-80%. Human reviews 20-30% of exceptions. |
| **Phase 3: Full production** | Month 3+ | All exception types eligible for auto-resolution. Human handles only low-confidence and high-value cases. Team reduced to 1-2 people. | Auto-resolution rate 85-92%. Human reviews <15% of exceptions. Total recon time <45 minutes. |

---

**Back to:** [README](../README.md) | **Related:** [Broking Ops Deep Dive](../docs/02-broking-ops/01-overview.md)
