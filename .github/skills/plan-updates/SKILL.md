````skill
---
name: plan-updates
description: "Progress tracking via plan decomposition and orchestration docs. Use when starting work from a plan document, after completing implementation phases, or when the user asks to update project status."
---

# Plan Updates — Context-Aware Progress Tracking

## When This Skill Applies

- When starting work from a plan document for the first time
- After completing a phase of implementation (hugo-workflow Step 5)
- When the user asks to update project status or review progress
- When resuming work after a break or new session

---

## The Problem This Solves

Large plan documents risk context loss during implementation. An agent working
from a 500-line plan will progressively lose fidelity on details from early
sections as it works through later ones. By decomposing the plan into focused
chunks and tracking progress in a separate orchestration doc, each unit of work
stays within reliable context window limits.

---

## Plan Decomposition Workflow

### Step 1 — Read the Plan Document

Read the full plan document. This is the user's source of truth — do not modify
it during this step.

### Step 2 — Evaluate Context Risk

Assess whether the plan can be held in working context reliably. Indicators of
risk:

- **Size:** Plan exceeds ~200 lines of substantive content (not counting tables
  or formatting)
- **Breadth:** Plan covers more than 3 distinct phases with different concerns
  (e.g., infrastructure, content, integrations, DNS)
- **Detail density:** Phases include inline code blocks, file listings, or
  multi-step procedures that must be followed precisely
- **Cross-references:** Later phases reference specific details from earlier
  phases (version numbers, file paths, config keys)

If the plan is short, focused, and self-contained — skip decomposition and
work from it directly. Use the orchestration doc for tracking only.

### Step 3 — Decompose into Phase Documents

If there is context risk, split the plan into one document per phase (or per
logical group of related phases). Each phase document should:

1. Be self-contained — include all context needed to implement that phase
2. Include the phase's tasks, constraints, and acceptance criteria
3. Include relevant cross-references from the master plan (don't rely on
   the reader having read other phase docs)
4. Be small enough to hold in context alongside the working files

Store phase documents in `.copilot/plans/`:
```
.copilot/plans/
  orchestration.md       ← progress tracking and phase ordering
  phase-1-scaffold.md    ← self-contained phase doc
  phase-2-deploy.md
  phase-3-domain.md
  ...
```

### Step 4 — Create the Orchestration Document

Create `.copilot/plans/orchestration.md` with:

1. **Phase index** — ordered list of phases with dependency notes
2. **Status tracker** — current state of each phase
3. **Decisions log** — choices made during implementation that affect later phases
4. **Deviations log** — where implementation diverged from the plan and why

Template:

```markdown
# Orchestration — [Project Name]

Source plan: [path to original plan document]

## Phase Index

| # | Phase | Depends On | Status | Phase Doc |
|---|-------|-----------|--------|-----------|
| 1 | Hugo Scaffold + Theme | — | not-started | phase-1-scaffold.md |
| 2 | GitHub Actions Deploy | Phase 1 | not-started | phase-2-deploy.md |
| ...

## Decisions

| Date | Phase | Decision | Rationale |
|------|-------|----------|-----------|
| | | | |

## Deviations

| Date | Phase | Plan Said | Actually Did | Why |
|------|-------|-----------|-------------|-----|
| | | | | |
```

### Step 5 — Await Approval

Present the decomposition to the user:
- List the phase documents created
- Show the orchestration doc structure
- Highlight any judgment calls made during decomposition
  (e.g., "I merged Phases 4 and 5 into one doc because they share content
  structure concerns")

Do not begin implementation until the user approves the decomposition.

---

## Ongoing Tracking

### After Completing a Phase

1. Update the phase's status in the orchestration doc (`not-started` →
   `in-progress` → `complete`)
2. Record any decisions made during implementation
3. Record any deviations from the plan
4. Note any discoveries that affect later phases

### Status Values

| Status | Meaning |
|---|---|
| `not-started` | No work begun |
| `in-progress` | Currently being implemented |
| `blocked` | Cannot proceed — dependency or issue |
| `complete` | All tasks done, acceptance criteria verified |
| `skipped` | Intentionally not implemented (with reason) |

### When Resuming Work

At the start of a new session or after a break:
1. Read the orchestration doc first — it has the current state
2. Read only the phase doc for the active phase
3. Do not re-read the full plan unless the orchestration doc is unclear

This is the primary benefit of decomposition: resumption requires reading
~50 lines of orchestration + one phase doc, not the entire plan.

---

## Artifacts Location

All tracking artifacts live in `.copilot/plans/`. This directory should be
git-ignored if the tracking is agent-only, or committed if the user wants
progress tracked in version control.

The original plan document is never modified by this process. It remains the
user's source of truth. The orchestration doc tracks execution against it.

---

## When NOT to Update

- Do not update status for work still in progress (use `in-progress` instead)
- Do not mark phases complete speculatively
- Do not modify the original plan document unless explicitly asked
- Do not update tracking for trivial changes that don't correspond to plan phases
````
