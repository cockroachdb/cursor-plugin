# Changelog

## [0.1.8](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.7...v0.1.8) (2026-05-03)


### Features

* add setup-cockroachdb.sh for local 3-node cluster provisioning ([fabeefa](https://github.com/cockroachdb/cursor-plugin/commit/fabeefa81fbfc65dcbde323419d4824d7362d676))

## [0.1.7](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.6...v0.1.7) (2026-04-22)


### Bug Fixes

* migrate toolbox config to v1.1.0 map-based format ([2130c90](https://github.com/cockroachdb/cursor-plugin/commit/2130c90a6ab2e4178cb92756e00a5bbcd09664cc))

## [0.1.6](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.5...v0.1.6) (2026-04-12)


### Bug Fixes

* add COCKROACHDB_SSLMODE env var to mcp.json for parity with claude-plugin ([4bbc9fc](https://github.com/cockroachdb/cursor-plugin/commit/4bbc9fc2a69b79aee772c581419ac4a7559a272b))

## [0.1.5](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.4...v0.1.5) (2026-04-10)


### Bug Fixes

* add explicit skills paths to plugin.json for marketplace discovery ([3560a82](https://github.com/cockroachdb/cursor-plugin/commit/3560a82dfe9362e445b446b8324f724448ad8f83))

## [0.1.4](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.3...v0.1.4) (2026-04-09)


### Bug Fixes

* restructure skills to match upstream category hierarchy ([bb22bdc](https://github.com/cockroachdb/cursor-plugin/commit/bb22bdcd812e484f60b5cd87ae94f6d669dee3e8))

## [0.1.3](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.2...v0.1.3) (2026-03-31)


### Bug Fixes

* remove unsupported plugin.json fields that break Cursor install ([1cfd2ab](https://github.com/cockroachdb/cursor-plugin/commit/1cfd2abe3ba84e9128d331a8895b0f584cab4143))

## [0.1.2](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.1...v0.1.2) (2026-03-27)


### Features

* add Cloud MCP and ccloud CLI GA support, replace skill symlinks with real directories ([a160eca](https://github.com/cockroachdb/cursor-plugin/commit/a160ecac9f4d8ac48aeb5a21c569efa9a68dca99))

## [0.1.1](https://github.com/cockroachdb/cursor-plugin/compare/v0.1.0...v0.1.1) (2026-03-17)


### Features

* initial CockroachDB plugin for Cursor ([cfb79b7](https://github.com/cockroachdb/cursor-plugin/commit/cfb79b71a2ca5d0bd9b7c55fc7c55250339dc070))

## 0.1.0 (Unreleased)

### Added
- Initial release of CockroachDB plugin for Cursor
- 4 MCP backends: Toolbox stdio, Toolbox HTTP, ccloud CLI (coming soon), Cloud MCP Server (coming soon)
- 3 database tools: execute-sql, list-schemas, list-tables
- 22 skills via cockroachdb-skills submodule (symlink pattern) across 10 operational domains
- 2 rule sets with 24+ best practices: SQL patterns, application patterns
- cockroachdb-skills submodule reference
- Release Please automation with GitHub Actions
