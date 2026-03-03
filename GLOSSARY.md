# Glossary

> Every term in this repository explained in plain English. If you are a product manager, business leader, or anyone who does not live in ops/tech jargon every day — this page is your friend. Bookmark it.

---

## Financial Services Terms

### Broking

| Term | What it means in plain English |
|------|-------------------------------|
| **Back-office system** | The software that records every trade, every client, every rupee — the "source of truth" for a broking firm. Think of it as the ERP of a broker. Common ones: Odin (by 63 Moons), NEST (NSE's system), or custom-built. |
| **BOLT+ / NEAT+** | The trading terminals used to place orders on BSE (BOLT+) and NSE (NEAT+). These are what the trader's screen shows. |
| **CDSL / NSDL** | The two depositories in India. They hold shares in electronic (demat) form, like banks hold money. CDSL = Central Depository Services Limited. NSDL = National Securities Depository Limited. |
| **CDSL easi / NSDL SPEED-e** | Online portals for investors/brokers to give demat instructions (transfer shares, pledge them, etc.) without paper forms. |
| **Client code / UCC** | Unique Client Code — every trading client gets a unique ID registered with the exchanges. Like a bank account number, but for trading. |
| **CMTRD file** | The trade file that NSE generates at end of day. Contains every trade executed — symbol, quantity, price, client code. BSE has its own equivalent. This is what the ops team downloads and reconciles against. |
| **Contract note** | The legal document sent to every client after each trading day, detailing what they bought/sold, at what price, and what charges apply. Like a bank statement, but for trades. SEBI requires it to be sent within 24 hours. |
| **DDPI** | Demat Debit and Pledge Instruction — a form that lets the broker debit shares from your demat account for delivery trades. Replaced the old Power of Attorney (PoA) system. |
| **Demat** | Dematerialised — holding shares in electronic form instead of paper certificates. Every investor needs a demat account (like a bank account for shares). |
| **DP operations** | Depository Participant operations — everything related to managing demat accounts: transferring shares, pledging for margin, corporate action processing. |
| **DIS** | Delivery Instruction Slip — a paper form to transfer shares from one demat account to another. Being replaced by digital instructions (DDPI, easi/SPEED-e). |
| **Exchange circular** | Official notification from NSE/BSE about rule changes, trading holidays, corporate actions, new guidelines. Brokers receive dozens daily by email. |
| **F&O** | Futures & Options — derivatives trading. Higher complexity, higher margin requirements than regular (cash) trading. |
| **Franchisee / AP** | Authorised Person — a sub-broker who operates under the main broker's licence. They bring clients, the main broker provides the trading infrastructure. Revenue is shared. Think of it like a franchise model (McDonald's franchisee = AP, McDonald's corporate = broker). |
| **IPV** | In-Person Verification — SEBI requires that someone physically (or via video) verifies a new client's identity before activating their trading account. |
| **KRA** | KYC Registration Agency — centralised agencies (like CVL, CDSL Ventures, KFintech) that store verified KYC records so clients do not have to repeat KYC with every broker. |
| **KYC** | Know Your Customer — the mandatory process of verifying a client's identity (PAN, Aadhaar), address, and financial profile before opening an account. |
| **Margin** | The money/collateral a client must keep with the broker to cover potential trading losses. If the margin falls short, the broker must notify the client and may square off (close) positions. |
| **Obligation** | What a broker owes (or is owed) on settlement day. If your clients bought Rs 10Cr worth of shares, you need to pay in Rs 10Cr to the clearing corporation. |
| **Pay-in / Pay-out** | Pay-in: money/shares flowing from broker to clearing corporation (you are paying for what your clients bought). Pay-out: money/shares flowing back (you are receiving what your clients sold). |
| **Peak margin** | The highest margin required during the trading day. SEBI requires brokers to collect peak margins from clients, not just end-of-day margins. |
| **Reconciliation** | Comparing two sets of records to make sure they match. E.g., "Do our trade records match what the exchange says?" — if they do not, someone needs to investigate. |
| **Running account settlement** | SEBI rule: brokers must return unused client funds quarterly (or monthly, if the client opts). Requires computing net balances for every client and transferring money back. SEBI Circular reference: SEBI/HO/MIRSD/DOP/P/CIR/2022/123. |
| **SCORES** | SEBI Complaints Redress System — the online portal where investors file complaints against brokers. Brokers must respond within specified timelines. |
| **SEBI** | Securities and Exchange Board of India — the regulator for the stock market. They make the rules that brokers must follow. |
| **Settlement cycle** | The timeline for completing a trade. India follows T+1 settlement — a trade executed on Monday is settled (money and shares exchanged) on Tuesday. |
| **Square off** | Forcibly closing a client's open position. Usually happens when the client does not have enough margin. |
| **Surveillance** | Monitoring trading activity for suspicious patterns — insider trading, price manipulation, circular trading, front-running. Exchanges generate alerts; brokers must investigate. |

### Mutual Fund Distribution

| Term | What it means in plain English |
|------|-------------------------------|
| **AMC** | Asset Management Company — the company that creates and manages mutual fund schemes. Examples: HDFC AMC, ICICI Prudential AMC, SBI MF. India has 44+ AMCs. |
| **AMFI** | Association of Mutual Funds in India — the industry body. They manage distributor registration (ARN), best practices, and investor education. |
| **ARN** | AMFI Registration Number — the licence number every mutual fund distributor must have. Without it, you cannot earn commission. Requires passing NISM certification exam. |
| **AUM** | Assets Under Management — the total value of investments managed. For a distributor, "my AUM is Rs 500 Cr" means their clients collectively have Rs 500 Cr invested. |
| **BSE StAR MF** | BSE's platform for mutual fund transactions. Distributors place purchase/redeem/switch orders through this. Has APIs for automation. |
| **CAMS / KFintech** | The two RTAs (Registrar & Transfer Agents) that process all mutual fund transactions in India. CAMS handles ~60% of AMCs, KFintech handles ~40%. They are the back-office of the MF industry. |
| **CAS** | Consolidated Account Statement — a single statement showing all of an investor's MF holdings across all AMCs. Generated by CAMS/KFintech. |
| **Clawback** | When a distributor must return commission because the investor redeemed early. E.g., "If the investor exits within 1 year, the upfront commission is clawed back." |
| **eKYC** | Electronic KYC — verifying identity using Aadhaar OTP instead of physical documents. Much faster than paper KYC. |
| **IFA** | Independent Financial Advisor — a solo or small-team mutual fund distributor. India has 1.2 lakh+ registered distributors. |
| **MFD** | Mutual Fund Distributor — same as IFA, just a different term. |
| **MFU** | Mutual Fund Utility — an alternative transaction platform (besides BSE StAR MF). Less popular but used by some distributors. |
| **NACH / UPI AutoPay** | Payment mandates that auto-debit a client's bank account for SIP instalments. NACH is the older system; UPI AutoPay is the newer, easier one. |
| **NAV** | Net Asset Value — the price of one unit of a mutual fund. Published daily by AMCs. |
| **NFO** | New Fund Offer — when an AMC launches a new mutual fund scheme. Like an IPO, but for mutual funds. |
| **NISM** | National Institute of Securities Markets — provides certification exams for financial intermediaries. MF distributors must pass NISM Series V-A exam. |
| **Reverse feed** | Data files sent by CAMS/KFintech to distributors, containing transaction details, holdings, commission info. Usually delivered via FTP or email daily. |
| **SIP** | Systematic Investment Plan — investing a fixed amount in a mutual fund every month. The bread and butter of MF distribution. |
| **STP / SWP** | Systematic Transfer Plan / Systematic Withdrawal Plan — auto-transferring between funds or auto-withdrawing from a fund at regular intervals. |
| **Trail commission** | Ongoing commission paid to the distributor as a % of AUM, for as long as the investor stays invested. Typically 0.3-1.0% per year. This is the recurring revenue that makes MF distribution viable. |

### Insurance Distribution

| Term | What it means in plain English |
|------|-------------------------------|
| **Bima Sugam** | IRDAI's upcoming digital platform for insurance — think of it as "UPI for insurance." Will allow policy comparison, purchase, and servicing in one place. |
| **Corporate agent** | A company (bank, NBFC, or other entity) licensed by IRDAI to distribute insurance. Can tie up with max 9 insurers per line (life/general/health). E.g., Motilal Oswal acting as a corporate agent. |
| **Endorsement** | A change to an existing insurance policy — updating address, adding a nominee, changing sum insured. Each change requires processing on the insurer's system. |
| **IMF** | Insurance Marketing Firm — an IRDAI-regulated entity that can market and distribute insurance products. |
| **IRDAI** | Insurance Regulatory and Development Authority of India — the regulator for insurance. Like SEBI is for stocks, IRDAI is for insurance. |
| **Persistency** | The % of policies that stay active (do not lapse) over time. 13th-month persistency means "what % of policies are still active after 1 year?" Higher = better. |
| **PoSP** | Point of Sale Person — a simplified distributor category that can sell basic insurance products with minimal training. IRDAI created this to expand distribution reach. |
| **Proposal** | The application form for insurance — filled by the client, submitted to the insurer, who then "underwrites" (assesses risk and decides premium). Not every proposal becomes a policy. |
| **TPA** | Third Party Administrator — companies that handle health insurance claims processing on behalf of insurers. They manage hospital tie-ups, cashless claims, etc. |
| **Underwriting** | The insurer's process of assessing risk and deciding whether to issue a policy (and at what premium). Involves evaluating medical history, financial profile, lifestyle factors. |

---

## AI and Technology Terms

| Term | What it means in plain English |
|------|-------------------------------|
| **Agentic AI** | AI that does not just answer questions but takes actions — reads emails, fills forms, calls APIs, makes decisions, and executes multi-step workflows. The "agent" part means it can act on your behalf. |
| **API** | Application Programming Interface — a way for two software systems to talk to each other automatically. Instead of a human logging into a website and clicking buttons, an API lets software do it directly. |
| **Claude / Gemini / GPT** | The three leading large language models (LLMs) from Anthropic, Google, and OpenAI respectively. Think of them as very smart text-processing engines that can understand documents, answer questions, and generate structured output. |
| **ETL** | Extract, Transform, Load — the process of pulling data from one system, cleaning/formatting it, and putting it into another system. Most ops work is essentially manual ETL. |
| **Hallucination** | When an AI confidently states something that is wrong. Dangerous in financial services (e.g., wrong PAN number, wrong trade quantity). Guardrails are essential. |
| **HITL** | Human-in-the-Loop — a design pattern where AI does the work but a human reviews/approves before the action is executed. Essential for high-stakes financial decisions. |
| **LLM** | Large Language Model — the AI technology behind ChatGPT, Claude, Gemini. It can read, understand, and generate text (and increasingly, images, code, and structured data). |
| **MCP** | Model Context Protocol — a standard (created by Anthropic) that lets LLMs connect to external tools and data sources. Think of it as a universal adapter that lets AI talk to any system. |
| **Multi-agent** | A system where multiple AI agents with different specialisations work together. E.g., one agent reads the email, another extracts data, a third validates it, a fourth submits it. Like a team of specialists. |
| **OCR** | Optical Character Recognition — technology that reads text from images or scanned documents. Essential for processing physical forms and scanned PDFs. |
| **Prompt** | The instruction you give to an AI model. Good prompts = good results. Bad prompts = garbage. Prompt engineering is the art of writing effective instructions for AI. |
| **RPA** | Robotic Process Automation — the previous generation of automation. Software bots that click buttons and type into screens, mimicking human actions. Works for simple, unchanging workflows. Breaks when UIs change. |
| **Tool use / Function calling** | The ability of an LLM to call external tools — APIs, databases, browsers, file systems — as part of its workflow. This is what makes "agentic" AI possible. |
| **Vector search / RAG** | Retrieval-Augmented Generation — a technique where the AI first searches a knowledge base for relevant information, then generates a response using that context. Useful for policy lookups, regulatory queries. |

---

*If a term is missing, open an issue or submit a PR. This glossary should be exhaustive.*
