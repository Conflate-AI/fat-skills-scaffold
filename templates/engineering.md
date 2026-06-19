## Communication

{{communication_style}} mode. See `.agents/skills/caveman/SKILL.md` if caveman is set. Do not revert unless explicitly asked.

---

## Codebase Navigation — ALWAYS use AST tools before `read`

**Rule: Never `read` a source file without running `ast_outline` on it first. No exceptions.**

This applies to ALL contexts — diagnosis, exploration, implementation, review. Every time you reach for `read`, run the appropriate AST tool first:

1. **Before reading any file** → `ast_outline` — see what's in it. Many questions end here — no `read` needed.
2. **Before reading to find dependencies** → `ast_imports` + `ast_exports` — module boundary in 2 calls.
3. **Before reading to trace flow** → `ast_callees` — per-scope call list.
4. **Before reading to find a symbol** → `ast_node_at` — locate it structurally first.
5. **`read` is for detail only** — once you know WHAT exists (from AST tools), read only the specific sections you need.

AST tools parse the file in <1ms and return ~5 lines. `read` returns the entire file. Use the cheaper option first. Always.

---

## Process state machine

{{state_machine}}

---

## Shell & tool rules

1. **No redundant `cd`.** Working directory is already set. Only `cd` to operate outside the project.
2. **Review output.** `.scratch/<feature>/review.md`. Delete post-merge.

---

## Testing strategy

| Layer | Tool | When |
|---|---|---|
| Pure logic (schemas, maps, functions) | Unit tests | Pure input→output. No mocks. |
| Orchestration (services, actions, handlers) | **Don't unit-test** | Wiring — integration tests prove the logic. |

> Adjust this table to match your project's test stack.

---

## Tech stack

{{tech_stack}}

---

## Architecture

{{architecture}}

---

## CRITICAL VERSION CONTROL DISCIPLINE

### Check main state before work
If there are uncommitted files, ask the user what they want to do.

### Always branch from main for new features/issues
You must always branch from main for any new issues or features.

### Checkpoint regularly
Checkpoint your work regularly within branches for easy rollbacks and change trails.

### ALWAYS ask before merging
Never merge to main without asking the user first.

---

## Agent skills

### Issue tracker

{{issue_tracker_config}}

### Triage labels

{{triage_labels_config}}

### Domain docs

{{domain_docs_config}}
