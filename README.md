# Dry Codes — Claude Code & Codex plugin

Reuse before you write. This plugin gives a coding agent a skill that searches
**Dry Codes** for existing code and docs to reuse — so it looks for an existing
implementation before writing a new one.

It bundles two things:

- **The `dry-codes` skill** — tells the agent to search Dry Codes before implementing,
  reuse matches, and check for near-duplicates.
- **The hosted MCP server** — `https://dry.codes/mcp`. On first use you sign in (GitHub
  OAuth) and pick which corpus to search: **one of your own endpoints**, or the
  **public corpus** (every repo listed publicly on Dry Codes). To switch later,
  reconnect and pick another.

## Install — Claude Code

```
/plugin marketplace add quotentiroler/dry-codes-plugin
/plugin install dry-codes@dry-codes-plugin
```

Then restart Claude Code (or `/reload-plugins`). Try it locally first with
`claude --plugin-dir ./dry-codes`.

## Install — Codex

Two steps: register the marketplace, then install the plugin from it.

```
codex plugin marketplace add quotentiroler/dry-codes-plugin
codex plugin add dry-codes@dry-codes
```

`marketplace add` only registers the source; `plugin add` is what installs the plugin
(`dry-codes@dry-codes` = the `dry-codes` plugin from the `dry-codes` marketplace — run
`codex plugin list` if you need the exact selector). Prefer the UI? Skip the second
command, open Codex's plugin directory, choose the **Dry Codes** marketplace, and
install `dry-codes`.

Codex reads `.agents/plugins/marketplace.json` from the repo; the bundled skill goes
into Codex and the `dry-codes` MCP server is configured automatically. Restart Codex
after installing.

## Which corpus does it search?

On the first connection Dry Codes shows a picker:

- **Your own endpoints** — your indexed repositories (and Pro features like
  `semantic_search`), grouped by account/team. Manage them at https://dry.codes.
- **Public corpus** — every repo listed publicly on Dry Codes (read-only).

The choice is bound to that connection. To search a different endpoint, reconnect and
pick another (or add a second MCP server for a specific endpoint URL from the dashboard).

## Layout

This one repo is both a Claude Code marketplace and a Codex marketplace, sharing one
skill + the same OAuth MCP URL:

```
.claude-plugin/marketplace.json     # Claude Code marketplace
.agents/plugins/marketplace.json    # Codex marketplace
dry-codes/                          # the plugin
├── .claude-plugin/plugin.json      # Claude Code manifest
├── .codex-plugin/plugin.json       # Codex manifest
├── .mcp.json                       # Claude: { mcpServers: { dry-codes: { type: http, url } } }
├── .mcp.codex.json                 # Codex:  { mcp_servers: { dry-codes: { url } } }
└── skills/dry-codes/SKILL.md       # the reuse-before-you-write skill (shared)
```

Learn more: https://dry.codes
