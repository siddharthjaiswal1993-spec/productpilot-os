# Page Structure

## Application Layout

```
┌─────────────────────────────────────────────────────────────────┐
│                         Top Bar                                  │
│  [Logo]  [Workspace Name]              [Notifications] [Avatar]  │
├────────────────┬────────────────────────────────────────────────┤
│                │                                                  │
│   Left         │          Main Content Area                      │
│   Sidebar      │                                                  │
│                │                                                  │
│  Signal Hub   │                                                  │
│  Briefs        │                                                  │
│  Prioritization│                                                  │
│  PRDs          │                                                  │
│  Risk Alerts   │                                                  │
│  Stakeholder   │                                                  │
│  Roadmap       │                                                  │
│  ─────────     │                                                  │
│  Settings      │                                                  │
│  Help          │                                                  │
│                │                                                  │
└────────────────┴────────────────────────────────────────────────┘
```

---

## URL Structure

| Page | URL Pattern | Access |
|------|-------------|--------|
| Signal Intelligence Hub | `/signals` | PM Full, Admin |
| Signal Detail | `/signals/:signal_id` | PM Full, Admin |
| Brief List | `/briefs` | All roles |
| Brief Detail | `/briefs/:brief_id` | PM Full, Viewer (read) |
| Prioritization | `/briefs/:brief_id/prioritization` | PM Full |
| PRD List | `/prds` | All roles |
| PRD Detail | `/prds/:prd_id` | PM Full, Viewer (read) |
| Risk Alerts | `/risk` | PM Full, Viewer (read) |
| Risk Brief | `/risk/:alert_id` | PM Full |
| Stakeholder | `/decisions/:decision_id/communications` | PM Full |
| Roadmap Health | `/roadmap` | All roles |
| Settings | `/settings` | Admin |
| Integrations | `/settings/integrations` | Admin |
| Team | `/settings/team` | Admin |
| Audit Log | `/settings/audit` | Admin |

---

## Navigation Hierarchy

### Primary Navigation (Left Sidebar)

1. **Signal Hub** — incoming signals, clusters, trending categories
2. **Briefs** — opportunity briefs (draft, approved, archived)
3. **Prioritization** — priority decisions (pending, approved, history)
4. **PRDs** — product requirements documents (draft, approved, shared)
5. **Risk Alerts** — active alerts (P1/P2/P3), resolved alerts
6. **Stakeholder** — communication drafts, sent communications
7. **Roadmap** — roadmap health, evidence-backing rate
8. **Settings** — integrations, team, configuration

### Secondary Navigation (within pages)

**Briefs page:** Tabs — My Drafts | Team Approved | Archived  
**Prioritization page:** Tabs — Pending | This Week | All Decisions  
**PRDs page:** Tabs — In Progress | Approved | Shared with Engineering  
**Risk page:** Tabs — Active | Resolved | Dismissed

---

## Notification Center

Accessible from the top bar notification bell icon.

**Notification types:**
- New brief ready (agent generated)
- Risk alert (P1 or P2)
- Approval reminder (brief pending for >24 hours)
- Stakeholder draft ready
- Integration health alert
- Weekly roadmap health report ready

**Notification format:** Source icon + message (1 line) + timestamp + action button

**Priority display:**
- P1 risk alerts: Red badge on notification bell; also sent to Slack DM
- Brief ready: Blue badge; stays in notification center until viewed
- Reminders: Shown as gray "reminder" notifications

---

## Mobile Considerations (V1)

V1 is desktop-first. Mobile layout is not designed for V1. The following mobile behaviors are implemented as minimal viable:

- The notification center is accessible on mobile (view-only)
- Approved briefs and decisions can be read on mobile (view-only)
- Draft approval is not supported on mobile — PM must use desktop to approve

This limitation is acceptable for V1 because PM approval workflows are inherently desktop tasks (reviewing evidence, editing PRDs). Mobile access for read-only review and notification monitoring is sufficient for the design partner phase.
