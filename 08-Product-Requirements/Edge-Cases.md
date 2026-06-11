# Edge Cases: ProductPilot OS V1

**Format:** EC-[ID] | Category | Scenario | Expected Behavior | Fallback

---

## Data Quality Edge Cases

| ID | Scenario | Expected Behavior | Fallback |
|----|----------|------------------|---------|
| EC-001 | Signal is ingested but contains no text content (attachment-only Slack message) | Classify as "media signal"; display in feed with "Attachment only — content not indexable"; do not include in evidence assembly | Skip for evidence, log for completeness tracking |
| EC-002 | Two signals from different sources appear to be about the same issue but have 65% semantic similarity (below merge threshold of 75%) | Show as separate signals with a "Possibly related" indicator and suggestion to review together | PM can manually merge if desired |
| EC-003 | A CRM note is updated after it has already been ingested and indexed | Re-index the updated version; deprecate the old chunk version; link new and old in audit trail | If re-index fails, display stale date warning on the signal |
| EC-004 | Signal source returns no results for a brief generation query (zero relevant signals found) | Generate the brief with an explicit "Insufficient evidence" warning; confidence score set to 15/100; PM sees "Evidence thin — consider gathering more signals before prioritizing" | PM can still approve with explicit low-confidence acknowledgment |
| EC-005 | Signal timestamp is missing or malformed (undated Gong transcript) | Use ingestion timestamp as proxy date; display "Date estimated" label in citation | Never display an incorrect date as confident; always indicate when date is estimated |
| EC-006 | CRM note references a customer by nickname that doesn't match the account name in Salesforce | Entity resolution fails; signal is tagged with "Account: Unresolved — [original text]"; not linked to an ARR figure | PM sees signal in feed; can manually tag it to the correct account |
| EC-007 | 200 new signals arrive simultaneously during a high-traffic period | Queue-based processing; signals processed in order; latency SLA measured from queue entry, not API receipt; PM sees "Ingestion queue: [N] pending" indicator | No signals dropped; SLA extended for queue depth, not violated |
| EC-008 | Historical data load for a new customer contains 50,000 documents | Batch processing with progress indicator; PM is notified when indexing is complete; system displays "Indexing in progress — search results may be incomplete" during processing | First search available within 2 hours of large batch start |

---

## Agent Failure Edge Cases

| ID | Scenario | Expected Behavior | Fallback |
|----|----------|------------------|---------|
| EC-009 | Brief generation times out at 90-second p95 threshold | Display partial brief with available evidence and a clear "Generation incomplete" warning; PM can retry or request manual review | Never display an incomplete brief as complete; always indicate generation status |
| EC-010 | LLM returns a response that fails hallucination check (unverifiable claim detected) | Block the claim from display; substitute with "[Evidence not found — this section requires PM review]"; flag the entire output with yellow warning | PM is informed; can regenerate section or write manually |
| EC-011 | Confidence score falls below 30/100 during brief generation | Brief is generated but displayed with a red confidence badge and an explicit warning: "Confidence is very low — recommend gathering more evidence before making a priority decision" | PM can override with explicit acknowledgment logged |
| EC-012 | Agent orchestration fails (one agent in a multi-step workflow times out) | Fail the specific step; save partial progress; notify PM: "[Agent Name] step failed — partial results available. Retry?" | PM can retry the failed step individually; previous steps' results preserved |
| EC-013 | PRD generation produces a section where no evidence is available in the corpus | Display that section with: "[Section] — Insufficient evidence in corpus. PM should populate this section manually." | Never fabricate content in sections without evidence; always surface evidence gaps explicitly |
| EC-014 | Stakeholder communication agent generates a draft that contains a customer name that should not be disclosed to the recipient | PII screening detects the customer name; redacts to "[Customer name — confidential]" before display; PM can review and restore if appropriate | Always err toward protection over disclosure |

---

## Integration Edge Cases

