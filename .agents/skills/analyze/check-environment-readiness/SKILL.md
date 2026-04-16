---
name: check-environment-readiness
description: Check onboarding, `node`, Atlassian, GitHub, and Knowledge Hub before story intake. Use when the workflow needs to confirm the device was onboarded for this spec repo, the required tools are available and authenticated, then stop fast on any blocker.
---

# Check Environment Readiness

Check the minimum dependencies before story intake.

## Workflow

1. Resolve the current repo root with `git rev-parse --show-toplevel`.
2. Check onboarding by reading `~/.config/platform-spec/platform-spec.yml`.
3. Verify that `spec_repo.path` in the onboarding config exactly matches the current repo root.
4. If onboarding config is missing or the path does not match, return `result: stop`.
5. Check `node` with `node --version`.
6. Check Atlassian with `getAccessibleAtlassianResources`.
7. Check GitHub with `gh auth status`.
8. Check Knowledge Hub with `https://knowledge-hub.chotot.org/health`.
9. Return a short status summary.
10. Return `result: stop` if onboarding is `blocked` or any dependency is `blocked`, `unavailable`, or `unauthenticated`.

## Output

Return a short report only.

Use this shape:

```md
- onboarding: ok
- node: ok
- atlassian: ok
- github: ok
- knowledge-hub: ok
- result: continue
```

If one check fails:

```md
- onboarding: blocked
- node: unknown
- atlassian: unknown
- github: unknown
- knowledge-hub: unknown
- result: stop
```

If one later check fails:

```md
- onboarding: ok
- node: ok
- atlassian: blocked
- github: ok
- knowledge-hub: ok
- result: stop
```

## Guardrails

- Use the cheapest safe check.
- Check onboarding before all other dependencies.
- Use `git rev-parse --show-toplevel` to resolve the current repo root.
- Read onboarding config from `~/.config/platform-spec/platform-spec.yml`.
- Treat onboarding as `ok` only when the file exists and `spec_repo.path` exactly matches the current repo root.
- If onboarding is missing or mismatched, stop immediately and require `/onboarding` first.
- Use exactly one check per dependency.
- Do not claim auth if only config was inspected.
- Do not narrate progress between checks.
- Do not announce the next step.
- If one dependency is not `ok`, stop.
- Use `getAccessibleAtlassianResources` for Atlassian.
- Use `gh auth status` for GitHub.
- Use the `/health` endpoint for Knowledge Hub.
