---
name: product-explorer
description: >
  Explore any URL, local web app, or desktop app from a product perspective.
  Use when asked to inspect, map, screenshot, analyze, benchmark, clone,
  replicate, reverse-engineer, or research an app/web product's pages,
  key flows, UI/UX, interactions, motion, design language, and implementation
  requirements. Produces a local exploration bundle with screenshots, page
  notes, competitive research, and a replication spec.
---

# Product Explorer

Explore a target product like a senior product designer and competitive
researcher, not like a blind crawler. Prioritize the product's core value,
critical paths, information architecture, UI/UX, design language, and the
parts another agent would need to replicate the experience.

## Inputs

Accept any of these targets:

- A public URL.
- A local web app URL such as `http://localhost:3000`.
- A desktop app or already-open browser/app that can be controlled with
  Computer Use.
- Optional login state, test credentials, desired viewport, or scope.
- Optional save path. If omitted, save under the current working directory.

Use browser automation first for web targets. Use Computer Use when the task
depends on the user's real desktop state, installed apps, logged-in sessions,
native UI, or mouse/keyboard interaction that browser automation cannot reach.
If anti-bot protection blocks ordinary automation and a stealth browser skill
is available, switch to that only when appropriate.

## Output Bundle

Create a local directory for every run. Default:

```text
explorations/
└── target-name-YYYY-MM-DD-HHMM/
    ├── report.md
    ├── replication-spec.md
    ├── artifacts.json
    ├── pages/
    ├── screenshots/
    ├── flows/
    └── videos/
```

If the user provides a save path, use it. Keep paths stable and reference
screenshots/videos from the markdown files with relative paths.

## Exploration Strategy

Use a two-pass strategy:

1. Structure discovery.
   - Crawl shallowly from the entry point.
   - Collect visible navigation, links, buttons, forms, route patterns, modal
     triggers, command palettes, sidebars, tabs, and primary CTAs.
   - Stay inside the product surface. Ignore docs, blogs, legal pages, press,
     social links, and external marketing destinations unless they are part of
     the product experience.
   - Default depth: marketing/landing pages up to 2 levels; authenticated app
     surfaces up to 3 levels of important paths.
   - Treat crawler output as candidates, not truth.

2. Product-led exploration.
   - Rank candidate routes by product importance.
   - Follow critical paths first: onboarding, activation, dashboard, create,
     edit, import, upload, generate, search, collaborate, share, export,
     checkout/pricing, settings when they shape the product model.
   - Sample repeated pages and list/detail patterns instead of exhaustively
     opening every item.
   - Record minor controls in notes without taking separate screenshots.

The agent decides what matters. Do not mechanically screenshot every toggle,
filter, hover state, or dropdown.

## Screenshot Rules

Always screenshot:

- Entry page and first viewport.
- Main navigation destinations and primary app surfaces.
- Each major step in a core task flow.
- Major structural states: empty state, loading/skeleton, populated state,
  success, error, modal, drawer, command palette, editor/canvas state.
- Distinct mobile layouts when the target clearly supports mobile or when the
  user asks for mobile coverage.
- Signature design moments or interactions that define the product.

Usually record in notes only:

- Ordinary toggles, checkboxes, sort/filter controls, and repeated dropdowns.
- Repeated list item hover states.
- Tooltips unless they carry essential product explanation.
- Minor visual variants of an already captured component.

Viewport coverage is situation-dependent. Use desktop by default for web apps.
Add mobile/tablet only when the product appears responsive, mobile-first, or
the user asks for it. Some products only have one meaningful surface.

## Motion And Video

Most motion should be described in text. Capture short video only when motion
is central to understanding or replicating the experience:

- Canvas, 3D, spatial, map, timeline, or editor interactions.
- AI generation/progress/result transitions.
- Onboarding or conversion motion that explains the product.
- Drag/drop, command palettes, or interaction feel that differentiates the app.
- Brand-signature animation, page transitions, or loading states that must be
  reproduced to match the product.

Save videos in `videos/` and reference them from page notes and flow notes.
For ordinary transitions, write concise motion notes: duration estimate,
easing feel, direction, opacity/scale/translation, and feedback behavior.

## Safety Boundary

The product is the focus, not account management. Login is allowed when needed
to reach the product. Registration, paid checkout, publish, send, invite,
delete, third-party authorization, and other externally visible or irreversible
actions should stop before final submission unless the user explicitly approves
and provides a safe test context.

If credentials or an authenticated state are required, ask for them or use the
user's already-open logged-in browser/app when available.

## What To Analyze

Every run must cover:

