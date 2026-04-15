# validate-prd

## Purpose

Validate the PRD before any shared artifacts are generated.

This command is the first quality gate of the Platform workflow.

## Inputs

- `features/<feature>/prd.md`

## Output

- `features/<feature>/master-spec/open-questions.md`

## Exploration Mode

Before drafting detailed open questions, use the local [brainstorming skill](../.agents/skills/brainstorming/SKILL.md) as an exploration aid.

Use only the parts of that skill that help with:

- identifying the core feature slice
- surfacing competing scope interpretations
- spotting the biggest ambiguity clusters early
- deciding whether decomposition questions should come before detail questions

Guardrails for this phase:

- do not let the skill replace the Platform workflow
- do not write a separate design doc
- do not invoke `writing-plans`
- do not require section-by-section user approval for the spec
- the required output of this command remains `open-questions.md`

## What To Check

- ambiguous wording
- missing business rules
- incomplete edge cases
- inconsistent terminology
- unclear acceptance criteria
- missing design states
- missing cross-platform behavior alignment
- product or design decisions that would materially change the shared baseline

## Output Shape

The output should follow the repository `open-questions-template.md` and behave like a revision-aware question register.

Expected sections:

- register metadata
- decision readout
- blocking questions summary and details
- non-blocking questions summary and details
- resolved questions
- deferred / out-of-scope items
- active assumptions

Use stable IDs:

- `OQ-001`, `OQ-002`, ...
- `A-001`, `A-002`, ... for active assumptions that still need confirmation

Initialize and keep aligned:

- `Master Spec Package Version = r1` on first package generation
- `Package Status = Draft` on first package generation

## Rules

- check the PRD before asking a question
- use the local brainstorming skill only for early scope shaping, context exploration, and ambiguity clustering
- run the scope decomposition gate before deep clarification
- do not ask for information already stated clearly
- group questions by business rule when possible
- classify each item as `blocking` or `non-blocking`
- start with a short decision readout so a reviewer can understand the current blockers quickly
- explain the impact if the question remains unanswered
- state the current handling if the question remains unanswered during draft mode
- include the originating PRD section, Figma state, or requirement reference where possible
- if the PRD is too broad, ask decomposition questions before detail questions
- do not create open questions for raw API schema, field naming, or payload-shape uncertainty by default
- let `api-contract.md` carry draft contract assumptions unless a contract gap actually hides a product or shared-behavior decision
- keep `Proposed assumption` local to each question unless the package is actively relying on it across the shared baseline
- keep `Resolved` and `Deferred / Out of Scope` as separate logs

## Success Criteria

- major ambiguity is surfaced
- redundant questions are minimized
- oversized or mixed scopes are flagged before package generation
- the team can decide whether the feature is ready for Master Spec synthesis
