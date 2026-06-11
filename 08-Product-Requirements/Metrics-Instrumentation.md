# Metrics Instrumentation: ProductPilot OS V1

**Purpose:** This document defines the full event instrumentation plan for V1. Every event listed here must be implemented before launch. Properties define the data model for each event.

**Format:** Event Name | Trigger | Properties | Purpose

---

## Signal Ingestion Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `signal.ingested` | Signal received from any source | `signal_id`, `source_type`, `tenant_id`, `ingestion_latency_ms`, `signal_size_chars` | Track ingestion volume and latency by source |
| `signal.classified` | Classification pipeline completes | `signal_id`, `classification`, `classification_confidence`, `duration_ms`, `model_version` | Monitor classification quality and speed |
| `signal.deduplicated` | Signal matched to an existing cluster | `signal_id`, `cluster_id`, `match_confidence`, `dedup_method` | Track deduplication effectiveness |
| `signal.indexed` | Signal chunk added to vector index | `signal_id`, `chunk_count`, `embedding_model`, `index_latency_ms`, `tenant_id` | Monitor embedding pipeline performance |
| `signal.pii_detected` | PII detected in signal during scanning | `signal_id`, `pii_type`, `pii_count`, `redaction_applied` | Audit PII handling compliance |
| `signal.ingestion_failed` | Signal fails to process | `signal_id`, `source_type`, `error_type`, `error_message`, `retry_count` | Debug integration failures |
| `signal.manually_submitted` | PM submits a signal manually | `signal_id`, `pm_id`, `source_description`, `product_area` | Track manual signal volume by PM |
| `signal.trend_threshold_triggered` | Volume trend exceeds 30% threshold | `category`, `baseline_volume`, `current_volume`, `percentage_change`, `tenant_id` | Drive risk watchdog alerts |
| `signal.viewed` | PM opens a signal detail | `signal_id`, `pm_id`, `source_type`, `dwell_time_ms` | Measure signal engagement |
| `signal.actioned` | PM clicks "Generate Brief" from a signal | `signal_id`, `cluster_id`, `pm_id`, `action_type` | Track signal-to-action conversion |

---

## Opportunity Brief Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `brief.generation_started` | PM triggers brief generation | `brief_id`, `pm_id`, `cluster_id`, `signal_count`, `trigger_type` (manual/automatic) | Track brief generation volume and trigger types |
| `brief.retrieval_complete` | RAG retrieval step completes | `brief_id`, `retrieved_chunk_count`, `retrieval_latency_ms`, `top_k_used`, `rerank_applied` | Monitor retrieval quality and speed |
| `brief.generation_complete` | Brief generation completes | `brief_id`, `generation_latency_ms`, `confidence_score`, `evidence_count`, `model_version` | Track generation performance and quality |
| `brief.generation_failed` | Brief generation fails | `brief_id`, `failure_reason`, `timeout_at_step`, `pm_id` | Debug generation failures |
| `brief.viewed` | PM opens a brief | `brief_id`, `pm_id`, `time_to_view_after_generation_ms`, `dwell_time_ms` | Measure brief engagement |
| `brief.citation_expanded` | PM clicks to expand a citation | `brief_id`, `citation_index`, `source_type`, `pm_id` | Track citation engagement (trust signal) |
| `brief.edited` | PM edits any field in the brief | `brief_id`, `pm_id`, `field_changed`, `edit_type` (add/modify/remove), `edit_length_chars` | Track edit patterns for model improvement |
| `brief.approved` | PM approves the brief | `brief_id`, `pm_id`, `approval_latency_ms` (from generation), `edit_count`, `confidence_score_at_approval` | Core quality metric: acceptance |
| `brief.rejected` | PM rejects the brief | `brief_id`, `pm_id`, `rejection_reason`, `confidence_score_at_rejection`, `rejection_count_for_brief` | Track rejection reasons for improvement |
| `brief.deferred` | PM defers the brief for later review | `brief_id`, `pm_id`, `defer_duration_days` | Track deferral patterns |

---

## Prioritization Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `prioritization.brief_generated` | Prioritization brief generated from approved opportunity brief | `pri_brief_id`, `opp_brief_id`, `pm_id`, `rice_score`, `strategic_alignment_score`, `composite_score`, `generation_latency_ms` | Track prioritization scoring volume and quality |
| `prioritization.backlog_compared` | Backlog comparison table rendered | `pri_brief_id`, `backlog_item_count`, `suggested_rank`, `pm_id` | Track backlog comparison usage |
| `prioritization.score_overridden` | PM manually adjusts a RICE dimension | `pri_brief_id`, `dimension_overridden`, `original_value`, `new_value`, `override_reason`, `pm_id` | Track overrides for calibration improvement |
| `prioritization.decision_approved` | PM approves priority decision | `pri_brief_id`, `final_priority`, `pm_id`, `time_to_approval_ms` | Track prioritization decision volume |
| `prioritization.jira_update_staged` | Priority change staged for Jira write | `pri_brief_id`, `jira_issue_id`, `old_priority`, `new_priority`, `pm_id` | Track Jira update requests |
| `prioritization.jira_update_approved` | PM confirms Jira priority update | `pri_brief_id`, `jira_issue_id`, `pm_id`, `confirmation_latency_ms` | Track Jira writes (with approval) |
| `prioritization.jira_update_cancelled` | PM cancels staged Jira update | `pri_brief_id`, `jira_issue_id`, `pm_id`, `cancel_reason` | Track Jira update cancellations |
| `prioritization.okr_alignment_calculated` | Strategic alignment score calculated | `pri_brief_id`, `okr_count`, `alignment_score`, `top_aligned_okr` | Monitor OKR alignment quality |