- Product thesis: what it is, who it serves, and what core value it promises.
- Critical paths: how users reach the product's main value.
- Information architecture: routes, navigation, object model, hierarchy.
- Feature model: primary entities, actions, states, permissions, and settings.
- UI/UX: layout, density, affordances, feedback, error handling, empty states,
  search/filtering, creation/editing, collaboration, sharing, export.
- Design language and aesthetic intent.
- Replication requirements: what another agent needs to build the experience.

### Design Language And Authorial Intent

Make aesthetics a first-class section. Ground every judgment in observable
evidence from screenshots, pages, components, or interactions.

Analyze:

- Overall impression.
- Visual vocabulary: color, typography, spacing, radius, shadow, borders,
  materials, icons, illustration, imagery, and texture.
- Composition: density, whitespace, alignment, hierarchy, rhythm.
- Interaction feel: motion speed, easing, hover/click feedback, transitions.
- Brand/personality: professional, playful, editorial, developer-first,
  luxury, AI-native, utilitarian, experimental, etc.
- Signature elements: 3-7 distinctive patterns that make the product feel like
  itself.
- Authorial intent: evidence-backed inference about what the maker prioritizes.
- Reusable patterns: what should be preserved in a clone or redesign.

Write authorial intent as inference, not fact. Example: "The sparse layout,
large type, and slow reveal animations suggest the product prioritizes brand
ritual and perceived craft over operational density."

## Report Files

### `report.md`

Use this structure:

```markdown
# Product Exploration Report: <Target>

## Executive Summary
## Coverage
## Product Thesis
## Critical User Paths
## Information Architecture
## Page Inventory
## Feature Model
## UI/UX Analysis
## Design Language / Aesthetic Intent
## Motion And Interaction Notes
## Conversion / Business Mechanics
## Strengths
## Weaknesses / Risks
## Opportunities
## Open Questions
```

### `replication-spec.md`

Use this structure:

```markdown
# Replication Spec: <Target>

## Product Scope
## P0 Critical Flows
## P0 Pages
## P0 Components
## P1 Quality / Interaction Details
## P2 Deferrable Details
## Route / IA Proposal
## Data Model Clues
## Visual Tokens
## Motion Requirements
## Component State Matrix
## Do Not Overcopy
## Implementation Notes
## Open Questions
```

Use priorities:

- `P0`: required to reproduce the core product experience.
- `P1`: important to quality, trust, or design fidelity.
- `P2`: minor, edge, or deferrable.

## Page Notes

Create one markdown file per significant page or state:

```markdown
# 001 - Home

- URL/location:
- Role:
- Priority:
- Screenshot(s):
- Related flow(s):

## What This Page Does
## Layout And Hierarchy
## Key Components
## Primary Interactions
## States Observed
## Copy / Messaging Notes
## Design Notes
## Motion Notes
## Replication Notes
```

## Flow Notes

Create files under `flows/` for critical flows:

```markdown
# Signup To Activation

- Priority:
- Start:
- End:
- Screenshots/videos:

## Steps
## Decision Points
## UX Feedback
## Failure / Empty States
## Replication Requirements
```

## `artifacts.json`

Keep JSON machine-readable. Store indexes and structured conclusions, not full
prose.

```json
{
  "target": {
    "input": "",
    "resolved_name": "",
    "type": "url|local_web_app|desktop_app",
    "captured_at": ""
  },
  "coverage": {
    "viewports": [],
    "auth_state": "anonymous|authenticated|unknown",
    "exploration_strategy": "critical_paths_first",
    "visited_pages": 0,
    "captured_screenshots": 0,
    "captured_videos": 0
  },
  "pages": [
    {
      "id": "",
      "title": "",
      "url_or_location": "",
      "role": "entry|dashboard|settings|checkout|content|editor|modal_state|other",
      "priority": "P0|P1|P2",
      "screenshot_paths": [],
      "video_paths": [],
      "notes_path": "",
      "key_components": [],
      "interactions": [],
      "aesthetic_signals": []
    }
  ],
  "flows": [
    {
      "id": "",
      "name": "",
      "priority": "P0|P1|P2",
      "steps": [],
      "screenshots": [],
      "videos": []
    }
  ],
  "replication": {
    "p0_pages": [],
    "p0_components": [],
    "signature_patterns": [],
    "visual_tokens": {},
    "motion_requirements": [],
    "open_questions": []
  }
}
```

## Final Response

After completing the exploration, respond with:

- Bundle path.
- Coverage summary.
- Most important product/design findings.
- Any blocked areas, missing auth, or actions intentionally not performed.
- Suggested next step: implementation, deeper audit, or side-by-side competitor
  comparison.

Keep the final answer concise. The detailed work belongs in the generated
bundle.
