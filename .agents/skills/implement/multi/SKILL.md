---
name: implement-multi
description: Use this skill to implement from the prepared delta through explicit phases. It creates an implementation plan, executes one phase at a time, and pauses after each phase for user verification before continuing.
---

# Implement Multi

Use this skill in the current working repo when `/implement` routes to `mode = multi`.

## Required Input

- `feature name`
- `platform`

Resume intent may come from user text such as:

- `continue`
- `next phase`
- `continue implement`
- similar wording that clearly asks to proceed to the next phase

Require these artifacts to already exist in the current working repo:

- `.planning/<feature>/`
- the platform delta for the same feature

Optional fallback references:

- `.planning/<feature>/repo-exploration.md`
- `.planning/<feature>/kh-search-brief.md`

## Goal

Implement through explicit user-verifiable phases instead of one uninterrupted pass.

This mode creates a plan file first, then executes one phase at a time and stops after each phase so the user can review before the next phase continues.

Persist the plan to:

- `.planning/<feature>/implementation-plan.md`

## Required Flow

1. Read the platform delta first and treat it as the primary implementation contract.
2. If `.planning/<feature>/implementation-plan.md` does not exist yet, treat this as the first run:
   - generate `.planning/<feature>/implementation-plan.md`
   - split implementation into explicit phases with:
     - scope per phase
     - likely touched files or modules
     - verification focus per phase
     - blockers or dependencies
   - start from phase 1
3. If `.planning/<feature>/implementation-plan.md` already exists, treat this as a resume run:
   - read the current plan state
   - if the user intent is `continue`, `next phase`, or similar, move to the next phase that is still `pending`
   - if all phases are already `completed`, stop and say the multi flow is already complete
   - if the current phase is `blocked`, stop and keep the flow on that phase
4. Execute only the current phase.
5. Run verification only for the current phase.
6. Update `.planning/<feature>/implementation-plan.md` with:
   - current phase
   - phase status
   - completed phases
   - next phase
7. Stop after that phase and return control to the user for review.
8. After every non-final phase, print one explicit continue instruction:
   - `If you want me to continue to the next phase, reply: continue`
9. If the last phase is complete, return a final completion summary instead of a continue instruction.

## Output

Return:

```md
- implement-mode: multi
- feature: PLMO-1328
- platform: web
- plan: .planning/PLMO-1328/implementation-plan.md
- current-phase: 1
- phase-status: completed|stopped-for-review
- next-step:
  - If you want me to continue to the next phase, reply: continue
- result: continue|stop
```

## Guardrails

- Start from the delta first.
- Always create `.planning/<feature>/implementation-plan.md` in this mode.
- Pause after each implementation phase for user review.
- Use `.planning/<feature>/repo-exploration.md` or `.planning/<feature>/kh-search-brief.md` only as fallback references, not as the main implementation source.
- If the delta is too weak to create a reliable phase plan, stop and say so explicitly instead of improvising broad rediscovery.
- Do not jump to the next phase unless the user explicitly resumes with `continue`, `next phase`, or similar wording.
- After each non-final phase, always print an explicit instruction telling the user how to continue.
