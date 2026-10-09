# CockroachDB Plugin for Cursor

[![Release Please](https://github.com/cockroachdb/cursor-plugin/actions/workflows/release-please.yml/badge.svg)](https://github.com/cockroachdb/cursor-plugin/actions/workflows/release-please.yml)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

Connect [Cursor](https://cursor.com/) directly to your CockroachDB clusters for hands-on database work — explore schemas, write optimized SQL, debug queries, and manage distributed database clusters. This plugin provides tools across MCP backends (self-hosted MCP Toolbox and managed CockroachDB Cloud MCP Server), specialized agents (DBA, Developer, Operator), skills across operational domains, and built-in safety and best-practice rules.

## Installation

Install from the [Cursor Marketplace](https://cursor.com/marketplace/cockroachdb), or in Cursor run:

```
/add-plugin cockroachdb
```

### Prerequisites

The skills and rules work without any setup. Each of the plugin's MCP servers needs its own:

- **`cockroachdb-toolbox`** (any self-hosted or Cloud cluster) needs [MCP Toolbox for Databases](https://github.com/googleapis/mcp-toolbox) v1.0.0 or later on your `PATH`, and a running CockroachDB cluster it can reach. Toolbox connects when the server starts, so without a reachable cluster this server fails to start. If you don't run a cluster, [turn the server off](#turn-off-a-server-you-dont-use).
- **`cockroachdb-cloud`** (CockroachDB Cloud) needs a CockroachDB Cloud account, and signs in with OAuth through your browser.

#### Install MCP Toolbox

macOS or Linux:

```bash
brew install mcp-toolbox
```

Windows: download `toolbox.exe` into a folder on your `PATH`. In PowerShell, set the version to the latest one on the [Toolbox releases page](https://github.com/googleapis/mcp-toolbox/releases), and use `windows/arm64` in the URL on Arm devices:

```powershell
$VERSION = "1.14.0"
curl.exe -o toolbox.exe "https://storage.googleapis.com/mcp-toolbox-for-databases/v$VERSION/windows/amd64/toolbox.exe"
```

Restart Cursor after changing your `PATH`, then confirm the install with `toolbox --version`. For other platforms, the container image, and building from source, see [Install Toolbox](https://github.com/googleapis/mcp-toolbox#install-toolbox).

## Configuration

### Self-hosted clusters (MCP Toolbox)

The `cockroachdb-toolbox` server reads its connection settings from these variables. Each one is optional, and an unset variable falls back to its default:

| Variable               | Default     | Notes                                                                        |
|------------------------|-------------|------------------------------------------------------------------------------|
| `COCKROACHDB_HOST`     | `localhost` |                                                                              |
| `COCKROACHDB_PORT`     | `26257`     |                                                                              |
| `COCKROACHDB_USER`     | `root`      |                                                                              |
| `COCKROACHDB_PASSWORD` | (empty)     |                                                                              |
| `COCKROACHDB_DATABASE` | `defaultdb` |                                                                              |
| `COCKROACHDB_SSLMODE`  | `require`   | Use `disable` for a local `--insecure` cluster, `verify-full` for production |

Set them in the environment Cursor starts from. On macOS or Linux, add them to your shell profile:

```bash
export COCKROACHDB_HOST="your-cluster-host"
export COCKROACHDB_PORT="26257"
export COCKROACHDB_USER="your-user"
export COCKROACHDB_PASSWORD="your-password"
export COCKROACHDB_DATABASE="your-database"
export COCKROACHDB_SSLMODE="verify-full"
```

On Windows, set them as user environment variables, for example `setx COCKROACHDB_HOST "your-cluster-host"`, then restart Cursor. You can also set them for the plugin in Cursor's dashboard under **Plugins** → **Configure**.

For a CockroachDB Cloud cluster, find the connection details in the [Cloud Console](https://cockroachlabs.cloud/).

Toolbox runs in read-only mode: `SELECT`, `SHOW`, and `EXPLAIN` work, and writes and schema changes are rejected. To allow writes, run Toolbox with your own copy of [`tools.yaml`](tools.yaml) that sets `enableWriteMode: true`, configured as your own MCP server, and turn off the plugin's `cockroachdb-toolbox` server.

### CockroachDB Cloud

The `cockroachdb-cloud` server connects to the [managed MCP server](https://www.cockroachlabs.com/docs/cockroachcloud/connect-to-the-cockroachdb-cloud-mcp-server) that Cockroach Labs hosts. The consent screen asks you to grant read access, write access, or both.

By default the connection can reach every cluster your CockroachDB Cloud role allows. To limit it to one cluster, set `COCKROACHDB_CLUSTER_ID` to that cluster's ID.

### Turn off a server you don't use

Cursor starts both MCP servers. If you only use one of them, open **Customize** in Cursor and toggle the other off. Disabled servers don't load or appear in chat.

## MCP Backends

<details>
<summary><strong>MCP Toolbox</strong> (self-hosted, any cluster) — Default</summary>

Connect to any CockroachDB cluster (Cloud, self-hosted, local) via [MCP Toolbox for Databases](https://github.com/googleapis/mcp-toolbox).

**Install:** see [Install MCP Toolbox](#install-mcp-toolbox) (v1.0.0+)

**Run (stdio, default):** `toolbox --config tools.yaml --stdio`

**Run (HTTP):** `toolbox --config tools.yaml --address 0.0.0.0 --port 5000`

**Run (with web UI):** `toolbox --config tools.yaml --ui --port 5000` (opens at `http://127.0.0.1:5000/ui`)

> **Note:** Toolbox must successfully connect to CockroachDB on startup. If the database is unreachable (wrong host/port, env vars not set), the server will hang during initialization and the UI will be stuck on "Fetching tools...". Make sure your `COCKROACHDB_*` environment variables are set and the database is accessible before starting.
</details>

<details>
<summary><strong>CockroachDB MCP Server</strong> (first-party, self-hosted)</summary>

[CockroachDB MCP Server](https://github.com/cockroachdb/cockroachdb-mcp-server) is Cockroach Labs' own MCP server for clusters you run yourself. By default it registers only read-only tools, such as `list_databases`, `list_tables`, `get_table_schema`, `select_query`, `explain_query`, `show_statement`, and `show_running_queries`. Setting `CRDB_MCP_ENABLE_WRITE_QUERIES=true` adds `create_database`, `create_table`, `insert_rows`, `update_rows`, and `delete_rows`, and the server refuses an `UPDATE` or `DELETE` without a `WHERE` clause.

**Install:** `go install github.com/cockroachdb/cockroachdb-mcp-server@latest` (Go 1.26+). Linux and Windows binaries and a Docker image are listed on the [releases page](https://github.com/cockroachdb/cockroachdb-mcp-server/releases). There are no prebuilt macOS binaries, so on macOS use `go install` or Docker.

**Configure** with certificate authentication (recommended), in `~/.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "cockroachdb-mcp-server": {
      "command": "cockroachdb-mcp-server",
      "env": {
        "CRDB_HOST": "your-cluster-host",
        "CRDB_USERNAME": "ai_agent",
        "CRDB_SSL_MODE": "verify-full",
        "CRDB_SSL_CA_PATH": "/certs/ca.crt",
        "CRDB_SSL_CERTFILE": "/certs/client.ai_agent.crt",
        "CRDB_SSL_KEYFILE": "/certs/client.ai_agent.key"
      }
    }
  }
}
```

For a local `--insecure` development cluster, use `"env": {"CRDB_DATABASE_URL": "postgresql://root@localhost:26257/defaultdb?sslmode=disable", "CRDB_MCP_ALLOW_INSECURE_DB": "true"}` instead.

Password authentication is off unless you set `CRDB_MCP_ALLOW_PASSWORD_AUTH=true`. If the server doesn't start, give the binary's absolute path as the `command`, since Cursor may not search `~/go/bin`. See the [server's README](https://github.com/cockroachdb/cockroachdb-mcp-server#configuration) for every setting.
</details>

<details>
<summary><strong>ccloud CLI</strong> (cluster lifecycle, backups, DR, networking)</summary>

The [`ccloud` CLI](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-cli-database-automation/) is an agent-ready command-line tool for full cluster lifecycle management. AI agents call ccloud directly via shell commands (not MCP protocol) -- every command supports `-o json` for structured output.

**Install:** `brew install cockroachdb/tap/ccloud`

**Authenticate (interactive):** `ccloud auth login` (opens browser; supports SSO via OIDC/SAMLv2)

**Authenticate (org-scoped):** `ccloud auth login --org {organization-label}`

**Authenticate (headless/CI):** `ccloud auth login --no-redirect` or use a service account API key as a bearer token.

**Example agent commands:**
```bash
# Provision
ccloud cluster create serverless my-cluster us-east-1 --cloud AWS -o json
ccloud cluster database create my-cluster myapp -o json

# Connect
ccloud cluster connection-string my-cluster --database myapp --sql-user maxroach -o json
# Composable: pipe into jq + psql
ccloud cluster connection-string my-cluster --database myapp --sql-user maxroach -o json \
  | jq -r '.connection_url' | xargs -I{} psql {} -c "SELECT count(*) FROM users"

# Operate
ccloud cluster list -o json
ccloud cluster info my-cluster -o json
ccloud cluster backup config update my-cluster --frequency 60 --retention 60

# Observe
ccloud audit list --limit 10 -o json
ccloud cluster versions -o json
ccloud cluster cmek get my-cluster -o json

# Scale & DR
ccloud replication create --primary-cluster prod-east --standby-cluster dr-west
ccloud cluster networking allowlist list <cluster-id> -o json

# Organize
ccloud folder create Production -o json
ccloud folder contents <folder-id> -o json

# Test resilience
ccloud cluster disruption set my-cluster --region us-east-1 --whole-region
```

**Coverage:** Provision, Connect, Operate, Observe, Scale & DR, Organize, Test resilience. See the [ccloud reference](https://www.cockroachlabs.com/docs/cockroachcloud/ccloud-reference) for full command list.
</details>

<details>
<summary><strong>CockroachDB Cloud MCP Server</strong> (OAuth/API key)</summary>

The official [managed MCP server](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-managed-mcp-server/) is hosted by Cockroach Labs and requires no infrastructure setup. The plugin already includes it as `cockroachdb-cloud`; use these snippets to configure it yourself, for example with a service account API key. With OAuth 2.1 (PKCE), the consent screen asks you to grant read access, write access, or both (scopes `mcp:read` and `mcp:write`).

**OAuth (recommended, opens a browser for consent):**
```json
{
  "mcpServers": {
    "cockroachdb-cloud": {
      "type": "http",
      "url": "https://cockroachlabs.cloud/mcp"
    }
  }
}
```

**API key (headless or autonomous agents):**
```json
{
  "mcpServers": {
    "cockroachdb-cloud": {
      "type": "http",
      "url": "https://cockroachlabs.cloud/mcp",
      "headers": {
        "Authorization": "Bearer {your-service-account-api-key}"
      }
    }
  }
}
```

Without an `mcp-cluster-id` header, a connection can reach every cluster your user or service account can access. To limit it to one cluster, add `"mcp-cluster-id": "{your-cluster-id}"` to `headers`.

See the [quickstart guide](https://www.cockroachlabs.com/docs/cockroachcloud/connect-to-the-cockroachdb-cloud-mcp-server) for detailed setup.
</details>

## What's Included

### MCP Backends

| Backend                    | Status      | Transport       | Use Case                                                                                                                          |
|----------------------------|-------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `cockroachdb-toolbox`      | Active      | stdio           | Any CockroachDB cluster via [MCP Toolbox](https://github.com/googleapis/mcp-toolbox)                                            |
| `cockroachdb-cloud`        | Active      | Streamable HTTP | [Managed MCP Server](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-managed-mcp-server/) — CockroachDB Cloud (OAuth/API key) |
| `cockroachdb-toolbox-http` | Available   | HTTP            | MCP Toolbox remote/multi-user via HTTP (not shipped; add it yourself)                                                             |
| `cockroachdb-mcp-server`   | Available   | stdio, HTTPS    | First-party server for self-hosted clusters (not shipped; add it yourself, see MCP Backends)                                      |

### CLI Tools

| Tool              | Status | Use Case                                                                                                                                           |
|-------------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| `ccloud`          | Active | [Agent-ready CLI](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-cli-database-automation/) — cluster lifecycle, backups, DR, networking, audit. Agents call directly via shell. |

### Tools

**MCP Toolbox** (self-hosted, any cluster):

| Tool                       | Description                                      |
|----------------------------|--------------------------------------------------|
| `cockroachdb-execute-sql`  | Run SQL (read-only unless write mode is enabled) |
| `cockroachdb-list-schemas` | List all schemas in the database                 |
| `cockroachdb-list-tables`  | List tables with columns, types, and constraints |

**CockroachDB Cloud MCP** (managed, read tools):

| Tool                    | Description                                 |
|-------------------------|---------------------------------------------|
| `list_clusters`         | List all accessible clusters                |
| `get_cluster`           | Get detailed cluster information            |
| `list_databases`        | List databases in the cluster               |
| `list_tables`           | List tables in a database                   |
| `get_table_schema`      | Get detailed schema for a table             |
| `select_query`          | Execute a SELECT statement                  |
| `explain_query`         | Execute an EXPLAIN statement                |
| `show_running_queries`  | List currently executing queries            |

**CockroachDB Cloud MCP** (managed, write tools — requires write consent):

| Tool                    | Description                                 |
|-------------------------|---------------------------------------------|
| `create_database`       | Create a new database                       |
| `create_table`          | Create a new table                          |
| `insert_rows`           | Insert rows into a table                    |

### Skills

Skills are sourced from the [`cockroachdb-skills`](https://github.com/cockroachlabs/cockroachdb-skills) submodule — a single source of truth shared across CockroachDB agent integrations.

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
.cursor-plugin/plugin.json    # Plugin manifest
skills/                       # Skills from cockroachdb-skills submodule
rules/                        # Rule sets (.mdc files)
mcp.json                      # MCP server definitions (Toolbox + Cloud MCP)
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
- [Managed MCP Server Blog Post](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-managed-mcp-server/)
- [Cloud MCP Quickstart Guide](https://www.cockroachlabs.com/docs/cockroachcloud/connect-to-the-cockroachdb-cloud-mcp-server)
- [ccloud CLI for AI Agents Blog Post](https://www.cockroachlabs.com/blog/cockroachdb-ai-agents-cli-database-automation/)
- [ccloud CLI Reference](https://www.cockroachlabs.com/docs/cockroachcloud/ccloud-get-started)
- [MCP Toolbox for Databases](https://github.com/googleapis/mcp-toolbox)
- [Report Issues](https://github.com/cockroachdb/cursor-plugin/issues)

## License

[Apache-2.0](LICENSE)
