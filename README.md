# CockroachDB Plugin for Cursor

[![Release Please](https://github.com/cockroachdb/cursor-plugin/actions/workflows/release-please.yml/badge.svg)](https://github.com/cockroachdb/cursor-plugin/actions/workflows/release-please.yml)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

The CockroachDB plugin for [Cursor](https://cursor.com/) gives your AI coding agent direct access to CockroachDB databases — explore schemas, write optimized SQL, debug queries, and manage distributed clusters.

## Installation

In Cursor, run:

```
/add-plugin cockroachdb
```

Or install from the [Cursor Marketplace](https://cursor.com/marketplace).

### Prerequisites

This plugin connects to CockroachDB via MCP (Model Context Protocol). Choose the backend that fits your setup — see [MCP Backends](#mcp-backends) below.

For the **MCP Toolbox** backend (available now), install [MCP Toolbox for Databases](https://github.com/googleapis/genai-toolbox) (v0.27.0+):

```bash
brew install mcp-toolbox
```

## Configuration

Set environment variables for your CockroachDB connection:

```bash
export COCKROACHDB_HOST="your-cluster-host"
export COCKROACHDB_PORT="26257"
export COCKROACHDB_USER="your-user"
export COCKROACHDB_PASSWORD="your-password"
export COCKROACHDB_DATABASE="your-database"
```

For CockroachDB Cloud, find connection details in the [Cloud Console](https://cockroachlabs.cloud/).

## What's Included

### MCP Backends

| Backend                    | Status         | Transport       | Use Case                                                                                                                          |
|----------------------------|----------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `cockroachdb-toolbox`      | ✅ Available    | stdio           | Any CockroachDB cluster via [MCP Toolbox](https://github.com/googleapis/genai-toolbox)                                            |
| `cockroachdb-toolbox-http` | ✅ Available    | Streamable HTTP | Same as above, remote/multi-user via HTTP                                                                                         |
| `ccloud`                   | 🔜 Coming soon | stdio           | Cluster lifecycle, backups, DR, networking via [ccloud CLI](https://www.cockroachlabs.com/docs/cockroachcloud/ccloud-get-started) |
| `cockroachdb-cloud`        | 🔜 Coming soon | HTTP            | CockroachDB Cloud MCP Server (OAuth/API key)                                                                                      |

### Tools

| Tool                       | Description                                      |
|----------------------------|--------------------------------------------------|
| `cockroachdb-execute-sql`  | Execute SQL statements (SELECT, DDL, DML)        |
| `cockroachdb-list-schemas` | List all schemas in the database                 |
| `cockroachdb-list-tables`  | List tables with columns, types, and constraints |

### Skills

22 skills from [cockroachdb-skills](https://github.com/cockroachlabs/cockroachdb-skills) across 10 operational domains:

| Domain                          | Skills | Examples                                                        |
|---------------------------------|--------|-----------------------------------------------------------------|
| **Query & Schema Design**       | 1      | cockroachdb-sql                                                 |
| **Observability & Diagnostics** | 7      | profiling-statement-fingerprints, triaging-live-sql-activity    |
| **Security & Governance**       | 11     | auditing-cloud-cluster-security, hardening-user-privileges      |
| **Onboarding & Migrations**     | 3      | molt-fetch, molt-verify, molt-replicator                        |
| **Application Development**     | —      | (planned)                                                       |
| **Performance & Scaling**       | —      | (planned)                                                       |
| **Operations & Lifecycle**      | —      | (planned)                                                       |
| **Cost & Usage Management**     | —      | (planned)                                                       |
| **Integrations & Ecosystem**    | —      | (planned)                                                       |
| **Resilience & Disaster Recovery** | —   | (planned)                                                       |

Skills are sourced from the [`cockroachdb-skills`](https://github.com/cockroachlabs/cockroachdb-skills) submodule via symlink — a single source of truth shared across CockroachDB agent integrations.

### Rules

| Rule                     | Applies To                                                        |
|--------------------------|-------------------------------------------------------------------|
| **SQL Patterns**         | UUID primary keys, indexing, transactions, CockroachDB SQL idioms |
| **Application Patterns** | ORM configuration, retry logic, schema design, error handling     |

## Development

Clone the repository:

```bash
git clone https://github.com/cockroachdb/cursor-plugin.git
cd cursor-plugin
```

### Project Structure

```
.cursor-plugin/plugin.json   # Plugin manifest
skills -> submodules/...     # Symlink to cockroachdb-skills (22 skills)
rules/                        # 2 rule sets (.mdc files)
mcp.json                      # MCP server definitions
tools.yaml                    # Toolbox source & tool configuration
submodules/cockroachdb-skills # Shared skills submodule
assets/logo.svg               # Plugin logo
```

## Releasing

This repo uses [Release Please](https://github.com/googleapis/release-please) for automated releases.

1. Use [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`) on `main`
2. Release Please opens a Release PR with version bump and changelog
3. Merge the Release PR to publish

## Links

- [CockroachDB Documentation](https://www.cockroachlabs.com/docs/)
- [CockroachDB Cloud Console](https://cockroachlabs.cloud/)
- [ccloud CLI](https://www.cockroachlabs.com/docs/cockroachcloud/ccloud-get-started)
- [MCP Toolbox for Databases](https://github.com/googleapis/genai-toolbox)
- [Report Issues](https://github.com/cockroachdb/cursor-plugin/issues)

## License

[Apache-2.0](LICENSE)
