````skill
---
name: hugo-workflow
description: "Phase-gated workflow for Hugo site development. Use when implementing any phase of the migration plan, or when the user requests work that spans multiple steps — anything that modifies site config, content structure, theme, deployment, or DNS."
---

# Hugo Workflow — Phase-Gated Implementation

## When This Skill Applies

Whenever implementing work from the migration plan, or any non-trivial change
to the Hugo site — theme configuration, content structure, deployment pipeline,
DNS, or integration work. This includes requests phrased as "start Phase N",
"implement …", "set up …", "configure …", or "deploy …".

This skill does **NOT** apply to:
- Single-file content edits (fixing a typo in a post, updating frontmatter)
- Questions, explanations, or research tasks
- Reading or reviewing plan documents without implementing

---

## The Problem This Solves

AI agents default to pushing ahead across multiple concerns at once, losing
context on earlier steps and producing half-configured states that are hard
to debug. Phase-gated work ensures each layer is solid before the next one
builds on it.

---

## The Required Workflow

### Step 1 — Read the Plan Chunk

Read the plan document (or orchestration doc chunk — see plan-updates skill)
for the active phase. Identify:

- **Prerequisites:** Which prior phases must be complete?
- **Tasks:** What specific actions does this phase require?
- **Acceptance criteria:** How do we verify the phase is done?

If any prerequisite phase is not marked complete in the orchestration doc,
stop and inform the user. Do not proceed with a phase whose foundation is
unverified.

### Step 2 — Verify Prerequisites

Before writing any files, confirm the prerequisite state is actually true:

| If the phase depends on… | Verify by… |
|---|---|
| Hugo scaffold exists | `hugo version` succeeds, `hugo.toml` exists |
| Theme is installed | `hugo server` renders without errors |
| Build pipeline works | Last GitHub Actions run succeeded |
| Custom domain resolves | `dig jackpines.info` returns expected IPs |
| Content structure exists | `content/` directory has expected subdirectories |

If verification fails, fix the prerequisite before starting the new phase.

### Step 3 — Implement Incrementally

Work through the phase's tasks one at a time. After each meaningful change:

1. **Build-check:** Run `hugo` (or `hugo server`) to confirm the site still
   builds. A broken build must be fixed before continuing.
2. **Visual-check:** If the change affects rendering (theme config, layout
   overrides, content structure), note what the user should verify visually
   and suggest running `hugo server` if not already running.

Do not batch multiple unrelated changes before checking. The goal is to
always have a working site at each step.

### Step 4 — Verify Acceptance Criteria

After all tasks in the phase are complete, work through each acceptance
criterion from the plan:

- If the criterion is machine-verifiable (build succeeds, file exists, URL
  resolves), verify it directly.
- If the criterion requires visual inspection (theme renders correctly, mobile
  layout works), list what the user should check and wait for confirmation.

### Step 5 — Record Completion

Update the orchestration doc per the plan-updates skill. Include:
- Which tasks were completed
- Any deviations from the plan
- Any issues discovered that affect later phases

---

## Phase Dependency Table

Phases must be implemented in dependency order. This table reflects the
migration plan structure:

| Phase | Depends On | Key Output |
|---|---|---|
| 1 — Hugo Scaffold + Theme | Nothing | Working local Hugo site with Congo |
| 2 — GitHub Actions Deployment | Phase 1 | Site deploys on push to main |
| 3 — Content Structure + Nav | Phase 1 | Menu, taxonomies, directory layout |
| 4 — Content Migration | Phases 1, 3 | All posts render correctly |
| 5 — Custom Domain | Phases 2, 4 | jackpines.info serves the Hugo site |
| 6 — EmailOctopus Integration | Phase 1 | Signup form on post pages |
| 7 — Polish + Launch | Phases 2, 4, 5, 6 | Production-ready site |

Phases 2 and 3 can proceed in parallel after Phase 1.
Phase 4 follows Phase 3; Phase 6 can proceed independently after Phase 1.
Phase 5 follows Phases 2 and 4 (content verified before domain switch).
Phase 7 requires everything else.

---

## Critical Rules

- **Never skip prerequisite verification.** A phase that builds on a broken
  foundation creates compounding errors.
- **Build-check after every change.** Hugo's error messages are clear — catch
  problems immediately, not after ten more changes.
- **Do not modify the plan structure** unless asked. The plan is the user's
  artifact. Record deviations in the orchestration doc.
- **Use the todo list** to track progress through a phase's tasks — this gives
  the user visibility into where you are.
````
