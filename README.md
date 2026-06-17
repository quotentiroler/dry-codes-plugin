# Dry Codes — Claude Code & Codex plugin

Reuse before you write. This plugin gives a coding agent a skill that searches the
**Dry Codes public corpus** for existing code and docs to reuse — so it looks for an
existing implementation before writing a new one.

It bundles two things:

- **The `dry-codes` skill** — tells the agent to search Dry Codes before implementing,
  reuse matches, and check for near-duplicates.
- **The hosted MCP server** — `https://dry.codes/mcp/public`, the read-only public
  corpus (every repo the community has listed publicly). Zero config, no token.

## Install — Claude Code

```
/plugin marketplace add quotentiroler/dry-codes-plugin
/plugin install dry-codes@dry-codes-plugin
```

Then restart Claude Code (or `/reload-plugins`). Try it locally first with
`claude --plugin-dir ./dry-codes`.

## Install — Codex

```
codex plugin marketplace add quotentiroler/dry-codes-plugin
```

Then restart Codex, open the plugin directory, choose the **Dry Codes** marketplace,
and install the `dry-codes` plugin. (Codex reads `.agents/plugins/marketplace.json`
from the repo; the bundled skill goes into Codex and the `dry-codes` MCP server is
configured automatically.)

## Searching your OWN repositories

This plugin connects to the **public** corpus only. To search your own private
repositories — and get your account's `semantic_search` (Pro and up) — connect your
account's endpoint from the dashboard at **https://dry.codes** (it gives you a
per-endpoint MCP URL + an install one-liner; add it as an extra MCP server). A
first-class per-account plugin (OAuth) is tracked as a follow-up.

## Layout

This one repo is both a Claude Code marketplace and a Codex marketplace, sharing one
skill + the same public MCP URL:

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
