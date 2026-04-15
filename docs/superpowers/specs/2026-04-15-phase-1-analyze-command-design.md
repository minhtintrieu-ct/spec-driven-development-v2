# Phase 1 Analyze Command Design

## Goal

Create the first AI-native workflow entrypoint for `ct-platform-spec-v2`.

The workflow should support Claude and Cursor through a shared canonical layer in `.agents`, while preserving client-specific config under `.claude` and `.cursor`.

## Architecture

- `.agents/commands` is the canonical command source.
- `.agents/skills` is the canonical skill source.
- `.claude/commands` and `.claude/skills` are symlinks to the canonical folders.
- `.cursor/commands` and `.cursor/skills` are symlinks to the canonical folders.
- `.claude/settings*.json`, `.cursor/settings.json`, `.cursor/mcp.json`, and root `.mcp.json` remain adapter-specific.

## Phase 1 Scope

Introduce a single command: `/analyze`.

`/analyze` is an orchestration command, not a local CLI implementation. It should route into one focused skill that performs the intake workflow in a deterministic order:

1. Check whether `node` is available.
2. Check whether Atlassian plugin, GitHub plugin, and Knowledge Hub MCP are available and authenticated.
3. Fetch the Jira issue through the Atlassian plugin.
4. Detect the PRD link, expected to be Confluence, and fetch its content.
5. Write the initial master spec package.
6. Finalize the intake run.

## Output Shape

Phase 1 keeps the artifact shape intentionally small:

- `features/<JIRA_KEY>/story-info.md`
- `features/<JIRA_KEY>/prd.md`
- `features/<JIRA_KEY>/prd-source.json`
- `features/<JIRA_KEY>/master-spec/master-spec.md`
- `features/<JIRA_KEY>/master-spec/open-questions.md`
- `features/<JIRA_KEY>/master-spec/changelog.md`
- `features/<JIRA_KEY>/run-report.md`

## Debug Model

The workflow should be easy to track and debug.

Each step should have a visible status in `run-report.md` with one of:

- `pending`
- `completed`
- `blocked`
- `failed`

Failures should be categorized as:

- environment
- authentication
- missing input
- fetch failure
- materialization failure

## Non-Goals

This phase does not introduce:

- a full repo-local CLI
- platform delta generation
- archival flow
- PR creation
- implementation handoff bundles

## Rationale

This keeps the repo AI-native and simple:

- the command layer stays declarative
- the skill layer owns execution behavior
- Claude and Cursor share the same workflow definitions
- debugging starts with one intake path before expanding the system