---

## PRD Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `prd.generation_started` | PM triggers PRD generation from brief | `prd_id`, `brief_id`, `pm_id`, `template_configured` | Track PRD generation volume |
| `prd.generation_complete` | PRD draft generation completes | `prd_id`, `generation_latency_ms`, `section_count`, `user_story_count`, `citation_count`, `edge_case_count` | Monitor PRD quality metrics |
| `prd.generation_failed` | PRD generation fails | `prd_id`, `failure_reason`, `failed_section` | Debug generation failures |
| `prd.section_edited` | PM edits a PRD section | `prd_id`, `section_name`, `pm_id`, `edit_type`, `word_count_before`, `word_count_after` | Track edit patterns for quality measurement |
| `prd.user_story_citation_viewed` | PM opens citation for a user story | `prd_id`, `user_story_id`, `citation_count`, `pm_id` | Measure citation engagement in PRDs |
| `prd.edge_case_accepted` | PM accepts a suggested edge case | `prd_id`, `edge_case_index`, `source` (knowledge_graph/llm), `pm_id` | Track edge case acceptance rate |
| `prd.edge_case_rejected` | PM rejects a suggested edge case | `prd_id`, `edge_case_index`, `pm_id` | Track edge case rejection for quality improvement |
| `prd.approved` | PM approves PRD for sharing | `prd_id`, `pm_id`, `approval_latency_ms` (from generation), `total_edits`, `word_count_delta` | Core quality metric: PRD acceptance |

---

## Approval and Rejection Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `approval.requested` | System generates any output requiring PM approval | `item_id`, `item_type` (brief/prd/risk_brief/stakeholder_update), `pm_id`, `confidence_score` | Track approval volume by item type |
| `approval.granted` | PM approves any output | `item_id`, `item_type`, `pm_id`, `time_to_approval_ms`, `edit_count`, `edit_distance_score` | Primary quality flywheel event |
| `approval.rejected` | PM rejects any output | `item_id`, `item_type`, `pm_id`, `rejection_reason_category`, `rejection_reason_text` | Primary model improvement signal |
| `approval.edit_distance_calculated` | Edit distance between generated and approved version computed | `item_id`, `item_type`, `edit_distance_score` (0–1, where 0=no change, 1=complete rewrite), `major_edit` (bool) | Key quality metric: PM acceptance rate |
| `approval.regeneration_requested` | PM requests regeneration of a rejected output | `item_id`, `item_type`, `pm_id`, `prior_rejection_count`, `rejection_reason_category` | Track regeneration rate |
| `approval.override_logged` | PM overrides a system recommendation | `item_id`, `override_type`, `original_value`, `new_value`, `pm_id`, `override_reason` | Track override patterns for calibration |

---

## Knowledge Graph Events

| Event | Trigger | Properties | Purpose |
|-------|---------|-----------|---------|
| `kg.entity_created` | New entity added to knowledge graph | `entity_id`, `entity_type`, `source_item_id`, `source_type`, `tenant_id` | Track graph growth rate |
| `kg.relationship_created` | New relationship added between entities | `relationship_id`, `from_entity_type`, `to_entity_type`, `relationship_type`, `tenant_id` | Track graph connectivity growth |
| `kg.query_executed` | PM or agent queries the knowledge graph | `query_id`, `query_text_hash`, `entity_types_queried`, `result_count`, `latency_ms`, `pm_id` | Track knowledge graph usage |
| `kg.decision_recorded` | A priority decision is added to the decision history | `decision_id`, `priority_level`, `brief_id`, `pm_id`, `timestamp` | Track decision history growth |
| `kg.outcome_recorded` | An outcome is linked to a past decision | `outcome_id`, `decision_id`, `outcome_type`, `outcome_value`, `pm_id` | Track outcome tracking for predictive intelligence |
| `kg.entity_resolved` | Entity in signal matched to existing entity in graph | `entity_id`, `signal_id`, `resolution_confidence`, `resolution_method` | Monitor entity resolution quality |

---

*Every event listed here must be implemented before V1 launch. Instrumentation is not an afterthought — it is the feedback system that makes the product improve. Missing events means missing learning.*
