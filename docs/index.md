# CyberCore Documentation

CyberCore is the control-plane application for the CyberHub, a cyber-education
platform that provisions isolated, per-user lab environments ("lanes") on a
Proxmox cluster and presents them through a single web hub.

This directory is the source of truth for how the system is built and why. If
something here disagrees with the code, the code wins — please open a PR to fix
the doc.

!!! note "Work in progress"
    These docs are just getting started. Sections below will fill out as the
    platform stabilizes.

## Getting started

- [Overview](Overview/01-overview.md) – Overview of how the CyberHub works.
- [How to Setup](Setup/Quickstart Guide.md) – set up CyberHub locally.

## Reading order

Start at the top and work down. The first three are the foundation; the rest are subsystem deep-dives you can read on demand.

| # | Doc | What it covers |
|---|-----|----------------|
| 01 | [Overview](Overview/01-overview.md) | What the CyberCore is, the glossary, and the top-level system diagram. **Start here.** |
| 02 | [Architecture](Overview/02-architecture.md) | Components, the boot sequence, and the request lifecycle. |
| 03 | [Data Model](Overview/03-data-model.md) | The databases, the entity-relationship map, and a key-table reference. |
| 04 | [Modules & Plugins](Overview/04-modules-and-plugins.md) | The manifest-driven loader and how to add a module or plugin. |
| 05 | [Lanes & Provisioning](Overview/05-lanes-and-provisioning.md) | The lane lifecycle and the Proxmox/SDN deploy sequence. |
| 06 | [Networking](Overview/06-networking.md) | Subnet schemes v1/v2/v3, lane gateways, Tailscale, and Guacamole consoles. |
| 07 | [Crucible & Challenges](Overview/07-crucible-challenges.md) | The challenge catalog vs. live events, and attachable challenges. |
| 08 | [Auth & Security](Overview/08-auth-and-security.md) | JWT/session auth, roles, MFA, rate limiting, and signed URLs. |
| 09 | [Deployment & Ops](Overview/09-deployment-and-ops.md) | The Docker Compose stack, environment variables, and offline mode. |
| 10 | [Plugins: CiaB & CLE](Overview/10-plugins.md) | The two shipped plugins and what they add. |

Also: [offline-mode.md](Overview/11-offline-mode.md) – running the CyberHub on a LAN or air-gapped machine without public HTTPS.

## Plugin deep-dives

The Overview covers the platform. Each shipped plugin gets its own section:

- [Clinic-in-a-Box](Plugins/Clinic-in-a-Box/index.md) – AI-powered cyber
  risk-assessment training. Profiles, intakes, the risk assessment and CIS RAM
  deliverables, the interview simulator, instructor tooling, and
  profile-driven lane deployment.

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
