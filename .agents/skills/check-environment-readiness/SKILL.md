---
name: check-environment-readiness
description: Check `node`, Atlassian, GitHub, and Knowledge Hub before story intake. Use when the workflow needs to confirm they are available and authenticated, then stop fast on any blocker.
---

# Check Environment Readiness

Check the minimum dependencies before story intake.

## Workflow

1. Check `node` with `node --version`.
2. Check Atlassian with `getAccessibleAtlassianResources`.
3. Check GitHub with `gh auth status`.
4. Check Knowledge Hub with `https://knowledge-hub.chotot.org/health`.
5. Return a short status summary.
6. Return `result: stop` if any dependency is `blocked`, `unavailable`, or `unauthenticated`.

## Output

Return a short report only.

Use this shape:

```md
- node: ok
- atlassian: ok
- github: ok
- knowledge-hub: ok
- result: continue
```

If one check fails:

```md
- node: ok
- atlassian: blocked
- github: ok
- knowledge-hub: ok
- result: stop
```

## Guardrails

- Use the cheapest safe check.
- Use exactly one check per dependency.
- Do not claim auth if only config was inspected.
- Do not narrate progress between checks.
- Do not announce the next step.
- If one dependency is not `ok`, stop.
- Use `getAccessibleAtlassianResources` for Atlassian.
- Use `gh auth status` for GitHub.
- Use the `/health` endpoint for Knowledge Hub.
