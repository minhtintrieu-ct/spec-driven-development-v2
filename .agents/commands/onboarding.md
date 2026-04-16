# /onboarding

## Intent

Create or refresh the shared device-level config for this platform spec workflow.

## Required Input

None.

Run this command from inside the spec repo that should be registered on the current device.

## Execution

1. Run `.agents/skills/onboarding/setup-global-config/SKILL.md`.
2. If config setup succeeds, run `.agents/skills/onboarding/setup-global-skills/SKILL.md`.

If either skill returns `result: stop`, stop the flow.
