# Contributing to CockroachDB Plugin for Cursor

Thank you for your interest in contributing! This guide covers the plugin itself — rules, MCP configuration, and tooling. For contributing **skills**, see the [cockroachdb-skills CONTRIBUTING.md](https://github.com/cockroachlabs/cockroachdb-skills/blob/main/CONTRIBUTING.md) instead; skills are maintained upstream and synced here automatically.

## Getting Started

### Prerequisites

- [Cursor](https://cursor.com/) installed
- [MCP Toolbox for Databases](https://github.com/googleapis/mcp-toolbox) v1.0.0+ (`brew install mcp-toolbox`)
- A running CockroachDB instance (local or cloud)

### Setup

```bash
git clone --recurse-submodules https://github.com/cockroachdb/cursor-plugin.git
cd cursor-plugin
```

Set your connection environment variables:

```bash
export COCKROACHDB_HOST=localhost
export COCKROACHDB_PORT=26257
export COCKROACHDB_USER=root
export COCKROACHDB_PASSWORD=
export COCKROACHDB_DATABASE=defaultdb
export COCKROACHDB_SSLMODE=disable
```

## Project Structure

```
.cursor-plugin/
  plugin.json              # Plugin manifest (version managed by Release Please)
mcp.json                   # MCP server definitions (Toolbox stdio, Cloud)
tools.yaml                 # MCP Toolbox source and tool definitions
rules/                     # Cursor rule files (.mdc format)
skills/                    # Copied from cockroachdb-skills submodule (do not edit directly)
submodules/
  cockroachdb-skills/      # Upstream skills submodule
assets/
  logo.svg                 # Plugin logo
```

## What You Can Contribute

| Area | Examples |
|------|----------|
| **Rules** | New `.mdc` rule files, improved pattern guidance |
| **MCP config** | New backend integrations, connection improvements |
| **Tools** | New tool definitions in `tools.yaml` |
| **Bug fixes** | Config issues, env var handling, manifest fixes |
| **Documentation** | README improvements, inline comments |

### What belongs elsewhere

- **New skills** → [cockroachdb-skills](https://github.com/cockroachlabs/cockroachdb-skills) repo
- **Toolbox bugs** → [MCP Toolbox](https://github.com/googleapis/mcp-toolbox) repo
- **Cursor bugs** → [Cursor](https://forum.cursor.com/) forum

## Development Workflow

1. **Fork** the repository and create a feature branch:
   ```bash
   git checkout -b fix/describe-your-change
   ```

2. **Make your changes** — match the existing code style and conventions.

3. **Test locally** — install the plugin in Cursor and verify your change works.

4. **Commit** using [Conventional Commits](https://www.conventionalcommits.org/):
   ```bash
   git commit -m "fix: correct env var reference in mcp.json"
   git commit -m "feat: add new rule for changefeed patterns"
   git commit -m "docs: clarify Cloud MCP setup in README"
   ```

5. **Open a Pull Request** against `main`.

## Commit Conventions

This repo uses [Release Please](https://github.com/googleapis/release-please) for automated versioning and changelogs. Your commit prefix determines what happens:

| Prefix | Effect | Example |
|--------|--------|---------|
| `fix:` | Patch release (0.1.x) | `fix: correct sslmode default in mcp.json` |
| `feat:` | Minor release (0.x.0) | `feat: add changefeed rule set` |
| `docs:` | No release | `docs: update README with new backend` |
| `chore:` | No release | `chore: update submodule reference` |

**Important:**
- Never bump the version in `plugin.json` or `.release-please-manifest.json` manually — Release Please owns these files.
- Use `fix:` or `feat:` only for changes that should appear in the changelog and trigger a release.

## Guidelines

### Rules

- Rule files live in `rules/` using Cursor's `.mdc` format.
- Each rule should focus on a specific domain (SQL patterns, app patterns, etc.).
- Reference MCP tools by their full names (e.g., `cockroachdb-execute-sql`, `list_clusters`).
- Include "Available MCP Tools" sections so the agent knows what tools it can use.

### MCP Configuration

- `mcp.json` (no dot prefix) defines MCP server backends for Cursor. Don't add a `.mcp.json` as well: Cursor reads it before `mcp.json`.
- Reference bundled files through `${CURSOR_PLUGIN_ROOT}`, as in `${CURSOR_PLUGIN_ROOT}/tools.yaml`. Cursor starts plugin MCP servers in the user's home directory, so a relative path never reaches the plugin folder.
- Reference variables as `${VAR:-default}`, never as a bare `${VAR}`: Cursor passes an unset bare reference through as the literal text `${VAR}` (issue cockroachdb/claude-plugin#27). Don't use `${env:VAR}` either, because it resolves to an empty string, and Toolbox uses an empty value as-is instead of its default. Give each Toolbox connection setting a non-empty default that matches `tools.yaml`; only `COCKROACHDB_PASSWORD` and the optional `mcp-cluster-id` header default to empty.
- Declare every variable that `mcp.json` references under `variables` in `.cursor-plugin/plugin.json`, so users can set it under **Plugins** → **Configure**.
- The `tools.yaml` file uses Toolbox v1.1.0 map-based format with `${VAR:default}` syntax for defaults.

### Skills

Skills are synced from the upstream [cockroachdb-skills](https://github.com/cockroachlabs/cockroachdb-skills) submodule by a [weekly CI workflow](.github/workflows/update-skills.yml). Do not edit files in `skills/` directly — changes will be overwritten. Contribute new skills to the upstream repo instead.

## Reporting Issues

- Use [GitHub Issues](https://github.com/cockroachdb/cursor-plugin/issues) for bugs and feature requests.
- Include your plugin version (`plugin.json` → `version`), Cursor version, and OS.
- For connection issues, include the MCP backend you're using (Toolbox, Cloud MCP, or ccloud).

## License

By contributing, you agree that your contributions will be licensed under the [Apache-2.0 License](LICENSE).
