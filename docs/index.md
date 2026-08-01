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
| **[1. Installation](01. Installation/1.1 Quickstart.md)** | Step-by-step install. | You want a running CyberHub. |
| **[3. User and Admin Guides](03. User and Admin Guides/01-guide-overview.md)** | How to use the platform once it is deployed. | You are a student, instructor, or admin. |
| **[4. Developer Overview](04. Developer Overview/01-overview.md)** | How the platform works and why, including the plugin internals. | You are changing CyberCore itself. |

Most people need sections 1 and 3. Section 4 is for people working on the
platform's code — it assumes the vocabulary defined in the
[4.1 Overview glossary](04. Developer Overview/01-overview.md#glossary).

## Developer Overview — reading order

Start at the top and work down. The first three are the foundation; the rest
are subsystem docs you can read on demand.

| # | Doc | What it covers |
|---|-----|----------------|
| 4.1 | [Overview](04. Developer Overview/01-overview.md) | What CyberCore is, the glossary, and the top-level system diagram. **Start here.** |
| 4.2 | [Architecture](04. Developer Overview/02-architecture.md) | Components, the boot sequence, and the request lifecycle. |
| 4.3 | [Data Model](04. Developer Overview/03-data-model.md) | The databases, the entity-relationship map, and a key-table reference. |
| 4.4 | [Modules & Plugins](04. Developer Overview/04-modules-and-plugins.md) | The manifest-driven loader and how to add a module or plugin. |
| 4.5 | [Lanes & Provisioning](04. Developer Overview/05-lanes-and-provisioning.md) | The lane lifecycle and the Proxmox/SDN deploy sequence. |
| 4.6 | [Networking](04. Developer Overview/06-networking.md) | Subnet schemes v1/v2/v3, lane gateways, Tailscale, and Guacamole consoles. |
| 4.7 | [Crucible & Challenges](04. Developer Overview/07-crucible-challenges.md) | The challenge catalog vs. live events, and attachable challenges. |
| 4.8 | [Auth & Security](04. Developer Overview/08-auth-and-security.md) | JWT/session auth, roles, MFA, rate limiting, and signed URLs. |
| 4.9 | [Deployment & Ops](04. Developer Overview/09-deployment-and-ops.md) | The Docker Compose stack, environment variables, and offline mode. |
| 4.10 | [Plugins: CiaB & CLE](04. Developer Overview/10-plugins.md) | The two shipped plugins and what they add. |
| 4.11 | [Clinic-in-a-Box](04. Developer Overview/4.11 Clinic-in-a-Box (CiaB)/01-ciab-overview.md) | Plugin internals: profiles, intakes, the risk assessment and CIS RAM deliverables, the interview simulator, instructor tooling, and profile-driven lane deployment. |
| 4.12 | [Cyber Learning Environment](04. Developer Overview/4.12 Cyber Learning Environment/01-cle-overview.md) | Plugin internals: courses, rosters, and course-driven lab provisioning. |

The last two are the shipped plugins. [4.10](04. Developer Overview/10-plugins.md)
summarizes both; 4.11 and 4.12 are the full references.

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
