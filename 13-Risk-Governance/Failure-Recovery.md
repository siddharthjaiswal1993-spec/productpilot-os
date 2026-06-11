# Failure Recovery

## Design Philosophy

ProductPilot OS is a decision-support system, not a mission-critical operational system. If it goes down, PMs don't lose customer data — they lose AI assistance. The failure recovery strategy reflects this priority: favor correctness and data integrity over availability.

When in doubt: save state, surface the error clearly, let the PM retry. Never silently fail. Never corrupt state. Never produce partial output labeled as complete.

---

## Failure Categories

### Category 1: Agent Execution Failures

**Timeout:** Agent exceeds its SLA latency target.

```
Response:
1. Save partial progress (e.g., sections completed in a PRD generation)
2. Deliver partial output to PM inbox labeled: "Generation incomplete — [step] timed out"
3. Offer retry button: "Retry [incomplete step]"
4. Log timeout with agent name, step, inputs, timestamp
```

**LLM API failure (5xx from provider):**
```
Response:
1. Retry once with exponential backoff (30 seconds)
2. If still failing: queue the generation request with status "Pending retry"
3. PM sees: "Generation queued — AI service temporarily unavailable. Typically resolves in 2–5 minutes."
4. Auto-retry every 5 minutes for up to 30 minutes
5. If still failing after 30 minutes: alert engineering on-call
6. PM can cancel queued generation at any time
```

**Schema validation failure (output malformed):**
```
Response:
1. Log validation failure with details
2. Retry generation once with explicit error message in the prompt: 
   "Previous output was malformed. Required format: [schema]. Please output valid JSON."
3. If second attempt also fails: deliver failure notice to PM with "Contact support" option
4. Engineering alert triggered immediately for repeated schema failures
```

### Category 2: Integration Failures

**Webhook failure (source system not delivering events):**
```
Response:
1. Detect missed events after 15-minute silence period
2. Activate polling fallback automatically
3. PM sees source health indicator: "Slack: Polling mode (webhook delayed)"
4. Engineering alert after 60 minutes of continuous webhook failure
5. When webhook resumes: deactivate polling, process any events received in polling mode
```

**API rate limit exceeded:**
```
Response:
1. Exponential backoff (15s, 30s, 60s, 120s) before retry
2. Queue requests that can be deferred
3. Prioritize real-time signal processing over historical loads
4. If rate limit sustained for >4 hours: alert admin with "Integration throttled" notification
```

**OAuth token expiry:**
```
Response:
1. Attempt silent re-authentication using refresh token
2. If refresh token expired: show PM reconnection prompt
3. Integration paused (no new data) until reconnected
4. Existing indexed data remains available; brief generation continues with "stale" warning
```

### Category 3: Infrastructure Failures

**Vector database unavailable:**
```
Response:
1. All brief generation requests queued with "Intelligence service temporarily unavailable" message
2. Signal ingestion pipeline continues (signals stored but not yet embedded)
3. PM can still access previously generated briefs, decision history, and knowledge graph
4. When service restored: process queued embedding requests and generation requests in order
5. Engineering alert immediately
```

**Knowledge graph query timeout:**
```
Response:
1. Agent proceeds without knowledge graph context
2. Output labeled: "Generated without historical context (knowledge service timeout)"
3. PM informed in brief: "Note: historical context was unavailable. Prior decisions and outcomes not included."
4. Brief can still be approved; historical context absence is noted in decision record
```

**Database (PostgreSQL) unavailable:**
```
Response:
1. Read path: serve from cache for up to 15 minutes
2. Write path: queue writes with guaranteed delivery
3. PM-visible: "System maintenance. Read-only mode." after 5 minutes
4. Engineering alert immediately
```

### Category 4: Data Corruption Scenarios

**Duplicate signal ingestion (same signal ingested twice):**
```
Response:
1. Deduplication pipeline detects duplicate (content hash match)
2. Second ingestion attempt creates a dedup record linked to original
3. Original signal remains in index; duplicate is marked "duplicate" and excluded from retrieval
4. No PM notification needed — handled silently
```

**Corrupted embedding (vector dimension mismatch):**
```
Response:
1. Embedding validation step detects dimension mismatch at index write time
2. Corrupted embedding discarded; signal logged for re-embedding
3. Background re-embedding process reruns the chunk
4. No PM notification unless the re-embedding fails twice (then: signal excluded, PM informed)
```

**Knowledge graph entity conflict (two agents create conflicting entities):**
```
Response:
1. Entity conflict detection: same name, same type, created within 5 minutes
2. Merge conflict flagged for async resolution
3. Higher-confidence entity retained; lower-confidence entity marked "pending merge review"
4. Engineering resolves in next business day review
```

---

## Recovery State Management

Every agent workflow maintains a checkpoint state. If a workflow fails mid-execution:

**State saved:** Which steps completed, what outputs were generated, what evidence was retrieved
**Checkpoint key:** `workflow_id + step_id + execution_timestamp`
**On retry:** Resume from the last successful checkpoint, not from the beginning
**Retention:** Workflow checkpoints kept for 24 hours; PM can retry within that window

---

## Business Continuity

**RPO (Recovery Point Objective):** 1 hour — maximum data loss in a catastrophic failure
**RTO (Recovery Time Objective):** 4 hours — maximum time to restore full service

**Backup strategy:**
- Database: Continuous backup; point-in-time recovery to any second in the past 30 days
- Vector index: Daily snapshot; re-indexing from raw signal archive if needed
- Knowledge graph: Daily snapshot; replicated to secondary region
- Audit logs: Write-once S3 with cross-region replication

**Disaster recovery testing:** Quarterly DR simulation exercise testing full restore from backup.
