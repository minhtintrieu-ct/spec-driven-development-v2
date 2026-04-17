# MCP Setup Guide

This guide explains how to connect the Chotot Knowledge Hub MCP in:

- Copilot in VS Code
- Cursor
- Claude

It covers both setup modes:

- Remote MCP: connect to the hosted Knowledge Hub endpoint
- Local MCP: run the MCP server from a local `ct-knowledge-hub` checkout

## Prerequisites

- Node.js 20 or newer
- `npx` available in your shell
- Access to the Knowledge Hub endpoint or the local `ct-knowledge-hub` repository

Hosted MCP endpoint:

```text
https://knowledge-hub.chotot.org/mcp
```

## 1. Set Up the Remote MCP

Use the hosted endpoint when you do not need to run the server locally.

### Claude

Node.js 20+ is required because the config uses `npx mcp-remote`.

Common config file locations:

- Claude Desktop: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Claude Code: `~/.claude/settings.json`

Add this server entry:

```json
{
  "mcpServers": {
    "chotot-knowledge-hub": {
      "command": "npx",
      "args": ["mcp-remote", "https://knowledge-hub.chotot.org/mcp"]
    }
  }
}
```

If your config file already contains other MCP servers, merge this entry into the existing `mcpServers` object.

### Cursor

You can add the remote MCP through the UI or through the workspace config file.

UI path:

```text
Settings -> Tools & Integrations -> MCP Tools -> New MCP Server
```

Workspace config example in `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "chotot-knowledge-hub": {
      "command": "npx",
      "args": ["mcp-remote", "https://knowledge-hub.chotot.org/mcp"]
    }
  }
}
```

If you want the server name to match other clients, you can rename the key to `chotot-knowledge-hub`.

### Copilot in VS Code

Add the server from the MCP UI:

```text
Chat Customizations -> MCP Server -> + -> HTTP
```

Use this endpoint:

```text
https://knowledge-hub.chotot.org/mcp
```

## 2. Run the MCP Locally

Use local mode when you need to run the Knowledge Hub MCP from your machine.

### Step 1: Clone and Open `ct-knowledge-hub`

Clone the repository if needed, then open it in your terminal.

### Step 2: Bootstrap the Local Server

Run:

```bash
./scripts/bootstrap-mcp.sh
```

The bootstrap script will:

1. Check your Node.js version
2. Install dependencies with `npm install`
3. Build TypeScript output into `dist/` with `npm run build`
4. Print a ready-to-paste `.env` snippet and client config examples with local paths filled in

### Step 3: Create `.env`

Create a `.env` file in the `ct-knowledge-hub` repository and paste in the values printed by the bootstrap script.

Then export the variables into your current shell:

```bash
export $(xargs < .env)
```

### Step 4: Start the MCP Server

Run:

```bash
npm run start
```

The local MCP endpoint is expected to be:

```text
http://localhost:8001/mcp
```

Keep this terminal running while your client is connected to the local server.

## 3. Point Each Client to the Local MCP

### Claude

Update the MCP server entry to use the local endpoint through `mcp-remote`:

```json
{
  "mcpServers": {
    "chotot-knowledge-hub": {
      "command": "npx",
      "args": ["mcp-remote", "http://localhost:8001/mcp", "--allow-http"],
      "env": {}
    }
  }
}
```

### Cursor

If you are using `.cursor/mcp.json`, point the server to the local endpoint:

```json
{
  "mcpServers": {
     "chotot-knowledge-hub": {
      "command": "npx",
      "args": ["mcp-remote", "http://localhost:8001/mcp", "--allow-http"],
      "env": {}
    }
  }
}
```

### Copilot in VS Code

Open the MCP server settings again and add a new HTTP server with:

```text
http://localhost:8001/mcp
```