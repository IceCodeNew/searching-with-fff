---
name: searching-with-fff
description: Use when searching files or code in the current Git worktree, locating definitions or references, finding a remembered filename, or searching multiple identifiers with fff. Not for web searches or general questions unrelated to repository files.
compatibility: Requires a connected fff stdio MCP server from dmtrKovalenko/fff and a host file-reading tool.
---

# Search with fff

Use the connected `fff` MCP server for repository search. This community skill is maintained in [IceCodeNew/searching-with-fff](https://github.com/IceCodeNew/searching-with-fff), not by fff upstream.

## Route by task

| Need | fff tool |
| --- | --- |
| Definition, reference, or one content pattern | `grep` |
| Filename or fuzzy path discovery | `find_files` |
| Multiple literal identifiers or naming variants, OR matching | `multi_grep` |

Tool names above are server-local. Select the corresponding tool exposed by the host's fff server; prefixes vary (Claude Code: `mcp__fff__grep`). Never substitute a built-in or shell grep for fff's `grep`.

Read [routing.md](routing.md) for constraints, pagination, or recovery. Match the task, not merely a keyword in prose. Start with `maxResults: 20`, narrow by known directory or file type, then read relevant code with the host's file-reading tool. Read a known path directly; no search is needed.

Example: send to fff's `multi_grep`:

```json
{"patterns":["InProgressQuote","in_progress_quote","inProgressQuote"],"constraints":"*.rs","maxResults":20}
```

## Recovery

For invalid arguments or query syntax, correct from the error and schema, then retry. If disconnected or unavailable, report the failure and use the host's MCP connection controls to enable/reconnect fff; if unavailable, request restoration. Do not silently switch to built-in search, shell grep/rg, or an ad hoc scanner. For empty results, broaden once; read any relevant known files, otherwise report no match rather than inventing a location.

Installation and host configuration belong in the [installation guide](https://github.com/IceCodeNew/searching-with-fff#installation), not in search-time recovery.
