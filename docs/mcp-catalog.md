# MCP Catalog

The MCP catalog is a public discovery list for MCP-backed Talents. Aegis Server
can load it through `AEGIS_TALENT_CENTER_CATALOG_URL`, search it, and generate
an install plan.

Current Aegis Server APIs:

```text
GET  /api/talent-center/mcp?q=notion
POST /api/talent-center/mcp/install-plan
```

## Transports

- `streamable_http`
- `sse`
- `http_json_rpc`
- `stdio`

Remote transports can be planned by the control plane. Local `stdio` transports
must be executed through Aegis Local Gateway because they depend on the user's
host runtime, filesystem, and installed tools.

## Runtime Requirements

Catalog entries should list required local runtimes such as `node`, `npx`,
`python`, `uvx`, or `docker`. If a runtime is missing, the install plan should
say so; it should not mark the Talent as installed.
