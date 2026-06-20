# Market Architecture

Aegis Talent Center can grow in three phases. The important boundary is that
public discovery is separate from tenant-local installation and execution.

## Phase 1: Curated Static Center

This phase needs no Worker and no database.

What works:

- Official curated JSON catalogs.
- JSON Schema for manifests and install plans.
- A polished public documentation site.
- Aegis Server reads `AEGIS_TALENT_CENTER_CATALOG_URL`.
- Maintainers review catalog changes through GitHub pull requests.

What does not work:

- Public self-serve submissions.
- Dynamic search ranking.
- User accounts.
- Reviews, ratings, publisher dashboards, or payment.

This is still useful because it gives Aegis a trusted official source without
coupling the public site to the private runtime repo.

## Phase 2: Pages Functions / Worker Search

This phase adds server-side routes but can still avoid a database at first.

Use Cloudflare Pages Functions or a Worker for:

- `/api/catalog/search`
- `/api/catalog/:id`
- `/api/install-plan/preview`
- `/api/schema/:name`

The function can read versioned static JSON bundled with the deployment or from
R2. This makes the center feel like a product API while keeping publishing
controlled by Git.

Cloudflare Pages Functions run on the Workers runtime and can bind Cloudflare
products such as D1 and R2 when needed.

## Phase 2.5: Private Asset Proxy

Cloudflare Workers can also provide a public download facade for private GitHub
assets. This is useful when the source repository must stay private but the
installer needs a stable public URL.

Recommended use:

- Release asset proxy for native bundles, checksums, and signed catalog snapshots.
- Catalog proxy for curated JSON that is generated from a private workflow.
- Short-lived signed URLs for larger artifacts.

Do not expose the GitHub token to the browser. Store it as a Worker secret and
use the narrowest practical GitHub token scope. The Worker should stream the
asset, set cache headers, and never return private repository metadata beyond
the selected public artifact.

Suggested routes:

```text
GET /api/releases/:repo/:tag/:asset
GET /api/catalog/:name
GET /api/checksums/:tag/SHA256SUMS
```

Use a Worker only for ordinary release asset downloads and catalog snapshots.
Native bundle download should stay simple: fetch, checksum, install.

## Phase 3: Real Public Marketplace

This phase needs database and object storage.

Recommended Cloudflare shape:

- **Pages**: polished public website and docs.
- **Pages Functions or Workers**: API, auth callbacks, search, review workflow.
- **D1**: publishers, submissions, reviews, versions, install counts, templates.
- **R2**: signed catalog snapshots, manifest artifacts, screenshots, bundles.
- **KV or cache**: hot catalog/search cache after the canonical D1/R2 write.

Core tables:

```text
publishers(id, name, url, verified, created_at)
talent_submissions(id, publisher_id, manifest_json, status, created_at)
talent_versions(id, talent_id, version, manifest_json, signature, published_at)
catalog_entries(id, kind, display_name, summary, latest_version_id, tags_json)
reviews(id, submission_id, reviewer, decision, notes, created_at)
agent_templates(id, manifest_json, status, published_at)
role_postings(id, template_json, status, published_at)
```

The marketplace still does not execute tenant tools. It only publishes signed
metadata and install plans. Aegis Server and Local Gateway continue to own
installation, binding, permissions, execution, and audit evidence.

## Naming Boundary

Cloudflare Worker / Pages Function:

- Public serverless backend for the Talent Center website/API.
- Handles catalog search, submissions, reviews, signatures, and install-plan
  preview.

Aegis worker / Local Gateway worker:

- Runs on the user's machine.
- Executes explicit structured tool calls.
- Uses local filesystem, terminal, browser, GUI, and stdio MCP tools.

These two should not share code paths or responsibilities.
