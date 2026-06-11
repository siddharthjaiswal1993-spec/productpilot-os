# Bolt.new Build Prompt: ProductPilot OS Components

**Use this prompt with Bolt.new to build specific UI components for the ProductPilot OS prototype.**

---

## Component 1: Opportunity Brief Card Component

Build a React component called `OpportunityBriefCard` that represents a single opportunity brief in a list view.

**Props:**
```typescript
interface OpportunityBriefCardProps {
  briefId: string;
  title: string;
  status: 'draft' | 'approved' | 'rejected';
  confidenceScore: number; // 0-100
  evidenceCount: number;
  businessImpact: string; // e.g. "$2.1M ARR"
  createdAt: string;
  productArea: string;
  onClickApprove: (briefId: string) => void;
  onClickView: (briefId: string) => void;
}
```

**Design:**
- Card with white background, 1px border (#E5E7EB), rounded-lg, subtle shadow
- Left colored accent border: yellow for draft, green for approved, red for rejected
- Status badge: top right — "AI Draft" in yellow pill, "Approved" in green pill, "Rejected" in red pill
- Title: semibold, 16px, dark (#111827)
- Confidence score: circular progress indicator (0-100) with color coding (green >70, yellow 50-70, red <50)
- Evidence count: gray text, small (14px): "8 evidence sources"
- Business impact: blue text, semibold: "$2.1M ARR at risk"
- Bottom row: product area tag + created date + "View" button + "Approve" button (only on draft status)

---

## Component 2: Evidence Citation Panel

Build a React component called `CitationPanel` that slides in from the right when a PM clicks a citation.

**Props:**
```typescript
interface CitationPanelProps {
  isOpen: boolean;
  onClose: () => void;
  citation: {
    sourceType: 'slack' | 'zendesk' | 'gong' | 'salesforce' | 'jira' | 'notion' | 'confluence';
    sourceId: string;
    author: string;
    date: string;
    excerpt: string;
    relevanceScore: number;
    originalUrl?: string;
  }
}
```

**Design:**
- Right slide-in panel, width 400px, white background, left border (2px, #E5E7EB)
- Header: source type logo (16x16 icon) + "Source: [Type]" label + close X button
- Author row: person icon + author name + date
- Excerpt section: light gray background (#F9FAFB), 14px mono font, left border accent (3px, source color)
- Relevance score: "Relevance: 94%" in small gray text
- "View Original Source" link button (if URL available)
- "Mark as Incorrect Citation" ghost button at bottom (red text)

**Source color coding:**
- Slack: #4A154B (purple)
- Zendesk: #03363D (dark teal)
- Gong: #FE7700 (orange)
- Salesforce: #1798C1 (blue)
- Jira: #0052CC (blue)

---

## Component 3: Confidence Score Breakdown

Build a React component called `ConfidenceScoreBreakdown` that shows the 5-component confidence score.

**Props:**
```typescript
interface ConfidenceScoreBreakdownProps {
  compositeScore: number;
  components: {
    signalVolume: number; // 0-20
    sourceDiversity: number; // 0-20
    recency: number; // 0-20
    businessImpactCoverage: number; // 0-20
    representativeness: number; // 0-20
  };
  interpretation: string; // e.g. "Strong evidence — 8 signals across 4 sources in the past 45 days"
}
```

**Design:**
- Collapsed state: Large number (48px, semibold) + color-coded badge + "Expand" chevron
- Expanded state: 
  - Composite score prominently displayed
  - Horizontal bar chart for each component (component name + filled bar + score/20)
  - Bar colors: green if >14, yellow if 8-14, red if <8
  - Interpretation text at bottom in gray italic
- Smooth expand/collapse animation

---

## Component 4: Signal Card Feed

Build a React component called `SignalCard` for the Signal Intelligence Hub feed.

**Props:**
```typescript
interface SignalCardProps {
  signalId: string;
  sourceType: 'slack' | 'zendesk' | 'gong' | 'salesforce' | 'jira';
  signalType: 'feature_request' | 'bug_report' | 'churn_risk' | 'competitive' | 'technical';
  excerpt: string;
  author: string;
  sourceContext: string; // channel name or account name
  date: string;
  classificationConfidence: number;
  isSelected: boolean;
  onSelect: (signalId: string) => void;
}
```

**Design:**
- Card with border, white background, hover state (light blue background)
- Left: source type icon (Slack/Zendesk/Gong/Salesforce colored icon, 24x24)
- Top row: signal type badge (colored pill) + source context (gray text, small) + date (gray, right-aligned)
- Excerpt: 2 lines max, truncated with ellipsis, 14px
- Bottom row: author name + classification confidence percentage
- Selected state: blue border, light blue background
- Checkbox appears on hover for multi-select

---

## Component 5: HITL Approval Button Group

Build a React component called `ApprovalActions` for the HITL gate action buttons.

**Props:**
```typescript
interface ApprovalActionsProps {
  itemType: 'brief' | 'prioritization' | 'prd' | 'communication' | 'risk';
  status: 'pending' | 'approved' | 'rejected';
  onApprove: () => void;
  onReject: (reason: string) => void;
  onEdit: () => void;
  onDefer: () => void;
  isLoading?: boolean;
}
```

**Design:**
- Sticky bottom bar (position: sticky, bottom: 0)
- White background, top border (1px #E5E7EB), padding 16px
- Left group: "Defer" ghost button + "Edit" ghost button
- Right group: "Reject" outlined button (red) + "Approve [itemType]" filled button (blue)
- Rejection flow: clicking "Reject" opens an inline dropdown asking for rejection reason (dropdown of categories + optional free text)
- Approval animation: button turns green briefly → shows "Approved ✓" → fades to green approved state
- Loading state: spinner on approve button with "Approving..." text

---

## Stack Requirements

- **Framework:** React 18 with TypeScript
- **Styling:** Tailwind CSS
- **Icons:** Lucide React
- **Animations:** Framer Motion for panel slides and state transitions
- **Font:** Inter (Google Fonts)

## Component Organization

```
src/
  components/
    briefs/
      OpportunityBriefCard.tsx
      CitationPanel.tsx
      ConfidenceScoreBreakdown.tsx
    signals/
      SignalCard.tsx
    shared/
      ApprovalActions.tsx
  pages/
    SignalHub.tsx
    BriefDetail.tsx
    PrioritizationBrief.tsx
```
