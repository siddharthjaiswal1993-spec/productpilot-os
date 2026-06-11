# UX Principles

## Core UX Philosophy

ProductPilot OS is built for PMs, not data scientists. The UX must feel like a smart colleague who has already done the research — not like a search engine or a dashboard. The PM should spend their time deciding, not navigating.

---

## Principle 1: The PM is Always the Author

Every agent output is labeled as a draft. The PM is the author of every approved brief, PRD, and communication — the AI is the researcher. The UX enforces this framing:
- "AI Draft" label is visible on every generated output until approval
- Approval converts the draft to a PM-owned document
- After approval, the document is attributed to the PM, not to "ProductPilot AI"
- The PM can edit anything before approving — the AI never locks content

### Design implementation:
- Yellow "AI Draft" badge in the top right corner of all generated content
- Badge disappears on approval; document header shows PM name + approval timestamp
- All text fields are editable (no read-only mode before approval)

---

## Principle 2: Evidence Should Be One Click Away

Every citation is a one-click journey to the source. Evidence is never presented as a number or a claim — it is always an invitation to verify.

### Design implementation:
- Inline superscript citation numbers on every factual claim
- Hover tooltip shows: source type, author, date, 2-line excerpt
- Click opens citation panel with full source details and link to original
- Evidence table in every brief is expandable without leaving the page
- "Confidence score" is a clickable element that opens a component breakdown

---

## Principle 3: PM Workflow, Not AI Interface

The PM should think of ProductPilot as part of their workflow, not as a separate AI tool they visit occasionally. The UX integrates into natural PM work patterns:

- **Morning inbox:** Briefs arrive while the PM is reviewing other updates
- **While in Jira:** A browser extension or deep link from a Jira ticket to the related brief in ProductPilot
- **After a customer call:** Meeting Intelligence notification appears 30 minutes after a Gong call ends
- **Before a planning meeting:** The weekly Roadmap Health report is in the inbox by Monday 8am

### Design implementation:
- Notifications are PM-time-aware (respect working hours; configurable)
- Slack integration for notification delivery (brief ready → Slack DM)
- Deep linking from Jira issue to related ProductPilot brief
- Calendar integration for proactive "meeting briefing" 15 minutes before relevant calls

---

## Principle 4: Speed Respects PM Attention

PMs don't have time to spend 10 minutes reviewing an AI output. Every interaction is designed for the PM who has 3 minutes.

- **Hierarchy of information:** Most important information at the top. Details available on expansion, not by default.
- **Scannable structure:** Every brief has a TL;DR section. Evidence table is collapsed by default. Full reasoning is in "Show more."
- **Progressive disclosure:** Confidence score shows as a number first → expand for components → expand for evidence list
- **No mandatory walkthroughs:** PMs can jump to any section; the flow is a default, not a requirement

### Design implementation:
- Opportunity brief top section: problem statement (3 sentences max) + confidence score + key evidence count + primary recommended action
- Full evidence table: collapsed by default, expand with one click
- RICE breakdown: headline score shown; full dimension breakdown on expand
- PRD approval: PM can mark individual sections as reviewed; system tracks which sections haven't been seen

---

## Principle 5: Trust Signals Are Everywhere

PMs need continuous reinforcement that what they're looking at is real and verifiable. Trust signals appear at every level:

- **Source logos:** Zendesk, Salesforce, Gong logos appear next to every citation (not just text labels)
- **Real dates:** Every piece of evidence shows its original source date (not ingestion date)
- **Author names:** Every signal shows the author (real person) wherever available
- **Confidence explanation:** Every confidence score has a plain-language interpretation ("Strong confidence — 8 signals across 4 sources in the past 45 days")
- **"How was this generated?":** Available on every brief; shows the agent's retrieval queries and reasoning steps in plain language

---

## Principle 6: Approval is a Moment, Not a Burden

Approval gates should feel like a natural step in the PM's workflow, not an interruption.

- The approval button is prominent but not alarming
- Editing before approving is frictionless — same page, no mode switching
- The approval action is named clearly by consequence: "Approve Brief," "Approve Priority Decision," "Approve for Engineering," "Confirm Communication"
- Approval confirmation is brief (1 line): "Brief approved. Prioritization and PRD now available."

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Wrong |
|-------------|---------------|
| Hallucination confidence theater ("AI is 97% confident") | PM stops trusting; doesn't understand what 97% means |
| Burying evidence in an accordion | PM never verifies; trust erodes when something is wrong |
| Requiring PM to configure before first use | Reduces time-to-value; onboarding should be lightweight |
| Chat interface as primary UX | Chat requires PM to know what to ask; agents should proactively surface |
| AI acting without showing work | Opacity destroys trust at first mistake |
| Over-notification | PM disables notifications; loses awareness of proactive intelligence |
