# Assets

This directory is the home for all visual and binary assets associated with the ProductPilot OS documentation repository.

---

## What Belongs Here

### Architecture Diagrams (PNG / SVG exports)

Export-quality renders of the Mermaid diagrams defined in the documentation. Priority diagrams to export:

- `system-architecture.png` — Full system architecture (from `18-Technical-Architecture/System-Architecture.md`)
- `agent-orchestration.png` — Agent coordination diagram (from `10-AI-Architecture/Agentic-Architecture.md`)
- `rag-pipeline.png` — Full RAG pipeline (from `11-RAG-Architecture/RAG-Overview.md`)
- `knowledge-graph-schema.png` — Entity-relationship diagram (from `11-RAG-Architecture/Knowledge-Graph.md`)
- `data-pipeline.png` — Data ingestion pipeline (from `18-Technical-Architecture/Data-Pipeline.md`)
- `user-journey-signal-to-decision.png` — End-to-end user journey (from `15-Prototype/Flowchart-Diagram.md`)
- `trust-stack.png` — Trust layer architecture (from `10-AI-Architecture/Trust-Layer.md`)

### Prototype Screenshots

Once the prototype is built via Lovable or Bolt (see `15-Prototype/Lovable-Prompt.md` and `15-Prototype/Bolt-Prompt.md`), capture:

- `screen-executive-dashboard.png`
- `screen-signal-hub.png`
- `screen-opportunity-briefs.png`
- `screen-prioritization-engine.png`
- `screen-prd-copilot.png`
- `screen-risk-watchdog.png`
- `screen-stakeholder-alignment.png`
- `screen-meeting-intelligence.png`
- `screen-knowledge-graph.png`
- `screen-ai-governance.png`

### Brand Assets

- `productpilot-os-logo.svg` — Primary logo (dark background variant)
- `productpilot-os-logo-light.svg` — Light background variant
- `productpilot-os-wordmark.svg` — Wordmark only
- `productpilot-os-icon.svg` — Icon/favicon
- `color-palette.md` — Design system color tokens

### Presentation Exports

- `investor-deck.pdf` — Pitch deck export
- `product-overview.pdf` — Product overview one-pager
- `technical-architecture-brief.pdf` — Technical architecture summary for engineering audiences

### Demo Videos

- `demo-opportunity-brief-generation.mp4` — 2-minute demo of signal to opportunity brief
- `demo-prd-copilot.mp4` — 2-minute demo of transcript to PRD
- `demo-prioritization-engine.mp4` — 2-minute demo of prioritization flow

---

## File Naming Convention

All assets should follow this naming convention:

```
{category}-{descriptive-name}-{variant}.{extension}
```

Examples:
- `diagram-rag-pipeline-v1.png`
- `screen-signal-hub-light.png`
- `brand-logo-dark-background.svg`

---

## Resolution and Format Standards

| Asset Type | Format | Minimum Resolution |
|---|---|---|
| Architecture diagrams | PNG or SVG | 2x (retina) or vector |
| Prototype screenshots | PNG | 1440px wide minimum |
| Logo / icon | SVG preferred | N/A (vector) |
| Presentation exports | PDF | Print quality |
| Demo videos | MP4 (H.264) | 1920x1080 minimum |

---

*All assets in this directory should be treated as public-facing artifacts consistent with the quality standard of the documentation they support.*
