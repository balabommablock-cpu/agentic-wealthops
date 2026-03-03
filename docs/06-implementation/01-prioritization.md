# Chapter 23: Prioritization Framework

> How to pick which of the 50+ use cases to automate first

## The Two-Axis Framework

Every automation use case should be evaluated on two dimensions:

**X-axis: Effort to implement**
- Technology complexity (API availability, data quality, edge cases)
- Regulatory sensitivity (does this need regulator approval?)
- Integration complexity (how many systems involved?)
- Change management (how resistant will the team be?)

**Y-axis: Impact**
- Volume (how many transactions/day?)
- Time saved per transaction
- Error reduction value
- Compliance risk reduction
- Revenue/margin impact

## The Quadrant

```
                    HIGH IMPACT
                        |
    Quick Wins          |         Strategic Bets
    (Do first)          |         (Plan carefully)
                        |
  LOW EFFORT -----------+----------- HIGH EFFORT
                        |
    Fill-ins            |         Avoid (for now)
    (Do when capacity   |         (Revisit when tech matures)
     allows)            |
                        |
                    LOW IMPACT
```

## Recommended Sequencing

### Phase 1: Quick Wins (Month 1-3)
These have high impact and low implementation effort. Start here to build momentum and prove ROI.

1. **Email parsing for exchange circulars** — High volume, structured format, no regulatory risk
2. **Contract note generation QA** — Template-driven, easy to validate
3. **Client communication automation** — Margin calls, SIP reminders, renewal alerts
4. **Commission statement parsing** — Structured PDFs, clear rules
5. **Failed SIP tracking and recovery** — Event-driven, measurable impact
6. **Scheme master data updates** — Low risk, high time savings
7. **MIS report generation** — Data aggregation and formatting

### Phase 2: Core Automation (Month 3-6)
Higher complexity but transformative impact.

8. **KYC document verification** — Needs good prompt engineering but massive volume
9. **Trade reconciliation** — Complex but well-defined rules
10. **Commission reconciliation** — Multi-format parsing, matching logic
11. **Client onboarding end-to-end** — Multiple systems, but clear workflow
12. **Regulatory return preparation** — Data compilation automation

### Phase 3: Advanced Automation (Month 6-12)
Requires more sophisticated agent architecture.

13. **Surveillance alert triage** — Needs judgment, but reduces false positives
14. **Claims processing orchestration** — Multi-party, document-heavy
15. **Portfolio review and rebalancing** — Needs financial reasoning
16. **Cross-sell/upsell identification** — Needs client understanding
17. **Underwriting data preparation** — Complex, multi-source

### Phase 4: Full Transformation (Month 12+)
Requires organizational change, not just technology.

18. **End-to-end trade lifecycle automation** — Needs full system integration
19. **AI-powered compliance monitoring** — Needs regulatory buy-in
20. **Predictive operations** — Anticipating issues before they occur

## ROI Calculation Template

For each use case, calculate:

```
Monthly Cost Today = (People involved) x (Hours/month) x (Hourly cost)
Monthly Cost After = (AI cost) + (Reduced people) x (Hours/month) x (Hourly cost)
Monthly Savings = Cost Today - Cost After
Implementation Cost = (Dev effort) + (Integration) + (Testing) + (Change management)
Payback Period = Implementation Cost / Monthly Savings
```

**Rule of thumb:** If payback period < 6 months, it is a Quick Win. If 6-18 months, it is a Strategic Bet. If > 18 months, reconsider.

---

**Next:** [Build vs Buy vs Orchestrate](02-build-buy-orchestrate.md)
