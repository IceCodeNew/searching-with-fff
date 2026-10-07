# Amp adapter

This directory is only for Amp. Other harnesses install `searching-with-fff/` and must not copy this directory or `mcp.json`.

Amp supports local-path skill installation and a sibling `mcp.json` for skill-bundled MCP. Compose the generic skill and this adapter into a temporary installation directory; do not maintain another copy of the skill text here.

First install the official `fff-mcp` binary as described in the repository [installation guide](../README.md#installation). Ensure `fff-mcp` is on Amp's PATH:

```bash
command -v fff-mcp
fff-mcp --version
```

From a checkout of this repository:

```bash
(
  set -euo pipefail
  root=$(git rev-parse --show-toplevel)
  staging=$(mktemp -d "${TMPDIR:-/tmp}/fff-amp.XXXXXXXX")
  trap 'rm -rf "$staging"' EXIT
  cp -R "$root/searching-with-fff" "$staging/searching-with-fff"
  cp "$root/amp/mcp.json" "$staging/searching-with-fff/mcp.json"
  amp skill add "$staging/searching-with-fff" --global
)
```

Omit `--global` for project installation. Amp documents project skills under `.agents/skills/` and machine-local global skills under `~/.config/agents/skills/`. The temporary directory is owned by this command and removed on exit. Review an existing skill before replacing it.

[`mcp.json`](mcp.json) starts server `fff` over stdio using `fff-mcp` on PATH, and exposes only `find_files`, `grep`, and `multi_grep`. Amp connects skill-bundled MCP during discovery and hides its exclusive tools until the skill is loaded. A same-named directly configured server takes precedence; avoid duplicate registrations. The common skill itself has no Amp-specific instructions.

Repeat the composition and local installation steps after updating the checkout. Do not use bare `amp skill add IceCodeNew/searching-with-fff` for this separated layout: it would not attach the adapter to the common skill. The documented local-path and bundled-MCP mechanisms are used here; actual Amp runtime discovery is not tested in the deployment repository's Claude Code image smoke.

References: [Amp skills](https://ampcode.com/docs/customize/skills), [global skills](https://ampcode.com/docs/customize/global-plugins-and-skills).
