# Talent Manifest

A Talent is an installable Aegis capability unit. It can be a skill, method,
tool bundle, provider, channel adapter, UI panel, cron hook, memory hook, or a
composition of those contracts.

The manifest describes what a Talent claims. A tenant-local Aegis Server decides
whether to install, bind, authorize, prepare, execute, and audit it.

## Required Fields

- `id`: stable lowercase identifier such as `dev.aegis.talent.web-research`.
- `version`: semantic version.
- `displayName`: user-facing name.
- `scope`: one or more of `aegis`, `workspace`, `agent`, `channel`.
- `contracts`: declared methods, skills, tools, providers, channels, UI panels,
  or cron contracts.

## Security Model

Manifests must declare permissions up front:

- `network`: allowed external origins or service families.
- `filesystem`: `none`, `read`, `write`, or `workspace`.
- `secrets`: secret names or env secret references.
- `approval`: `none`, `act_with_approval`, or `always_ask`.

Raw secret values must not appear in manifests or binding configs.

## Runtime Boundary

The public Talent Center can publish manifests and install plans. It must not
execute provider/channel/UI IO for a tenant. Execution belongs to Aegis Server
and Local Gateway, with explicit tool calls and audit evidence.
