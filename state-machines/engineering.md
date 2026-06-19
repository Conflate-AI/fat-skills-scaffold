# Engineering Process State Machine

Every issue follows this sequence. No step may be skipped.

```
TRIAGE ──► PLAN ──► BRANCH ──► TDD-LOOP ──► COMMIT ──► REVIEW ──► FIX ──► ASK-MERGE ──► DONE
  │           │         │          │          │          │         │         │
  │           │         │          │          │          │         │         └─ merge → delete branch → return to main
  │           │         │          │          │          │         └─ fix all C+W findings
  │           │         │          │          │          └─ async delegate to reviewer subagent
  │           │         │          │          └─ commit with issue ref
  │           │         │          └─ one test → one impl → repeat. Refactor only on GREEN.
  │           │         └─ from main only. Slug: feature/X, fix/Y
  │           └─ confirm interface + behaviours with user
  └─ must be ready-for-agent or ready-for-human
      │
      └─► DIAGNOSE (bug path only — skip for features)
              │
              └─ reproduce → hypothesise → instrument → fix
                 Must have a feedback loop before hypothesising.
                 Must show ranked hypotheses to user before testing.
                 After fix, re-enter PLAN or go straight to BRANCH if cause is clear.
```

For new features that need a PRD first, the full lifecycle is:

```
DISCOVERY ──► PRD ──► ISSUES ──► (pick an issue → state machine above)
  │             │         │
  │             │         └─ tracer-bullet vertical slices
  │             └─ identify testing seams, get user approval, then write PRD
  └─ explore codebase, understand current state
```

## Transition guards

| Transition | Guard |
|---|---|
| TRIAGE → PLAN | Issue status = `todo` (tag `needs-human` if human-required) |
| PLAN → BRANCH | User confirms interface + behaviours to test |
| BRANCH → TDD-LOOP | On new branch from `main` |
| TDD-LOOP → COMMIT | All suites green. Pre-existing failures OK. |
| COMMIT → REVIEW | Commit exists with task ID reference |
| REVIEW → FIX | Review report saved to `.scratch/<feature>/review.md` |
| FIX → ASK-MERGE | All critical + warning findings resolved |
| ASK-MERGE → DONE | **Explicit user approval. No exceptions.** |

## TDD-LOOP detail

Vertical slicing only. Never write all tests then all implementation.

```
RED:   write ONE failing test for ONE behaviour
GREEN: minimal code to pass
REFACTOR: clean up — only on GREEN
→ repeat for next behaviour
```

Rules:
- One test at a time. Only enough code to pass current test.
- Never refactor while RED.
- Mock only at system boundaries (external APIs, time). Never mock collaborators.
- Integration-style tests through public interfaces. Tests survive internal refactors.

## Diagnose detail

Bug path only. Skip for features/enhancements.

1. **Build a feedback loop.** Failing test, curl script, replay harness — whatever gives a deterministic pass/fail signal. Do not proceed without a loop.
2. **Reproduce.** Run the loop. Confirm the failure matches what the user described.
3. **Hypothesise.** Generate 3–5 ranked, falsifiable hypotheses. Show to user before testing.
4. **Instrument.** One probe per hypothesis.
5. **Fix + regression test.** Write regression test before applying fix.
6. **Cleanup.** Remove instrumentation, delete throwaway prototypes.
