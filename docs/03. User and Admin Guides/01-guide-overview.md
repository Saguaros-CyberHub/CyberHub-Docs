# 3.1 User and Admin Guides

Everything in this section is about **using** a CyberHub that is already
deployed. If you are standing one up, start at [1.1 Quickstart](../01. Installation/1.1 Quickstart.md).
If you are changing how the platform works, go to
[4. Developer Overview](../04. Developer Overview/01-overview.md).

| Guide | Audience | Covers |
|---|---|---|
| [3.2 User Guide](02-user-guide.md) | Students and other learners | Signing in, reaching your lab environment, working through a challenge or an assessment. |
| [3.3 Admin Guide](03-admin-guide.md) | Instructors and admins | Accounts and groups, deploying labs to a class, monitoring and tearing them down, day-to-day operation. |
| [3.4 Offline / LAN Mode](04-offline-mode.md) | Admins | Running the hub on a local network or an air-gapped machine, without a public domain or HTTPS. |

!!! warning "2.1 and 2.2 are not written yet"
    Those two pages are currently outlines. They exist so the structure is in
    place and so the intended scope is on record. 2.3 is complete.

## A note on writing these

The reason this section lagged is that the UI changes faster than the docs can
track it. The way out is to write to tasks, not to screens:

- Describe the goal and the outcome, not the click path. "Deploy a challenge to
  your class, which creates one isolated lane per student" survives a redesign;
  "click the blue button in the top right" does not.
- Name things by their stable identity — URL paths (`/ciab/workspace`), role
  names, and object names (lane, challenge, event) — rather than by their
  position on screen.
- Push anything version-sensitive into screenshots that are clearly dated, or
  leave it out.
- Link to the Developer Overview for the *why*; keep these pages to the *how*.

The vocabulary these guides depend on — lane, module, plugin, challenge, event,
allocation — is defined in the [4.1 Overview glossary](../04. Developer Overview/01-overview.md#glossary).