| ID | Scenario | Expected Behavior | Fallback |
|----|----------|------------------|---------|
| EC-015 | Slack API rate limit exceeded during bulk historical data pull | Exponential backoff with maximum 3 retries; PM notified of the delay; partial sync displayed as "Partial — [N] channels synced, [M] pending" | Historical sync completes eventually; real-time events (new messages) given priority in rate limit budget |
| EC-016 | Jira webhook fails to deliver (network error at Jira's end) | Polling fallback activates automatically; Jira polled every 5 minutes as backup; PM sees "Jira connection: polling mode (webhook offline)" | Jira signal latency degrades from <5 min to <10 min during polling mode |
| EC-017 | Salesforce OAuth token expires during an active session | System attempts silent re-authentication; if it fails, PM is shown "Reconnect Salesforce" prompt; brief generation without Salesforce continues with reduced business impact coverage and lower confidence score | CRM-sourced business impact unavailable; confidence score component "Business Impact Coverage" drops |
| EC-018 | Zendesk webhook payload format changes after an API update | Schema validation fails; ingestion pauses for that integration; PM notified: "Zendesk integration requires reconnection — schema change detected" | Historical Zendesk signals remain in index; new signals pause until reconnection |
| EC-019 | Gong API is unavailable for 4 hours (planned maintenance) | No new Gong transcripts ingested during outage; system records the gap in the ingestion log; brief generation continues without Gong data and notes "Gong unavailable since [time]" in confidence score context | System never claims completeness when a data source is offline |
| EC-020 | Two tenants' signal data accidentally maps to the same vector namespace (misconfiguration) | Automated isolation check detects namespace collision; both tenants' data is quarantined; both PMs notified: "Data isolation check failed — your workspace is temporarily paused pending review" | Security over availability: pause both tenants; alert engineering on-call immediately |

---

## User Behavior Edge Cases

| ID | Scenario | Expected Behavior | Fallback |
|----|----------|------------------|---------|
| EC-021 | PM rejects an opportunity brief 3 times in succession with the same feedback | After 3rd rejection, system shows: "You've rejected this brief 3 times. Would you like to discard it, or should we suggest a different evidence framing?" | Rejection logged each time; feedback text used to improve generation |
| EC-022 | PM approves a brief but then immediately makes significant edits to the PRD generated from it | Edits are tracked as PM overrides; edit distance score calculated and logged; major edits (>30% of content changed) trigger a model improvement signal | PM edits are always final; system learns from the delta |
| EC-023 | Two PMs approve conflicting priority decisions for the same backlog item simultaneously | System detects the conflict; shows a "Priority conflict" alert to both PMs; requires one PM to explicitly supersede the other | Conflict is surfaced and logged; never silently resolved in either direction |
| EC-024 | PM requests a brief for an opportunity with fewer than 3 signals in the corpus | Brief is generated with a "Thin evidence" warning; evidence table shows available signals (fewer than 3); confidence score reflects the thin evidence; PM approval requires explicit low-confidence acknowledgment | PM can still generate and approve; the thin evidence state is permanently logged in the decision record |
| EC-025 | PM attempts to delete an approved opportunity brief | System shows: "Approved briefs cannot be deleted — they are part of the decision audit trail. You can archive this brief, which will remove it from active views." | Briefs are immutable after approval; archiving moves them out of active view without destroying the record |
| EC-026 | PM with View-only role attempts to approve a brief | Approve button is not shown in View-only mode; if accessed via direct URL, the system returns 403 with "Insufficient permissions" | Permission check is server-side, not client-side only |

---

## Security Edge Cases

| ID | Scenario | Expected Behavior | Fallback |
|----|----------|------------------|---------|
| EC-027 | A Slack message containing a customer's personal email address is ingested | PII scanner detects the email address; it is stored encrypted separately; in signal display, email is shown as "[email redacted]"; PM can access full PII in source panel with access control | Never index raw PII; access always requires explicit permission |
| EC-028 | An API request arrives with a JWT that is expired | System returns 401 Unauthorized with message "Token expired — please re-authenticate" | No data is returned for expired tokens; token expiry is not extendable via client-side manipulation |
| EC-029 | A large, malformed webhook payload is received (possible injection attempt) | Payload is rejected at schema validation; error logged with source IP; security monitoring alerts if this pattern repeats | Never process malformed payloads; log all rejections for security review |
| EC-030 | A PM queries the knowledge graph for data from another tenant's organization | Query is scoped to the PM's tenant at the database level; cross-tenant entities are physically inaccessible, not just hidden by access control | Tenant isolation is enforced at the database layer, not the application layer alone |
| EC-031 | A Salesforce integration is configured by an Admin who leaves the company | Orphaned integration tokens are not valid for new data access without re-authentication; system shows "Integration requires re-authentication" after the token's TTL expires | No integration operates on indefinitely valid credentials; periodic re-auth required |
| EC-032 | Generated stakeholder communication accidentally includes a confidential contract value | Pre-send review detects financial figures above a configurable threshold; PM is shown a warning: "This communication includes $[amount]. Confirm this is appropriate to share with [audience]." | PM confirmation required before sending any communication containing financial figures |

---

*Edge cases define the boundaries of the happy path. Every edge case documented here is a failure mode prevented. Every undocumented edge case is a failure mode waiting to happen.*
