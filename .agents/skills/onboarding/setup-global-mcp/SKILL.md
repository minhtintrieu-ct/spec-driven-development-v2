---
name: setup-global-mcp
description: Publish the registered MCP servers globally for all supported clients. Use during `/onboarding` to synchronize the `.mcp.json` definitions from the specification repository into system-wide paths.
---

# Setup Global MCP

Publish the global MCP configuration for supported clients.

## Execution Contract

Use this fixed toolchain in the happy path:

- `git` CLI to resolve the current repo root
- File read/write tools to merge JSON safely

Publish the configuration from:

- `<repo-root>/.mcp.json`

To the exact global destinations (creating them if they do not exist):

- `~/.claude.json` (for Claude Code)
- `~/.cursor/mcp.json` (for Cursor)

Do not overwrite existing MCP servers in the destination outside of the ones defined in the repo's `.mcp.json`.

## Workflow

1. Resolve the current repo root with `git rev-parse --show-toplevel`.
2. Require `<repo-root>/.mcp.json` to exist.
3. Read `<repo-root>/.mcp.json` and extract the `mcpServers` object.
4. If `mcpServers` is empty or missing, return `result: continue` since there is nothing to publish.
5. Create parent directories for global configs if needed:
   - `mkdir -p ~/.cursor`
6. Parse the current Claude Code global config `~/.claude.json`. If it does not exist, initialize it in memory as `{ "mcpServers": {} }`.
7. Merge the `mcpServers` from `.mcp.json` into the global Claude config. If a key already exists, overwrite it with the config from `.mcp.json`.
8. Write the merged JSON safely back to the Claude config path.
9. Parse the current Cursor global config `~/.cursor/mcp.json`. If it does not exist, initialize it in memory as `{ "mcpServers": {} }`.
10. Merge the `mcpServers` from `.mcp.json` into the Cursor config and write it back.
11. Return a short status report.
12. Return `result: stop` if the repo root cannot be resolved or JSON writing fails.

## Command Sequence

Use this exact command sequence in the happy path to guarantee atomic, safe merges without destroying existing config properties or other MCP servers:

```bash
git rev-parse --show-toplevel

# For Claude Code (JSON merge to ~/.claude.json)
if [ -f ~/.claude.json ]; then
  jq --argjson newMcp "$(jq '.mcpServers // {}' .mcp.json)" '.mcpServers = (.mcpServers // {}) * $newMcp' ~/.claude.json > ~/.claude.json.tmp && mv ~/.claude.json.tmp ~/.claude.json
fi

# For Cursor (JSON merge to ~/.cursor/mcp.json)
mkdir -p ~/.cursor
if [ ! -f ~/.cursor/mcp.json ]; then
  echo '{"mcpServers": {}}' > ~/.cursor/mcp.json
fi
jq --argjson newMcp "$(jq '.mcpServers // {}' .mcp.json)" '.mcpServers = (.mcpServers // {}) * $newMcp' ~/.cursor/mcp.json > ~/.cursor/mcp.json.tmp && mv ~/.cursor/mcp.json.tmp ~/.cursor/mcp.json
```

## Output

If publishing succeeds:

```md
- onboarding: ok
- global-mcp: published
- claude: merged
- cursor: merged
- result: continue
```

If publishing fails:

```md
- onboarding: blocked
- global-mcp: failed
- result: stop
```

## Guardrails

- Use `git rev-parse --show-toplevel` to resolve the repo root.
- Use the exact `jq` command provided in the Command Sequence to perform the merge. Do not attempt to use `sed`, `awk`, or manual string replacement, as `~/.claude.json` is a highly complex file and any structure destruction will break the user's IDE.
- Do not destroy or remove existing unrelated MCP servers that the user has already configured globally.
- Do not make assumptions about other keys in the global configs; only interact with the `mcpServers` key.
