# Enterprise Product Intelligence

## The Enterprise Opportunity

ProductPilot OS's initial ICP is Series A–C B2B SaaS companies with 3–15 PMs. This is the right beachhead: these companies have enough PM workflow complexity to feel Signal Collapse acutely, but not so much organizational complexity that enterprise procurement and integration requirements dominate the sale.

But the enterprise opportunity is significantly larger, and the product moats built serving the beachhead — knowledge graph richness, PM Acceptance Rate flywheel, integration depth — compound in the enterprise context.

---

## How Enterprise Product Intelligence Differs

### Scale of Signal Sources
An enterprise product organization (50–200+ PMs) operates across multiple product lines, multiple geographies, and multiple customer segments simultaneously. Signal sources scale proportionally:
- 20,000+ customer support tickets per month (vs. 500 for a Series B company)
- 200+ Gong calls per week
- Multiple Salesforce instances (acquisitions, regional CRMs)
- Jira Portfolio (multi-project, multi-team) vs. a single Jira project
- Internal tools, custom data warehouses, proprietary analytics platforms

The engineering challenge at enterprise scale is not fundamentally different — it is the same architecture at 50x load. The product challenge is more interesting: how does an enterprise PM navigate intelligence across product lines, and how do insights from one product area inform decisions in another?

### Cross-Product Intelligence
The most unique value proposition for enterprise customers is cross-product intelligence: the ability to detect patterns and risks that span multiple product areas or customer segments simultaneously.

**Example:** An enterprise customer using ProductPilot OS across their platform, analytics, and API products detects that a specific enterprise customer segment (fintech companies with custom compliance requirements) is reporting integration friction across all three product areas simultaneously. This pattern would be invisible if each PM team were operating in a silo — it would look like three unrelated product issues. With a shared knowledge graph across product areas, it resolves into a single strategic pattern: "fintech enterprise customers need a compliance-grade integration framework that spans all products."

This cross-product synthesis capability is only possible with a multi-team knowledge graph — and it is only achievable by a company that has built deep trust with the individual PM teams first.

### Portfolio-Level Roadmap Intelligence
At the enterprise scale, the question shifts from "what should my team build next?" to "how does this team's roadmap align with the broader product portfolio strategy?"

Portfolio-level roadmap intelligence in V3+:
- Cross-product dependency detection: does Team A's planned API change create a risk for Team B's integration roadmap?
- Customer portfolio coverage: which customer segments are currently underserved across the product portfolio, and which team is best positioned to address them?
- Investment allocation signals: based on signal volume and business impact data, is the engineering investment allocation across product areas proportional to strategic priority?

These are questions that CPOs and heads of product currently answer through manual quarterly portfolio reviews. A system with full signal access, complete decision history, and cross-product knowledge graph context can provide this synthesis continuously rather than quarterly.

---

## Enterprise-Grade Requirements

Enterprise customers require specific product and compliance capabilities that are not needed for V1:

### SAML/SSO
Enterprise IT departments require identity integration. ProductPilot OS must support SAML 2.0 and OAuth 2.0 SSO with the customer's identity provider (Okta, Microsoft Entra, Google Workspace). Role and permission provisioning via SCIM.

**Roadmap:** V2 (Month 7–14)

### Role-Based Data Segregation at Portfolio Scale
At enterprise scale, a PM on the payments team should not see signals or briefs from the identity team — unless cross-team sharing is explicitly configured by the workspace admin.

**Data model:** Workspace partitioning within a tenant. Each product area is a Workspace. Briefs, signals, and decisions belong to a Workspace. Cross-workspace sharing is a specific configured permission, not a default.

**Roadmap:** V2

### On-Premises or Private Cloud Deployment
Some enterprise customers (healthcare, financial services, government-adjacent) require their data to never leave their infrastructure. ProductPilot OS must offer a deployment model that runs entirely within the customer's AWS or Azure environment.

**Architecture:** Docker Compose / Kubernetes deployment manifests. Customer provisions their own LLM API keys (pointing to Azure OpenAI or Anthropic). ProductPilot OS software license; data sovereignty entirely with customer.

**Roadmap:** V3 (Month 15–24)

### Enterprise Compliance Certifications
- SOC 2 Type II: Required by most enterprise security reviews
- ISO 27001: Required by European enterprise customers
- HIPAA BAA: Required for healthcare customers

**Roadmap:**
- SOC 2 Type I: V1 launch
- SOC 2 Type II: Month 12
- ISO 27001: Year 2
- HIPAA BAA: Available on request for enterprise contracts from V2

### Custom LLM Configuration
Enterprise customers may have data processing agreements or regulatory requirements that prevent them from using third-party LLM APIs. They need the option to configure ProductPilot OS to use:
- Azure OpenAI (data residency within customer's Azure subscription)
- Internal fine-tuned models for specific domains
- Anthropic's enterprise API with DPA

**Roadmap:** V3

---

## Enterprise Sales and Deployment Motion

### Enterprise ICP (Year 2+)
- Series D+ or public software companies
- 20–200 PMs across multiple product lines
- Centralized IT procurement
- Existing compliance certifications required
- Budget owner: VP Product or CPO (not individual PM teams)

### Minimum Contract Size
Enterprise contracts: $75,000–$500,000+ annually. Minimum viable deal for a dedicated enterprise account: $36,000/year (12 seats at $250/PM/month Enterprise pricing).

### Implementation Services
Enterprise deployments require:
- Integration setup support (Salesforce custom objects mapping, Jira project scoping, CRM field configuration)
- Training for PM team leads and admins
- Executive onboarding session with CPO/VP Product
- Quarterly business reviews with PM lead

These services are included in enterprise contracts or offered as a paid implementation package for complex deployments.

### Success Metrics for Enterprise
Enterprise customers measure value differently than self-serve Team customers:

| Metric | SMB/Team | Enterprise |
|--------|---------|----------|
| Primary metric | PM Acceptance Rate | Evidence-Backing Rate at portfolio level |
| Expansion trigger | Seat growth | Product area expansion |
| Renewal evidence | NPS + usage | Quantified time savings + documented decisions |
| Executive sponsor metric | PM satisfaction | CPO: decision quality & speed |

---

## The Enterprise Moat

Enterprise customers create the most durable moat in ProductPilot OS:

**Data depth:** A 3-year enterprise engagement with 50 PMs generates a knowledge graph with 200,000+ signals, 10,000+ decisions, and full outcome tracking across multiple product generations. This is irreplaceable institutional memory. No competitor can recreate it by offering a lower price.

**Switching cost:** When ProductPilot OS is the system of record for product decisions — where every roadmap item has linked evidence, every PRD has citations, and every priority call has an audit trail — replacing it requires migrating the organization's entire decision history. This is a 9–12 month project. The switching cost approaches that of a CRM or ERP.

**Expansion economics:** Enterprise customers naturally expand as new product lines are added to the workspace. NRR from enterprise customers is projected at 140%+ in Year 3, driven entirely by seat and workspace expansion rather than upsell pressure.
