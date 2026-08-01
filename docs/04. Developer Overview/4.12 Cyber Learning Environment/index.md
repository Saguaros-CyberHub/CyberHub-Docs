# 4.12 Cyber Learning Environment (CLE)

The Cyber Learning Environment is the instructor-facing class-management plugin
— an LMS-lite layer over the range. Instructors create courses, enroll students,
attach materials and labs, review submissions, and track sessions. It ties
courses to the lane system so a course can provision lab environments for its
whole roster.

!!! note "Not yet expanded"
    This section is a summary. CLE does not yet have the per-topic docs that
    [Clinic-in-a-Box](../4.11 Clinic-in-a-Box (CiaB)/01-ciab-overview.md) has. The summary in
    [4.10 – Plugins](../10-plugins.md) is currently the
    fullest account.

## At a glance

| | |
|---|---|
| Key | `cle` |
| Lives in | [modules/crucible/plugins/cle/](https://github.com/Saguaros-CyberHub/CyberCore/tree/main/front-end/modules/crucible/plugins/cle/) |
| Parent module | `crucible` |
| Database | `cle_db` |
| Page mount | `/cle` |
| API mount | `/` – each sub-router registers its own prefix |
| Roles | instructor · admin (mostly gated) |

## Capabilities

| Area | Routes | What it covers |
|------|--------|----------------|
| Courses | `courses.js`, `course-students.js` | Course CRUD, roster management, enrollment. |
| Students | `students.js` | Student directory within a course. |
| Labs / VMs | `labs.js`, `vms.js`, `templates.js` | Attach labs to courses; provision and track per-course VMs. |
| Provisioning | `utils/lane-provision.js` | Bridges course labs into the core lane system. |
| Consoles | `guacamole.js` | Guacamole console access for course VMs. |
| Sessions | `sessions.js` | Session and activity log (`cle_activity_log`). |

## Access control

Course-scoped routes must gate on the shared, admin-aware access helper
([utils/course-access.js](https://github.com/Saguaros-CyberHub/CyberCore/blob/main/front-end/modules/crucible/plugins/cle/utils/course-access.js),
`canManageCourse` / `getManagedCourse`) — **not** a raw `instructor_id`
comparison. An admin must be able to manage any course; an instructor only their
own. The helper enforces both. Read that pattern before adding a course route.

## Data model

`cle_db` holds `cle_course`, `cle_course_enrollment`, `cle_course_material`,
`cle_student_submission`, and `cle_activity_log`.

## Related

- [4.10 – Plugins: CiaB & CLE](../10-plugins.md) – both plugins side by side.
- [4.4 – Modules & Plugins](../04-modules-and-plugins.md) – how plugins load and get their database.
- [4.5 – Lanes & Provisioning](../05-lanes-and-provisioning.md) – the lane system CLE provisions into.
