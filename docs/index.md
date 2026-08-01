# CyberHub Documentation

The CyberHub is a cyber-education platform that provisions isolated, per-user
lab environments ("lanes") on a Proxmox cluster and presents them through a
single web hub. **CyberCore** is its control-plane application — the Express
app that does the provisioning. These docs cover both.

This directory is the source of truth for how the system is built and why. If
something here disagrees with the code, the code wins — please open a PR to fix
the doc.

!!! note "Work in progress"
    These docs are just getting started. Sections below will fill out as the
    platform stabilizes.

## How this site is organized

| Section | What it is | Read it when |
|---------|-----------|--------------|
| **[1. Setup](01. Setup/Quickstart Guide.md)** | Step-by-step install. | You want a running CyberHub. |
| **[2. User and Admin Guides](02. User and Admin Guides/index.md)** | How to use the platform once it is deployed. | You are a student, instructor, or admin. |
| **[3. Developer Overview](03. Developer Overview/01-overview.md)** | How the platform works and why, including the plugin internals. | You are changing CyberCore itself. |

Most people need sections 1 and 2. Section 3 is for people working on the
platform's code — it assumes the vocabulary defined in the
[3.1 Overview glossary](03. Developer Overview/01-overview.md#glossary).

## Developer Overview — reading order

Start at the top and work down. The first three are the foundation; the rest
are subsystem docs you can read on demand.

| # | Doc | What it covers |
|---|-----|----------------|
| 3.1 | [Overview](03. Developer Overview/01-overview.md) | What CyberCore is, the glossary, and the top-level system diagram. **Start here.** |
| 3.2 | [Architecture](03. Developer Overview/02-architecture.md) | Components, the boot sequence, and the request lifecycle. |
| 3.3 | [Data Model](03. Developer Overview/03-data-model.md) | The databases, the entity-relationship map, and a key-table reference. |
| 3.4 | [Modules & Plugins](03. Developer Overview/04-modules-and-plugins.md) | The manifest-driven loader and how to add a module or plugin. |
| 3.5 | [Lanes & Provisioning](03. Developer Overview/05-lanes-and-provisioning.md) | The lane lifecycle and the Proxmox/SDN deploy sequence. |
| 3.6 | [Networking](03. Developer Overview/06-networking.md) | Subnet schemes v1/v2/v3, lane gateways, Tailscale, and Guacamole consoles. |
| 3.7 | [Crucible & Challenges](03. Developer Overview/07-crucible-challenges.md) | The challenge catalog vs. live events, and attachable challenges. |
| 3.8 | [Auth & Security](03. Developer Overview/08-auth-and-security.md) | JWT/session auth, roles, MFA, rate limiting, and signed URLs. |
| 3.9 | [Deployment & Ops](03. Developer Overview/09-deployment-and-ops.md) | The Docker Compose stack, environment variables, and offline mode. |
| 3.10 | [Plugins: CiaB & CLE](03. Developer Overview/10-plugins.md) | The two shipped plugins and what they add. |
| 3.11 | [Clinic-in-a-Box](03. Developer Overview/11. Clinic-in-a-Box/index.md) | Plugin internals: profiles, intakes, the risk assessment and CIS RAM deliverables, the interview simulator, instructor tooling, and profile-driven lane deployment. |
| 3.12 | [Cyber Learning Environment](03. Developer Overview/12. Cyber Learning Environment/index.md) | Plugin internals: courses, rosters, and course-driven lab provisioning. |

The last two are the shipped plugins. [3.10](03. Developer Overview/10-plugins.md)
summarizes both; 3.11 and 3.12 are the full references.

## Conventions used in these docs

- **Code references** are relative paths, e.g. [src/server.js](https://github.com/Saguaros-CyberHub/CyberCore/blob/main/front-end/src/server.js).
- **Tables** are named with their database prefix (`cybercore_*` live in the
  main database; `clinic_*`/`cle_*` live in their plugin databases).
- **Diagrams** are [Mermaid](https://mermaid.js.org/) fenced code blocks so they
  render on GitHub and in most Markdown viewers.

## About this site

This documentation is built with [MkDocs](https://www.mkdocs.org) and published
to <https://docs.saguaroscyberhub.org> from the
[CyberHub-Docs](https://github.com/Saguaros-CyberHub/CyberHub-Docs) repository.

To work on the docs locally:

```bash
pip install mkdocs
mkdocs serve
```

Then open <http://127.0.0.1:8000>. Pages live in `docs/` as Markdown; the site
structure and settings are in `mkdocs.yml`.
