# 2.3 Admin Guide

How to run a deployed CyberHub: manage people, put lab environments in front of
them, and keep the thing healthy. Written for instructors and admins.

!!! warning "Outline only"
    This page is a placeholder. The sections below are the intended scope, drawn
    from what the platform actually does — they are not yet written.

## Planned contents

### Roles and what they allow

- The three effective roles: **admin**, **instructor**, and **user**.
- Which surfaces each role reaches, and the rule that an admin can manage any
  course while an instructor manages only their own.

### People

- Creating accounts; the first admin comes from `ADMIN_EMAIL` at install.
- Groups, and using them as the unit you deploy to.
- Enforcing multi-factor authentication.

### Putting labs in front of a class

- Deploying a challenge to a group. This creates **one lane per student**, each
with its own network and its own copies of the target VMs.
- Picking a subnet scheme (v1/v2/v3) and what each implies for access.
- Watching a batch deploy, and reading the failures when some lanes error.
- Attaching a challenge into an existing lane.
- Suspending and tearing down lanes, and why teardown is safe to re-run.

### Events

- Scheduling a live event — CTF, king-of-the-hill, red-versus-blue — and how
  that differs from deploying a challenge.

### Templates and capacity

- The template catalog: the registry of Proxmox VM templates the orchestrator
  clones from.
- What happens when a template moves between nodes. (The platform reconciles
  this at boot; a clone failing with "no such VMID on node" means the catalog
  needs a look.)
- Keeping an eye on cluster capacity before a large deploy.

### Instructor tooling

- **Clinic-in-a-Box:** the console at `/ciab/instructor` — assignments, working
  sets, answer keys, scan documents, groups and schedules, and per-student
  progress. Deploying lanes from a profile.
- **Cyber Learning Environment:** courses, rosters, enrollment, attaching
  materials and labs, reviewing submissions.

### Running without a public domain

Already written up separately: see [2.3 Offline / LAN Mode](04-offline-mode.md)
for LAN and air-gapped operation, and for switching back to public HTTPS.

### Day-to-day operation

- Console access and credential hygiene. **Change the default Guacamole
  password** if that was skipped at install.
- Reading the logs. Output is structured and tagged by subsystem, so filter on
  tags such as `[Deploy]` or `[PluginLoader]`.
- Rate limiting, and what to raise if active users start tripping it.
- Spotting a feature that has gone missing because a module failed to load —
  the server starts anyway by design.
- Backups: what lives in PostgreSQL versus what is disposable.

## Related

- [2.1 User Guide](02-user-guide.md) – what your students see.
- [3.9 Deployment & Ops](../03. Developer Overview/09-deployment-and-ops.md) – the operational reference behind this guide.
- [3.5 Lanes & Provisioning](../03. Developer Overview/05-lanes-and-provisioning.md) – what actually happens during a deploy.
- [3.7 Crucible & Challenges](../03. Developer Overview/07-crucible-challenges.md) – challenges, events, and attachable challenges.
