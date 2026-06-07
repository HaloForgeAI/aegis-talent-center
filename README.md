# Aegis Talent Center

Public Talent specs, seed catalogs, and marketplace-facing docs for Aegis.

Aegis Talent Center is the public discovery layer. It does not contain the
private Aegis runtime source. Aegis Server owns installation, binding,
authorization, execution, health checks, and audit evidence. This repository
owns the public contracts and catalog material that a server or user can inspect.

## What Exists Today

- A public Talent manifest schema.
- A public MCP catalog schema.
- A public install-plan schema.
- Seed catalogs for official MCP Talents and recruitment-style agent templates.
- A small Cloudflare Pages-ready static site.

## What This Is Not Yet

- Not a full third-party marketplace.
- Not a payment or hiring platform.
- Not the runtime that executes provider/channel/UI IO.
- Not a replacement for tenant-local Aegis Talent registry and audit logs.

## Market Shape

Phase 1 is a curated static center: official specs, reviewed catalog files, and
a polished public site. That is enough for Aegis Server to discover official
Talents through `AEGIS_TALENT_CENTER_CATALOG_URL`.

Phase 2 adds Cloudflare Pages Functions or Workers for search and install-plan
preview. Phase 3 adds D1/R2 for publisher submissions, reviews, signatures,
versions, screenshots, and recruitment templates.

Cloudflare Worker / Pages Function is the public marketplace backend. It is not
the same thing as an Aegis Local Gateway worker, which runs on a user's machine
and executes explicit tool calls.

## Repository Layout

```text
spec/
├── talent-manifest.schema.json
├── mcp-catalog.schema.json
└── install-plan.schema.json

catalog/
├── official-mcp.seed.json
├── official-talents.seed.json
└── recruitment.seed.json

docs/
├── manifest.md
├── mcp-catalog.md
├── market-architecture.md
└── recruitment-market.md

public/
└── index.html
```

## Use With Aegis Server

Point Aegis Server at a hosted catalog:

```bash
AEGIS_TALENT_CENTER_CATALOG_URL=https://talents.aegis.example/catalog/official-mcp.seed.json
```

Current Aegis API surface:

```text
GET  /api/talent-center/mcp?q=notion
POST /api/talent-center/mcp/install-plan
```

## Deploy The Static Site

```bash
npx wrangler pages deploy public --project-name aegis-talent-center
```

Current Pages preview:

```text
https://55bdf68c.aegis-talent-center.pages.dev
```

Recommended production domain:

```text
talents.aegis.haloforge.dev
```

If DNS/API permissions are not available locally, add the custom domain from the
Cloudflare dashboard and point it at the Pages project.

The long-term target is a Cloudflare Pages site plus optional Workers/Pages
Functions for search, review, signing, and install-plan preview.

## Related Repositories

| Repository | Owns |
| --- | --- |
| `HaloForgeAI/Aegis` | Runtime source, Aegis Server, Gateway, MCP, Talent runtime |
| `HaloForgeAI/aegis-release` | Public installers and release assets |
| `HaloForgeAI/aegis-site` | Aegis brand site |
| `HaloForgeAI/aegis-agent-plugins` | Codex, Claude Code, and portable agent plugins |
