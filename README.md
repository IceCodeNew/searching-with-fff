# searching-with-fff

A community skill for searching the current Git worktree with the official [dmtrKovalenko/fff](https://github.com/dmtrKovalenko/fff) MCP server. Maintained here for Amp, Claude Code, and other MCP-capable skill hosts.

This is the single source for the skill and its routing reference. [claude-code-plugins-setup](https://github.com/IceCodeNew/claude-code-plugins-setup) installs it rather than maintaining a second copy.

## Installation

### Official fff binary

Install `fff-mcp` using the [upstream instructions](https://github.com/dmtrKovalenko/fff#installation). The examples below follow upstream; URLs on `main` track updates rather than a reviewed immutable revision. Review the script before running it, or pin a reviewed commit and checksum for reproducible deployments.

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

The shell installer normally writes `~/.local/bin/fff-mcp`; Homebrew uses `$(brew --prefix)/bin/fff-mcp`. The binary must be on the host's PATH when using the bundled configuration. Check `fff-mcp --version` before connecting. Query examples are verified against v0.11.0; for other releases consult the exposed schemas.

### Harness-specific adapters

Install only `searching-with-fff/` for non-Amp harnesses. It contains the generic `SKILL.md` and `routing.md`, with no harness-specific MCP config or installation instructions.

Amp setup is isolated in [`amp/README.md`](amp/README.md) and [`amp/mcp.json`](amp/mcp.json). Follow that adapter guide to compose a temporary Amp installation from the common skill and Amp config. Do not copy `amp/` when installing for other harnesses.

### Claude Code

```bash
openskills install IceCodeNew/searching-with-fff/searching-with-fff -g -y
claude mcp add --scope user --transport stdio fff -- "$HOME/.local/bin/fff-mcp" --no-update-check
```

Use the actual absolute binary path if installed elsewhere. OpenSkills copies only the common skill directory and its reference; it does not register MCP or install the separate Amp adapter. Start Claude in the intended worktree; use `/mcp` to check the connection and `/skills` to check discovery, or invoke `/searching-with-fff` explicitly. The deployment repository documents [pinned installation and scope/context controls](https://github.com/IceCodeNew/claude-code-plugins-setup/blob/master/claude-plugins-setup.md).

### Other hosts and updates

Install the `searching-with-fff/` directory in the host's skill location, then register the official stdio server using that host's configuration. For example, Codex can register it with `codex mcp add fff -- "$HOME/.local/bin/fff-mcp" --no-update-check`; make the skill available under `~/.agents/skills/searching-with-fff`. Tool prefixes and file-reader names vary; the skill resolves them from the host rather than assuming Claude Code names.

For an OpenSkills remote installation, `openskills update searching-with-fff` refreshes it. For a pinned/local-checkout installation, repeat the checkout and install steps for the chosen revision; do not assume `openskills update` advances a pin. Install the skill only once per host and review existing content before overwriting it.

## Search contract

[`SKILL.md`](searching-with-fff/SKILL.md) contains task routing and failure recovery. [`routing.md`](searching-with-fff/routing.md) is loaded as needed for constraints, cursor handling, and scope.

- `grep` searches one content term; `find_files` discovers fuzzy filenames; `multi_grep` matches literal naming variants with OR semantics.
- Start with 20 results, constrain by known directory/type, then read relevant code. A known path needs no search.
- The stdio process indexes its startup worktree. Query arguments cannot select another repository; reconnect in the correct working directory when needed.
- Argument errors are corrected and retried. Disconnections are reported and restored through host controls, without silently falling back to built-in search or shell scanners.
- Skill discovery, MCP registration/connection, and schema loading are separate. The skill neither enables a disabled MCP server nor controls tool-schema context loading.

No hooks or CLAUDE.md/AGENTS.md injection are installed. These instructions guide model behavior; they do not enforce a runtime tool ban or guarantee automatic skill activation. This repository does not manage global Tool Search settings or install the binary automatically.

## Validation

The merged routing guidance has reasoning scenarios for Claude-style, non-Claude, and Amp-style hosts; these are not proof of host reconnection or automatic discovery. Real fff v0.11.0 stdio calls validate the documented payloads. The deployment repository owns [`build/test-fff.py`](https://github.com/IceCodeNew/claude-code-plugins-setup/blob/master/build/test-fff.py) and native amd64/arm64 image smoke, including real searches in separate Git roots and startup from a subdirectory.
