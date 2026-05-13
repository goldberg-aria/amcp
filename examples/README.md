# Examples

This directory contains small examples for using AMCP through:

- direct HTTP calls
- the JavaScript/TypeScript SDK
- the reference client and MCP bridge

Current examples:

- `curl.md`
- `sdk.md`
- `compliance.md`
- `harness-provider.md`

The examples use AMCP v0.4 fields where useful:

- `pinned` for explicit memory pinning
- `supersedes` / `superseded_by` for version relationships

These fields are optional and additive. Older clients may ignore them.
