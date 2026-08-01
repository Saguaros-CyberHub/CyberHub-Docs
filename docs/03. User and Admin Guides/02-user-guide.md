# 3.2 User Guide

How to use the CyberHub as a learner: get in, reach your lab environment, and
work through what your instructor assigned.

!!! warning "Outline only"
    This page is a placeholder. The sections below are the intended scope, drawn
    from what the platform actually does — they are not yet written.

## Planned contents

### Getting in

- Signing in to the hub.
- Setting up multi-factor authentication.
- What to do when your session drops. (Sessions are backed by a server-side
  store; a platform restart can sign everyone out.)

### Finding your way around

- The hub and its modules: The Crucible, CyberLabs, The Forge, Saguaros
  University.
- What your role lets you see. Most learners hold the plain **user** role.

### Your lab environment

- What a **lane** is: your own private, isolated network plus the VMs on it —
  typically a gateway, a Kali workstation, and whatever targets the challenge
  defines. Everyone in your class gets their own.
- Opening a VM console in the browser. Consoles are brokered through Apache
  Guacamole; no VPN client or local install is needed.
- Lane states you may see: `pending`, `deploying`, `active`, `suspended`,
  `error`.
- What persists and what does not when a lane is torn down.

### Working a challenge

- Challenges versus events: a **challenge** is a scenario you can deploy on
  demand; an **event** is a scheduled, human-run session such as a live CTF or a
  king-of-the-hill match.
- Submitting flags and tracking your score.

### Clinic-in-a-Box engagements

For classes using the CiaB plugin, where you act as a consultant running a
security engagement for a client organization:

- The workspace at `/ciab/workspace` and the eight-part assessment.
- Completing the intake questionnaire.
- Building the Clinic Risk Assessment: assets, scenarios, findings, and the
  exported report.
- The CIS RAM workbook.
- Practising stakeholder elicitation in the interview simulator.
- Checking your progress.

### When something goes wrong

- A lane that will not start, or a console that will not connect.
- Who to contact, and what to include in the report.

## Related

- [3.3 Admin Guide](03-admin-guide.md) – the instructor side of the same workflows.
- [4.1 Overview](../04. Developer Overview/01-overview.md) – glossary and system map.
