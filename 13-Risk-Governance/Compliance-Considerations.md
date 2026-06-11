# Compliance Considerations

## Overview

ProductPilot OS is a B2B SaaS product handling organizational product intelligence data. The compliance landscape includes data protection regulations, AI-specific governance requirements, enterprise security standards, and sector-specific requirements for customers in regulated industries.

---

## Data Protection Regulations

### GDPR (EU General Data Protection Regulation)

**Applicability:** Any tenant with EU employees or EU customer data  
**ProductPilot OS role:** Data processor (processes data on behalf of the customer, who is the data controller)

**Requirements and implementation:**

| Requirement | Implementation |
|-------------|---------------|
| Lawful basis for processing | Contract performance for organizational data; documented in DPA |
| Data subject rights | Access, deletion, portability, rectification — all supported via admin tools |
| Data Processing Agreement | Standard DPA available; customer countersigns |
| Data residency | EU data residency option for enterprise customers (EU-hosted deployment) |
| Breach notification | P0 incident response triggers 72-hour regulatory notification process |
| Privacy by design | PII scanner on ingestion + generation; data minimization; retention limits |

### CCPA (California Consumer Privacy Act)

**Applicability:** Tenants with California consumer data (primarily relevant when customer interview transcripts or consumer support data is ingested)  
**Key requirements:** Right to deletion, right to know, right to opt-out of "sale" of data  
**Implementation:** Admin can execute data deletion on behalf of data subjects; ProductPilot OS does not sell data

### UK GDPR

Post-Brexit equivalent of EU GDPR with similar requirements. Standard compliance approach applies.

---

## Enterprise Security Standards

### SOC 2 Type II

**Target:** Achieve SOC 2 Type II certification by V2 launch (12 months post-V1)

**Trust Service Criteria in scope:**
- **Security:** Access controls, encryption, vulnerability management, incident response
- **Availability:** Uptime SLA, disaster recovery, monitoring
- **Confidentiality:** Tenant data isolation, encryption, access logging
- **Processing Integrity:** Audit log immutability, HITL gates, output validation

**Readiness milestones:**
- V1 launch: SOC 2 Type I readiness review; controls designed and in place
- Month 6: SOC 2 Type II observation period begins
- Month 12 (V2): SOC 2 Type II report issued; available to enterprise customers

**Design partner access:** Enterprise design partners receive access to the SOC 2 readiness package (control descriptions, evidence examples) prior to formal certification.

### ISO 27001

Not in scope for V1/V2. Planned for enterprise tier expansion (Year 2+).

---

## AI-Specific Compliance

### EU AI Act

**Risk classification:** ProductPilot OS is likely classified as "limited risk" (AI systems that generate synthetic content or analyze data for business decisions) rather than "high risk" (AI systems that affect employment, creditworthiness, or essential services).

**Requirements for limited risk:**
- Transparency obligations: Users must know they're interacting with AI
- Documentation: Maintain technical documentation of the AI system

**Implementation:** All agent outputs are labeled as AI-generated. The "How was this generated?" transparency panel is available on all outputs. Technical documentation (this repository) is maintained and versioned.

### Sector-Specific Considerations

**Healthcare customers (HIPAA):**
- ProductPilot OS supports HIPAA requirements for customers in healthcare
- Business Associate Agreement (BAA) available for enterprise customers
- PHI detection in PII scanner
- Data residency and encryption requirements met
- Not enabled for Starter/Team tier; enterprise tier only

**Financial services customers:**
- Data residency requirements accommodated on enterprise tier
- Audit trail meets financial record-keeping requirements
- No financial advice generation — output is product intelligence, not financial guidance

**Legal and professional services:**
- Attorney-client privileged documents can be excluded from indexing
- Admin can designate specific source types or channels as privileged (excluded from indexing)

---

## Vendor Risk Management

Enterprise customers frequently conduct vendor security assessments. ProductPilot OS provides:

**Standard security package:**
- SOC 2 Type I report (at V1 launch)
- Penetration test results summary (annual)
- Data Processing Agreement
- Sub-processor list (LLM providers, vector DB, infrastructure)
- Security questionnaire responses (SIG Lite, CAIQ)

**Sub-processors disclosed:**
- LLM API providers (Anthropic or OpenAI — customer chooses)
- Vector database provider (Pinecone / Weaviate)
- Cloud infrastructure (AWS primary region)
- Analytics (anonymous product usage only; no customer data)

---

## Compliance Roadmap

| Certification/Standard | Timeline | Tier |
|----------------------|----------|------|
| GDPR compliance | V1 launch | All tiers |
| CCPA compliance | V1 launch | All tiers |
| SOC 2 Type I readiness | V1 launch | Enterprise design partners |
| SOC 2 Type II | Month 12 (V2) | All enterprise |
| HIPAA BAA | Month 6 | Enterprise healthcare |
| ISO 27001 | Year 2 | Enterprise |
| EU AI Act compliance | Before EU commercial launch | All tiers |
