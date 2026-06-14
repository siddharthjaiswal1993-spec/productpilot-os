# What I Built — ProductPilot OS

This is the flagship project in my portfolio. I wrote every document here from first principles — no templates, no generated boilerplate. The 19 sections represent a complete product lifecycle from problem framing to investor narrative, written as if I were leading the product at a funded startup.

---

## What I Actually Did

**I defined the problem from scratch.** The "product signal collapse" framing — the idea that the core PM crisis is not a tooling problem but a reasoning and evidence problem — is my own analysis. I wrote the problem space (Section 03) to build a precise diagnosis before proposing a solution, the same way I'd approach a discovery brief.

**I designed a multi-agent architecture for a product I know deeply.** Having spent 9 years working in enterprise B2B SaaS across workflow, governance, and integrations, I know where the evidence actually lives (CRM notes, Slack threads, Jira, support tickets, call transcripts) and what PMs actually need to do with it (write PRDs, score priorities, update stakeholders). I designed 8 specialised agents — not a single general-purpose one — because I knew each output type requires a different signal input set and a different quality bar.

**I wrote the evaluation framework myself.** Section 12 defines how each agent's output is measured: human acceptance rate, citation quality, output freshness, and a composite trust score. I wrote this because I believe evaluation is a PM responsibility, not something to delegate to an ML team after launch.

**I designed the RAG architecture as a product bet, not just a technical choice.** The decision to use a knowledge graph (Features, Customers, Signals, Decisions, Outcomes) over fine-tuning is a product strategy decision: a compounding organisational memory creates a defensible moat; a fine-tuned model is a one-time improvement. That reasoning is documented in Section 11.

**I wrote the governance layer.** Section 13 covers PII handling, audit log design, permission model, and the human-in-the-loop approval gates. In enterprise AI, the governance model is part of the product. I did not treat it as a compliance appendix.

---

## The Scope

| Area | What I wrote |
|---|---|
| Problem + vision | Signal collapse diagnosis, why-now analysis, category narrative, mission |
| Market | Competitive landscape, market trends, comparable companies, category creation thesis |
| Strategy | North star, product bets, pricing model, positioning, business model |
| Users | 10 persona documents — each with JTBD, workflow map, pain points, and agent interaction design |
| Journeys | 7 end-to-end journey maps showing agent reasoning steps and before/after time comparisons |
| Requirements | Full PRD, MVP scope, functional/non-functional requirements, acceptance criteria, user stories |
| AI architecture | Agent specs (all 8), orchestration design, reasoning layer, confidence model, trust score |
| RAG | Full pipeline: chunking strategy, embedding approach, retrieval + re-ranking, knowledge graph schema |
| Evals | Eval stack, online/human/LLM-as-judge rubrics, golden dataset design, hallucination checks |
| Governance | Responsible AI, PII, security model, audit logs, RBAC, compliance framework |
| Roadmap | V1 PM Copilot → V2 Team Intelligence → V3 Agentic Product Org |
| GTM | ICP, positioning, messaging, pricing tiers, launch strategy, sales motion, competitive battlecards |
| Investor narrative | Why-now, market opportunity, category creation thesis, product moat, investor memo |
| Technical architecture | System design, integrations (Slack, Jira, CRM, Support, Analytics, Billing), API design |
| Future vision | Product OS, autonomous PM, multi-agent product team |

---

## The Decisions I'm Most Confident In

**Evidence-first output model** — Every agent output must cite the specific source document, author, date, and excerpt that supports it. No citations means no output surfaced to the user. I made this a hard product requirement because in a PM context, an uncited recommendation is just noise. This is documented in Section 12.

**8 narrow agents instead of one broad one** — Each agent has a defined signal input set, a defined output type, and a dedicated evaluation rubric. A general-purpose "intelligence agent" would have unclear quality criteria and no ownership model. Narrower agents are evaluable; broad agents are not.

**RAG over fine-tuning** — The knowledge graph compounds with every interaction; a fine-tuned model does not. The architecture bet is documented in Section 11 with explicit reasoning about what each approach can and cannot do.

**Trust score gates autonomy** — The evaluation framework produces a per-agent composite trust score. Agents below threshold get human review; agents above threshold can take approved actions. This is not a binary human-in-the-loop/autonomous choice — it is a graduated model based on measured reliability.
