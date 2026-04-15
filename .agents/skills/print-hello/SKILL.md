---
name: print-hello
description: Print a minimal hello response for smoke testing skill discovery and invocation. Use when the workflow needs a tiny no-dependency skill to confirm that shared skills from `.agents` are visible and callable.
---

# Print Hello

Return a short hello message and nothing more.

Use this as a wiring test, not as a real workflow.

## Behavior

1. Reply with `hello`.
2. If the user asks for a variant, reply with that hello variant only.
3. Do not add explanation unless the user asks for it.
