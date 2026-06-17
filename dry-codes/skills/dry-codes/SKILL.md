---
name: dry-codes
description: Reuse existing code instead of writing it twice. Use BEFORE implementing any new function, component, utility, type, endpoint, hook, or module — query the Dry Codes MCP to find an existing implementation to reuse, and check for near-duplicates before finishing. Also a searchable knowledge base over code AND docs (READMEs, ADRs, conventions) — search it for prior decisions and patterns before writing. Triggers when about to write new code, "implement X", "add a helper/util", "create a component", refactoring toward reuse, or whenever following DRY (Don't Repeat Yourself).
---

# Dry Codes — reuse before you write

You have access to the **Dry Codes** MCP server — a searchable knowledge base over
indexed repositories. It indexes both **code** and **docs** (READMEs, ADRs, guides)
and finds them by text and by meaning. Your job is to *reuse what already exists* —
code, conventions, and decisions — rather than regenerate it.

This plugin connects to the **public corpus** (`https://dry.codes/mcp/public`): every
repository the community has listed publicly on Dry Codes. To search your OWN private
repositories, connect your account's endpoint instead (see https://dry.codes).

Use whatever Dry Codes tools your endpoint exposes; `semantic_search` appears on
Pro and up, and `search_code` / `dry_wand` cover the rest on every plan.

## Workflow

Before writing any non-trivial new code:

1. **Search first.** When you are about to implement something, call the Dry Codes
   tools before writing:
   - `dry_wand` / `search_code` — find an existing implementation of what you need.
   - `semantic_search` (Pro and up) — find code that does the same thing even when
     it is written differently.
2. **Reuse the match.** If a close result exists, READ it and **reuse** it — import,
   call, extend, or adapt it — instead of writing a parallel version.
3. **Check before you finish.** Run `list_dry_issues` to confirm you did not add a
   near-duplicate of something already in the codebase.

## Guidelines

- Reuse beats regenerate: it spends far fewer tokens and keeps the codebase DRY.
- Prefer extending an existing utility over creating a second one that does the same job.
- Only write new code when nothing suitable exists — then put it in one place so the
  next agent can reuse it too.
- When you reuse something, say so: cite the file you reused.
- Search the docs too: before implementing, look in the indexed docs (use
  `scope: docs`) for an existing convention, ADR, or pattern — don't re-decide
  something already written down.

## When duplication spans repositories

Dry Codes sees duplication ACROSS repos, not just within one — that is the signal
for *abstraction* and *consolidation*, not just reuse.

- Run `list_dry_issues` with `cross_repo_only` to find logic copied across multiple
  repositories.
- When the same thing is duplicated in several repos, **propose extracting it into
  one shared place** — a shared package, library, or repository that each consumer
  depends on — instead of editing the copies in place.
- Name every repo that would benefit, using the duplication results as evidence.
- Treat this as a **recommendation**: surface it, explain the trade-off, and let the
  user decide. Do NOT create a new repository or restructure projects without their
  go-ahead.

## Examples

- "Add a function to format dates" → `dry_wand` for a date formatter first; reuse it if found.
- "This util appears in three of our repos" → `list_dry_issues` cross_repo_only, then propose a shared package.
- "Create a CSV parser" → search Dry Codes for an existing parser before writing one.
- Wrapping up a change → `list_dry_issues` to catch duplicates you may have introduced.

Learn more: https://dry.codes
