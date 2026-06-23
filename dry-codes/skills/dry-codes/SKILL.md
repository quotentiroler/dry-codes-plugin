---
name: dry-codes
description: Reuse existing code instead of writing it twice. Use BEFORE implementing any new function, component, utility, type, endpoint, hook, or module — query the DRY.codes MCP to find an existing implementation to reuse, and check for near-duplicates before finishing. Also a searchable knowledge base over code AND docs (READMEs, ADRs, conventions) — search it for prior decisions, naming, and patterns before writing so you stay consistent. Chain several tool calls (search, then read, then compare, then check) rather than a single lookup. Triggers when about to write new code, "implement X", "add a helper/util", "create a component", refactoring toward reuse, or whenever following DRY (Don't Repeat Yourself).
interface:
  display_name: "DRY.codes"
  short_description: "Reuse before you write — search indexed code & docs."
  icon_small: "./assets/icon.png"
  icon_large: "./assets/logo.svg"
  brand_color: "#3fb950"
---

# DRY.codes — reuse before you write

You have access to the **DRY.codes** MCP server, a searchable knowledge base over
indexed repositories. It indexes both **code** and **docs** (READMEs, ADRs, guides)
and finds them by text AND by meaning. Your job is to *reuse what already exists*
(code, conventions, and decisions) instead of regenerating it, and to keep the
codebase free of duplicates and inconsistencies.

DRY stands for Don't Repeat Yourself. The opposing view is WET (Write Everything Twice).
The counter-balance is AHA (Avoid Hasty Abstractions): abstract only on real, repeated
evidence, never on a single guess.

On first use this plugin signs you in (GitHub OAuth) and lets you pick which corpus to
search: one of **your own** DRY.codes MCP endpoints, or the **public corpus** (public
repos shared on DRY.codes). To switch later, reconnect and pick another. Manage your
endpoints at https://dry.codes.

## Chain your calls — one lookup is rarely enough

A single search tells you little. The value comes from **chaining** tools: cast a wide
net, READ the strongest hits, then CONFIRM with a second tool before you act. Treat each
result as a lead for the next call, not a final answer. Typical chains:

- **Before writing** `semantic_search` or `dry_wand` to find candidates → READ the top
  files → if two look close, `compare_files` to see how much they overlap → reuse the winner.
- **Before deciding** `search_code` with `scope: docs` and `find_file` for an existing ADR,
  README, or convention → follow it instead of re-deciding.
- **Before consolidating** `list_dry_issues` + `list_semantic_dupes` (both with
  `cross_repo_only`) → `compare_files` on the worst pairs to verify → propose ONE shared home.
- **Before finishing** `list_dry_issues` and `list_semantic_dupes` again to catch any
  near-duplicate you just introduced.

If the first call is thin, widen it (lower `threshold`, raise `top_k`, try `scope: docs`,
switch text↔meaning) before concluding nothing exists. Two or three focused calls beat one.

## The tools

Availability depends on your plan and endpoint; use whatever is exposed.

**Find what already exists (search):**
- `semantic_search` (Pro and up) — finds code by MEANING, so it matches an existing
  implementation even when it is named or written differently. Best first call when you
  know the intent but not the wording.
- `dry_wand` — paste the code you are ABOUT to write; returns existing implementations
  ranked by similarity. `threshold` tunes strictness; `enrich`/`cross_lang` also match
  renamed or cross-language variants.
- `search_code` — token/keyword search. Wrap a term in quotes to require it; wrap several
  in quotes for a phrase (adjacency). `fuzzy_level` 1=exact, 2=case+compound split, 3=prefix.
- `find_file` — locate a file by name or path fragment (`path: true` matches the whole path).
- `list_files` — enumerate indexed files (filter by owner/repo/scope/ext) to learn a repo's
  layout before diving in.

**Find what repeats (duplication & consolidation):**
- `list_dry_issues` — precomputed TEXTUAL duplicate pairs (Jaccard), ranked by overlap
  (🔴 large, 🟡 moderate, 🟢 small). Use `cross_repo_only` / `intra_repo_only`, `threshold`.
- `list_semantic_dupes` (Pro and up) — precomputed MEANING duplicate pairs: files that do the
  SAME thing while reading differently, which the textual list cannot see. Same filters.
- `compare_files` — pairwise overlap of the SPECIFIC files you name (full index paths like
  `acme/proxy-smart/src/auth.ts`). Use it to confirm a suspected duplicate before acting.
- `list_unique_files` — the most ORIGINAL files (inverse of `list_dry_issues`). Good
  canonical implementations to reuse, and good seeds when extracting a shared module.

**Live analysis (uses GitHub, when the App is connected):**
- `preview_audit` — dry-run DRY analysis of a GitHub repo/branch that is NOT yet indexed:
  internal duplicates plus matches against your indexed corpus. Nothing is persisted.

Scopes: pass `scope: docs` to search prose/ADRs, `scope: code` for source, `scope: data`
(Pro and up) for JSON/XML. Most tools accept `owner` and `ext` filters too.

## Workflow

Before writing any non-trivial new code:

1. **Search first, by meaning and by text.** `semantic_search` and/or `dry_wand` for an
   existing implementation; `search_code` with `scope: docs` for an existing convention.
2. **Read and reuse the match.** If a close result exists, READ it and reuse it (import,
   call, extend, or adapt) instead of writing a parallel version. Cite the file you reused.
3. **Stay consistent.** Match the naming, structure, and patterns you found, so you do not
   introduce a second style for the same thing.
4. **Check before you finish.** Run `list_dry_issues` (and `list_semantic_dupes` on Pro) to
   confirm you did not add a near-duplicate.

## Guidelines

- Reuse beats regenerate: it spends far fewer tokens and keeps the codebase DRY.
- Prefer extending an existing utility over creating a second one that does the same job.
- Only write new code when nothing suitable exists, then put it in ONE place so the next
  agent can reuse it too.
- Search the docs before deciding: an ADR, README, or convention may already settle it.
- Do not stop at the first search. Chain a confirming call (`compare_files`, a docs search,
  a duplication check) before you conclude.

## When duplication spans repositories

DRY.codes sees duplication ACROSS repos, not just within one. That is the signal for
*abstraction* and *consolidation*, not just local reuse. Mind AHA: abstract only when the
evidence is real and repeated.

- Run `list_dry_issues` (and `list_semantic_dupes`) with `cross_repo_only` to find logic
  copied across multiple repositories, then `compare_files` to confirm the strongest pairs.
- When the same thing is duplicated in several repos, **propose extracting it into one
  shared place** (a shared package, library, or repository each consumer depends on) instead
  of editing the copies in place.
- Name every repo that would benefit, using the duplication results as evidence.
- Treat this as a **recommendation**: surface it, explain the trade-off, and let the user
  decide. Do NOT create a new repository or restructure projects without their go-ahead.

## Examples

- "Add a function to format dates" → `semantic_search`/`dry_wand` for a date formatter →
  read the hit → reuse it if found.
- "Create a CSV parser" → `search_code` for a parser, then `compare_files` on the two
  closest before writing one.
- "Follow our API conventions" → `search_code` with `scope: docs` / `find_file` for the ADR
  before coding.
- "This util appears in three of our repos" → `list_dry_issues` + `list_semantic_dupes`
  `cross_repo_only` → `compare_files` to confirm → propose a shared package.
- Wrapping up a change → `list_dry_issues` (and `list_semantic_dupes`) to catch duplicates
  you may have introduced.

Learn more: https://dry.codes