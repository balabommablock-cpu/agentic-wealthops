# Chapter 2: Why Now — The Agentic AI Moment

> From RPA to agents: what changed, and why wealth management ops is the perfect battleground

## The Evolution of Automation in BFSI

### Wave 1: Digitization (2000-2010)
Paper to digital. Physical forms became online forms. Manual ledgers became Excel. The work didn't go away — it just moved to screens.

### Wave 2: Process Automation / RPA (2010-2018)
Tools like UiPath and Automation Anywhere automated the "clicking" — moving data between systems via screen scraping and UI automation. Worked well for structured, predictable workflows. Failed when formats changed, exceptions arose, or judgment was needed.

**Why RPA hit a ceiling in BFSI:**
- Exchange portals change UI frequently, breaking bots
- KYC documents come in dozens of formats
- Regulatory requirements change quarterly
- Exception handling still needed humans

### Wave 3: Traditional AI/ML (2018-2022)
OCR for document reading. NLP for chatbots. ML for fraud detection. Each solved a narrow problem well, but couldn't chain together into end-to-end workflows.

### Wave 4: Agentic AI (2023-Present)
Large Language Models that can understand context, use tools, make decisions, and execute multi-step workflows. The missing piece: AI that can reason about what to do next.

## What Makes Agentic AI Different

| Capability | RPA | Traditional ML | Agentic AI |
|-----------|-----|---------------|------------|
| Handle structured data | Yes | Yes | Yes |
| Handle unstructured data | No | Partially | Yes |
| Adapt to format changes | No | No | Yes |
| Multi-step reasoning | No | No | Yes |
| Exception handling | No | Limited | Yes |
| Natural language I/O | No | Limited | Yes |
| Tool use (APIs, browsers) | Screen scraping | No | Native |
| Learn from feedback | No | Retraining needed | In-context |
| Cost at scale | Medium | High (training) | Low (API calls) |

## The Key Enablers (2024-2026)

### 1. Tool Use / Function Calling
LLMs can now call APIs, query databases, send emails, and interact with external systems. This transforms them from "answer generators" to "work executors."

### 2. Model Context Protocol (MCP)
Anthropic's MCP standard allows Claude (and other LLMs) to connect to any data source or tool via a standardized protocol. No custom integration code needed for each tool.

### 3. Multi-Agent Orchestration
Frameworks like CrewAI, LangGraph, and Autogen allow multiple specialized agents to collaborate on complex workflows — one agent extracts data, another validates it, a third submits it, a fourth handles exceptions.

### 4. Vision + Document Understanding
Modern LLMs can directly read images of documents — scanned forms, PDFs, screenshots of portals — eliminating the need for separate OCR pipelines.

### 5. Cost Reduction
Claude Haiku and GPT-4o-mini make it economically viable to process millions of documents. A KYC verification that costs Rs 50 in human labor can be done for Rs 0.50 with AI.

## Why Wealth Management Ops Specifically

Financial services operations have characteristics that make them uniquely suited for agentic AI:

1. **High volume, low variance**: Thousands of similar transactions daily
2. **Clear rules**: Regulatory frameworks define exactly what to check
3. **Structured ecosystems**: Exchange APIs, RTA APIs, insurer APIs exist
4. **High cost of errors**: Compliance penalties motivate investment in accuracy
5. **Measurable ROI**: Easy to calculate cost per transaction before and after
6. **Competitive pressure**: Discount brokers and digital platforms are squeezing margins

## The 90% Hypothesis

We assert that **90% of operational tasks in wealth management can be automated** using current agentic AI technology. The remaining 10% involves:

- Novel regulatory interpretations requiring legal judgment
- Complex client situations requiring empathy and negotiation
- System failures or edge cases that need human improvisation
- Relationship management and trust-building activities

The goal is not to replace humans entirely, but to transform ops teams from "data processors" to "exception handlers and relationship managers."

---

**Next:** [AI Platform Landscape](03-ai-platforms.md)
