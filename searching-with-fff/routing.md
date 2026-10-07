# fff task routing

Use this reference after `searching-with-fff` applies to a repository search. General explanations, web research, and filtering command output do not need fff. These routes guide tool choice; they do not install hooks or enforce a tool ban.

## Choose one entry point

All search entries below refer to tools from the fff MCP server, not similarly named built-in tools. Resolve the host's exposed names before calling: fff's `multi_grep` may appear as `mcp__fff__multi_grep` or `fff_multi_grep`. Preserve the server's argument names and types.

| Task or next action | Entry point | Arguments |
| --- | --- | --- |
| Find a definition, usages, or a literal in source | fff `grep` | `{"query":"*.rs InProgressQuote","maxResults":20}` |
| Find a vaguely remembered filename | fff `find_files` | `{"query":"typescropt","maxResults":20}` |
| Locate files under a known directory | fff `find_files` | `{"query":"src/ config","maxResults":20}` |
| Search names or naming variants, OR matching | fff `multi_grep` | `{"patterns":["InProgressQuote","in_progress_quote","inProgressQuote"],"constraints":"*.rs","maxResults":20}` |
| Need only paths, not matching text | fff `grep` | `{"query":"src/ InProgressQuote","output_mode":"files","maxResults":20}` |
| Read a file whose path is already known | Host's file-reading tool | Read the relevant range directly |

fff matches are textual references, not a semantic call graph. Read code before identifying callers or definitions. `multi_grep` matches ANY literal pattern, not every pattern.

## Query constraints

For `grep`, prefix constraints inside `query`. For `multi_grep`, use the separate string `constraints`, not an object.

- File type: `*.rs` or `*.{ts,tsx}`.
- Directory: `src/` (include the trailing slash).
- Filename: `schema.rs` or `src/main.rs`.
- Exclude: `!test/` or `!*.spec.ts`.

A bare word is not a directory constraint. Use one specific content term; do not combine independent identifiers into one `grep` query. Keep filename queries short; multiple terms narrow results, not OR them. Prefer plain identifiers over escaped code syntax. Use the schema field `query`.

## Keep results bounded

Start with `maxResults: 20` and little or no extra `context`. Use `output_mode: "files"` when only paths matter. Once locations are available, read relevant code after at most two content searches instead of cycling through spellings. This is not a limit on pagination needed for coverage.

If a response supplies a cursor and more results are needed, call the same tool with the same query or patterns, constraints, output mode, context, and result limit, plus `cursor`. Never manufacture a cursor. A first page is not exhaustive evidence of all references.

## Availability and scope

fff maintains an index within its long-lived stdio process. It searches its startup directory, resolving Git subdirectories to their worktree root. Tools have no argument for switching repositories. Ensure the host launches fff in the intended worktree; restart/reconnect with the correct working directory if the root is wrong. Git dirty/untracked annotations describe file status, not exhaustive coverage of ignored files.

For argument errors, correct the payload and retry. If disconnected, report the condition and use available host connection controls; otherwise request restoration. Do not replace failed searches with built-in search, grep/rg, or a custom scanner. Known files can still be read without claiming repository-wide coverage.

MCP registration, skill discovery, and schema loading are separate. This skill cannot activate a disabled server or defer its definitions. Host-specific installation is in the [README](https://github.com/IceCodeNew/searching-with-fff#installation).
