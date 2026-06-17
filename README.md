# Dry Codes — Claude Code plugin

Reuse before you write. This plugin gives any Claude Code session a skill that
searches the **Dry Codes public corpus** for existing code and docs to reuse — so an
agent looks for an existing implementation before writing a new one.

It bundles two things:

- **The `dry-codes` skill** — tells the agent to search Dry Codes before implementing,
  reuse matches, and check for near-duplicates.
- **The hosted MCP server** — `https://dry.codes/mcp/public`, the read-only public
  corpus (every repo the community has listed publicly). Zero config, no token.

## Install

```
/plugin marketplace add quotentiroler/dry-codes-plugin
/plugin install dry-codes@dry-codes-plugin
```

Then restart Claude Code (or `/reload-plugins`). The `dry-codes` skill and the
`dry-codes` MCP server are now available; the skill triggers whenever you are about to
write new code.

### Try it locally first

```
claude --plugin-dir ./dry-codes
```

## Searching your OWN repositories

This plugin connects to the **public** corpus only. To search your own private
repositories — and get your account's `semantic_search` (Pro and up) — connect your
account's endpoint from the dashboard at **https://dry.codes** (it gives you a
per-endpoint MCP URL + an install one-liner). A first-class per-account plugin with
OAuth is tracked as a follow-up.

## Layout

```
.claude-plugin/marketplace.json   # this repo is its own marketplace
dry-codes/
├── .claude-plugin/plugin.json    # plugin manifest
├── .mcp.json                     # → https://dry.codes/mcp/public (type: http)
└── skills/dry-codes/SKILL.md     # the reuse-before-you-write skill
```

Learn more: https://dry.codes
