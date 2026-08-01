# 4.11.6 CIS RAM Workbook

The CIS RAM tab mirrors the published CIS RAM v2.1 IG1 workbook in the
database, so students fill out the real thing instead of a spreadsheet
attachment. It's a tab inside the Clinic Risk Assessment SPA, backed by
[routes/cis-ram.js](https://github.com/Saguaros-CyberHub/CyberCore/blob/main/front-end/modules/crucible/plugins/ciab/routes/cis-ram.js)
at `/api/cis-ram`.

## Shape

Two tables, both added by migration `005_cis_ram.sql`:

| Table | Cardinality | Holds |
|---|---|---|
| `cis_ram_assessments` | one per profile | The engagement envelope – `acceptable_risk_score` (1–9, default 6), `impact_criteria` notes, `status` |
| `cis_ram_safeguards` | 56 per profile | One row per IG1 safeguard – inherent scoring, treatment plan, residual scoring |

Both are created lazily on first GET, not at profile creation. The
envelope is a single insert; the 56 safeguard rows go in as one
`INSERT … VALUES (…) ON CONFLICT DO NOTHING` so re-opening the tab is a no-op
and the first open doesn't cost 56 round-trips.

`safeguard_num` (`"1.1"`, `"4.3"`, …) joins back to
[data/frameworks/cis-ig1.json](https://github.com/Saguaros-CyberHub/CyberCore/blob/main/front-end/modules/crucible/plugins/ciab/data/frameworks/cis-ig1.json),
which carries the CIS Controls v8.1 IG1 catalog: 56 safeguards across 17
controls, plus the control→CSF-function crosswalk used elsewhere
([04](05-risk-assessment.md)).

## The risk math

CIS RAM is tri-factor and scores everything 1–3, unlike the 1–5 likelihood
× impact used by `risk_findings`. Two impact dimensions are assessed, and the
worse one wins:

```
inherent_risk_score = likelihood × GREATEST(mission_impact, obligations_impact)
residual_risk_score = treatment_likelihood × GREATEST(treatment_mission_impact,
                                                      treatment_obligations_impact)
```

Both are generated stored columns, so they can't drift from their inputs
and they're indexable. Each lands in 1–9.

`is_reasonable` — the workbook's central question — is computed at read
time, never stored:

```js
is_reasonable = (residual_risk_score != null)
  ? residual_risk_score <= acceptable_risk_score
  : null;
```

A safeguard with no scored treatment yet returns `null`, not `false`. Because
it's read-time, raising or lowering `acceptable_risk_score` immediately
re-evaluates all 56 rows – no recompute pass.

## Per-safeguard fields

| Group | Fields |
|---|---|
| Scope | `asset_class` (Workstations, Servers, Data, …) |
| Inherent risk | `mission_impact`, `obligations_impact`, `likelihood` (each 1–3) |
| Treatment plan | `treatment_safeguard` (defaults to itself), `treatment_title`, `treatment_description`, `treatment_cost` (free text – "Low", "$5k") |
| Residual risk | `treatment_mission_impact`, `treatment_obligations_impact`, `treatment_likelihood` (each 1–3) |
| Tracking | `implementation_year`, `last_completed_date`, `notes`, `status` |

`status` is `open` / `accepted` / `mitigated` / `transferred` / `not_applicable`.

## API

| Endpoint | Purpose |
|---|---|
| `GET /api/cis-ram/:profileId` | Full workbook bundle; lazily creates the envelope + 56 rows |
| `PUT /api/cis-ram/:profileId` | Update the envelope (`acceptable_risk_score` 1–9, `impact_criteria`, `status`) |
| `PUT /api/cis-ram/:profileId/safeguards/:safeguardNum` | Update one safeguard row |

Auth is `authenticateToken` on the whole router plus the same
`userCanReadProfile` ownership check used by the risk assessment: instructors
and admins read any profile, students only their own.

The GET response is grouped for the UI's collapsible layout:

```jsonc
{
  "profile": { "id": "…", "company_name": "…" },
  "assessment": { "acceptable_risk_score": 6, "impact_criteria": {…}, "status": "in_progress" },
  "safeguard_catalog": { "1.1": { "name": "…", "control": 1, "control_name": "…" }, … },
  "controls": [
    { "control": 1, "control_name": "Inventory and Control of Enterprise Assets",
      "rows": [ … ], "scored": 2, "total": 5 }
  ],
  "totals": { "total": 56, "scored": 31, "reasonable": 18, "above_acceptable": 9 }
}
```

Rows are sorted by catalog order, not string order, so `9.1` correctly
precedes `10.1`.

The safeguard update endpoint has a fixed allow-list of editable columns —
generated columns and identity columns can't be written through it. Empty
strings from form inputs become `NULL` so a field can be cleared. A 404 from
this endpoint means the rows were never pre-populated: open the workbook tab
once.

## Where it surfaces

Besides its own tab, the workbook feeds the Clinic Risk Assessment PDF export.
That load is best-effort — if the tables or rows aren't there, the renderer
logs and skips the CIS RAM section rather than failing the export.

The instructor answer-key generator populates all 56 rows plus the envelope
for a profile; see [07 – Instructor Tools](08-instructor-tools.md).

Continue to **[06 – Interview Simulator](07-interview-simulator.md)**.
