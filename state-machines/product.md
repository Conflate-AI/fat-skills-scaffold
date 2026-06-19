# Product Management Process State Machine

Every product initiative follows this sequence:

```
DISCOVERY ──► PRD ──► SPEC ──► PRIORITIZE ──► DELIVER ──► MEASURE ──► ITERATE
  │              │        │          │             │          │           │
  │              │        │          │             │          │           └─ feed learnings back into DISCOVERY
  │              │        │          │             │          └─ track adoption, satisfaction, business metrics
  │              │        │          │             └─ engineering builds, QA tests, PM validates
  │              │        │          └─ rank against roadmap, allocate resources
  │              │        └─ detailed spec with user stories and acceptance criteria
  │              └─ synthesize discovery into a PRD
  └─ user research, market analysis, stakeholder input, data review
```

## Transition guards

| Transition | Guard |
|---|---|
| DISCOVERY → PRD | User needs validated. Problem well-defined. Stakeholder alignment on opportunity. |
| PRD → SPEC | PRD approved. Testing seams identified. Scope boundaries clear. |
| SPEC → PRIORITIZE | Specs complete with acceptance criteria. Effort estimates available. |
| PRIORITIZE → DELIVER | Prioritized against roadmap. Resources allocated. Sprint/iteration assigned. |
| DELIVER → MEASURE | Feature shipped to users. Tracking and analytics in place. |
| MEASURE → ITERATE | Minimum 2 weeks of usage data. User feedback collected. |

## DISCOVERY detail

1. **User research** — interviews, surveys, usage analytics review
2. **Market analysis** — competitive landscape, market trends, opportunity sizing
3. **Stakeholder input** — sales, support, success, and leadership perspectives
4. **Data review** — existing product analytics, support tickets, feature requests

## PRD detail

1. **Problem statement** — from the user's perspective
2. **Solution direction** — from the user's perspective
3. **User stories** — extensive, numbered list covering all aspects
4. **Implementation decisions** — modules, interfaces, technical clarifications
5. **Testing decisions** — what makes a good test, which modules, prior art
6. **Out of scope** — explicitly list what's NOT included

## SPEC detail

1. **User stories** — with Given/When/Then acceptance criteria
2. **Edge cases** — error states, empty states, permission boundaries
3. **Design decisions** — UI/UX references, interaction patterns
4. **Dependencies** — other teams, external services, data requirements

## PRIORITIZE detail

1. **Scoring** — RICE (Reach × Impact × Confidence / Effort) or equivalent
2. **Roadmap alignment** — does this fit the current theme/OKR?
3. **Resource check** — do we have capacity?
4. **Dependency check** — any blockers or prerequisites?
