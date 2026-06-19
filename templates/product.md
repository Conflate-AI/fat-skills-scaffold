## Communication

{{communication_style}} mode.

---

## Domain Context

{{domain_context}}

---

## Process State Machine

{{state_machine}}

---

## Product Rules

1. **User-centric.** Every decision starts from the user's problem, not the business's feature list.
2. **Evidence over opinion.** Back product decisions with data: usage analytics, user research, competitive analysis.
3. **Scope ruthlessly.** Ship the smallest valuable slice first. Features expand to fill the time allotted.
4. **Document decisions.** Every "why" should be recoverable. Use PRDs and ADRs, not meeting notes.
5. **No redundant `cd`.** Working directory is already set.

---

## Codebase Navigation — ALWAYS use AST tools before `read`

**Rule: Never `read` a source file without running `ast_outline` on it first. No exceptions.**

When product work touches the codebase:
1. **Before reading any file** → `ast_outline`
2. **Before reading to find dependencies** → `ast_imports` + `ast_exports`
3. **Before reading to trace flow** → `ast_callees`
4. **`read` is for detail only** — once you know WHAT exists, read only what you need.

---

## Tech Stack

{{tech_stack}}

---

## Architecture

{{architecture}}

---

## CRITICAL VERSION CONTROL DISCIPLINE

### Check main state before work
If there are uncommitted files, ask the user what they want to do.

### Always branch from main for new features
You must always branch from main for any new issues or features.

### ALWAYS ask before merging
Never merge to main without asking the user first.

---

## Agent Skills

### Issue Tracker

{{issue_tracker_config}}

### Triage Labels

{{triage_labels_config}}

### Domain Docs

{{domain_docs_config}}
