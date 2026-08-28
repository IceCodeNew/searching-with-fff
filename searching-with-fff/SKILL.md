---
name: searching-with-fff
description: "Searches the current git-indexed directory with fff. Triggers on: file search, grep, multi-pattern grep, find_files, find a path, find a filename."
compatibility: "fff-mcp on PATH from dmtrKovalenko/fff. stdio MCP. Indexes the git worktree Amp started in."
---

# Searching with fff

Search this git worktree with the fff MCP tools. Load this skill, then call them.

fff keeps a warm index in one long-lived stdio process.

## Tools

| Job | Tool |
|-----|------|
| File contents: a definition, identifier, or pattern | `grep` |
| Path or filename | `find_files` |
| Several literal identifiers at once, including case and naming variants | `multi_grep` |

`grep` when you have a name. `find_files` when you are looking for a file. `multi_grep` when you need OR across literals.

The search target is this git worktree, including git-aware dirty and untracked annotations.

## Calls

Keep queries to one or two terms.

**Contents** (`grep`):

```json
{ "query": "InProgressQuote" }
```

Optional: `maxResults`, `cursor`, `output_mode` (`content`). `pattern` aliases `query`. Constraints go inline before the text: `*.rs InProgressQuote`, `src/ InProgressQuote`.

**Path** (`find_files`):

```json
{ "query": "mcp.json" }
```

Optional: `maxResults`, `cursor`. Multiple words narrow. Glob constraints such as `*.md !tests/` are valid.

**Literals OR** (`multi_grep`):

```json
{ "patterns": ["InProgressQuote", "in_progress_quote"], "constraints": "*.rs" }
```

Optional: `maxResults`, `cursor`, `output_mode`, `context`. Patterns are literal text.

A `cursor` in the result is the next page. Pass it back only then.

After at most two greps, read the top file.

## Install fff-mcp

Sibling `mcp.json` starts stdio server `fff` as `fff-mcp`.

Linux / macOS:

```bash
curl -fsSL https://raw.githubusercontent.com/dmtrKovalenko/fff/main/install-mcp.sh | bash
```

Homebrew:

```bash
brew install dmtrKovalenko/fff/fff-mcp
```

Windows (PowerShell):

```powershell
irm https://raw.githubusercontent.com/dmtrKovalenko/fff/main/install-mcp.ps1 | iex
```

The installer writes `~/.local/bin/fff-mcp` (Homebrew: `$(brew --prefix)/bin/fff-mcp`). Amp needs that directory on `PATH`.

If the binary is missing, run the installer above, then reload skills.

```bash
command -v fff-mcp
fff-mcp --version
```
