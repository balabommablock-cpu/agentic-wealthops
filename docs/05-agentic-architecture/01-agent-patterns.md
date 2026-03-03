# Chapter 19: Agent Design Patterns for Wealth Management

> How to architect AI agents that can actually do financial operations work

## Pattern 1: Single-Purpose Agent

The simplest pattern. One agent, one task, one LLM call (or a short chain).

**Use cases:**
- KYC document data extraction
- Email classification (which department should handle this?)
- Contract note generation from trade data
- Client communication message generation

**Architecture:**
```
Input --> [LLM with system prompt + tools] --> Output
```

**Example: Email Classification Agent**
```
System prompt: You are an email classifier for a stockbroking firm.
Classify each email into one of these categories:
- CLIENT_QUERY (client asking about their account/trades)
- EXCHANGE_CIRCULAR (regulatory update from NSE/BSE/SEBI)
- CORPORATE_ACTION (dividend, bonus, split announcements)
- COMPLAINT (client grievance)
- INTERNAL (internal team communication)
- SPAM (irrelevant)

Tools: None needed
Output: JSON with category, confidence, suggested_action
```

## Pattern 2: Sequential Pipeline Agent

Multiple agents in sequence, each handling one step of a workflow.

**Use cases:**
- KYC verification (extract -> validate -> cross-check -> decide)
- Trade reconciliation (download -> match -> analyze exceptions -> resolve)
- Commission reconciliation (download -> parse -> match -> flag -> report)

**Architecture:**
```
Input --> [Agent 1: Extract] --> [Agent 2: Validate] --> [Agent 3: Decide] --> Output
              |                       |                       |
              v                       v                       v
         Structured Data         Validation Results      Decision + Confidence
```

## Pattern 3: Supervisor-Worker Pattern

A supervisor agent that understands the overall task, delegates to specialized worker agents, and assembles the final result.

**Use cases:**
- Client onboarding (supervisor coordinates KYC, risk profiling, account creation, mandate setup)
- Regulatory filing (supervisor coordinates data collection from multiple sources, form filling, validation, submission)
- Claims processing (supervisor coordinates document collection, verification, form filling, insurer submission)

**Architecture:**
```
                    [Supervisor Agent]
                   /    |    |                      v     v    v     v
            [KYC    [Risk   [Account  [Mandate
             Agent]  Agent]  Agent]    Agent]
```

**Key design decisions:**
- Supervisor should have full context but not do detailed work
- Workers should be stateless and focused
- Communication via structured messages (not free text)
- Supervisor handles error recovery and human escalation

## Pattern 4: Human-in-the-Loop (HITL) Agent

Agent does the work, but critical decisions require human approval before proceeding.

**Use cases:**
- Fund transfers above a threshold
- KYC approval for high-risk clients
- Regulatory submission sign-off
- Claim amount approval

**Architecture:**
```
[Agent does work] --> [Confidence check]
                          |
                    High confidence --> Auto-approve --> Execute
                    Low confidence  --> Queue for human --> Human reviews --> Execute/Reject
```

**Implementation detail:** The approval queue should show the agent's reasoning, the supporting data, and a one-click approve/reject interface. Human time should be spent on judgment, not re-doing the agent's work.

## Pattern 5: Event-Driven Reactive Agent

Agent that monitors for events and takes action when triggered.

**Use cases:**
- Margin shortfall detection and notification
- SIP bounce detection and recovery
- Renewal approaching alert and outreach
- Regulatory deadline monitoring

**Architecture:**
```
[Event Stream] --> [Event Filter] --> [Agent evaluates] --> [Action]
                                           |
                                     No action needed --> Log and continue
```

## Pattern 6: Multi-Agent Collaboration

Multiple agents with different specializations working together on complex tasks, potentially debating or reviewing each other's work.

**Use cases:**
- Complex compliance assessment (legal agent + operational agent + risk agent)
- Portfolio review and recommendation (analysis agent + compliance agent + communication agent)
- Dispute resolution (fact-finding agent + negotiation agent + documentation agent)

**Caution:** Multi-agent systems add complexity. Use only when single-agent or pipeline approaches are insufficient. Always start simple.

## Choosing the Right Pattern

```
Simple extraction or classification? --> Pattern 1 (Single-Purpose)
Multi-step linear workflow?          --> Pattern 2 (Sequential Pipeline)
Complex workflow with parallel tasks? --> Pattern 3 (Supervisor-Worker)
High-stakes decisions?               --> Pattern 4 (HITL)
Monitoring and reactive?             --> Pattern 5 (Event-Driven)
Requires multiple perspectives?      --> Pattern 6 (Multi-Agent)
```

## State Management

Financial operations agents MUST maintain state. Every action must be logged, every decision must be traceable, every data access must be recorded.

**State requirements:**
- Transaction ID for every operation
- Complete audit trail of agent decisions and reasoning
- Idempotent operations (safe to retry)
- Rollback capability for failed multi-step workflows
- Compliance-grade logging (who, what, when, why)

---

**Next:** [Tool and MCP Integration](02-tool-mcp-integration.md)
