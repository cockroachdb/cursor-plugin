# Agent instructions for cursor-plugin

Guidance for AI coding assistants (and new contributors) working in this repo. It complements [CONTRIBUTING.md](./CONTRIBUTING.md).

## What this repo is

The CockroachDB plugin for Cursor: MCP backends (`mcp.json` + `tools.yaml`), best-practice rules (`rules/*.mdc`), and skills (`skills/`).

## Rules that prevent breakage

- **This plugin has no hooks.** Cursor does not run the PreToolUse/PostToolUse hook mechanism that the Claude Code plugin uses; safety and best-practice guidance ships as Cursor rules in `rules/`. Never describe this plugin as having "safety hooks" (the description says "safety and best-practice rules" on purpose), and do not port `hooks.json` here.
- **MCP config uses `${CURSOR_PLUGIN_ROOT}` and `${VAR:-default}`.** Cursor starts plugin servers in the user's home directory, so `tools.yaml` must be referenced as `${CURSOR_PLUGIN_ROOT}/tools.yaml`. An unset bare `${VAR}` reaches the server as literal text and `${env:VAR}` as an empty string, and Toolbox uses either one as the value (issue cockroachdb/claude-plugin#27). Declare each referenced variable under `variables` in `.cursor-plugin/plugin.json`.
- **Never edit `skills/` by hand.** Skills are synced from the [cockroachdb-skills](https://github.com/cockroachlabs/cockroachdb-skills) submodule by a weekly workflow that also regenerates the `skills` array in `.cursor-plugin/plugin.json`. Contribute skills upstream instead.
- **Never bump versions by hand.** Release Please owns `version` in `.cursor-plugin/plugin.json`, `.release-please-manifest.json`, and `CHANGELOG.md`. Conventional commits: `fix:`/`feat:` cut a release, `chore:`/`docs:` do not.
- **No counts in descriptions.** Do not write "three agents" or "N skills" anywhere; counts go stale. Name the things instead.

## Writing style

Commit messages and PR bodies in a plain human voice: conventional-commit prefixes, no AI attribution trailers, plain punctuation.
