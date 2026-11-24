# Implementation backlog

**Status:** Planning; no application has been built.  
**Updated:** 2026-11-23

This backlog is also tracked in [GitHub issues](https://github.com/et-nat1995/home-meal-planner/issues). It turns the [PRD](PRD.md), [architecture document](ARCHITECT.md) and [accepted ADRs](adr/README.md) into an ordered starting point. It is a planning document, not authorization to begin implementation. Estimates and detailed task breakdowns remain open.

## Priorities and dependencies

The first usable vertical slice is manual recipe entry → editable weekly plan → explicit finalization → traceable shopping list. Calendar labels, scheduled suggestions and AI-assisted intake belong to the broader first release or discovery, but follow validation of that slice. Each item below can be split into smaller changes when planned.

### 1. Resolve proof-of-concept foundation

**Depends on:** Accepted architecture decisions.

- Choose a maintained local authentication solution and define initial provisioning, recovery, revocation and shared-device sessions.
- Decide local HTTPS, DNS and deployment workflow for the Pi; distinguish same-machine development from access by other household devices.
- Define the minimal local development and PostgreSQL migration workflow.

**Done when:** The technical architect has reviewed the choices and a developer can set up the proof of concept without inventing an identity or network-access policy.

### 2. Define the first-slice behavior contracts

**Depends on:** PRD and ADRs; can be discussed alongside item 1.

- Specify recipe revision and local-Monday cutoff behavior, including enforcement when no job runs exactly at midnight and how time-zone changes affect existing weeks.
- Apply the shopping-item identity, merge/split, override and review examples in [ADR-0004](adr/0004-shopping-list-reconciliation.md); validate them with the household.
- Decide stale-edit conflict and recovery behavior, archive behavior, and whether lists for multiple future weeks are maintained. Confirm whether a draft has only a preview or an editable list.

**Done when:** The architect and household can evaluate concrete examples and the invariants are precise enough to test. Preserve the confirmed historical policy and ADR-0004 unless a new decision changes them.

### 3. Establish the application and access foundation

**Depends on:** Item 1.

- Set up the Next.js and TypeScript application, PostgreSQL, migrations and the agreed Pi deployment approach.
- Provision two local accounts and household memberships; separate household users from sign-in identities.
- Verify sessions and authorize membership on the server for private reads and writes. Provide the agreed local HTTPS path for use from other devices.

**Done when:** Both authorized users can sign in and reach a protected application, unauthorized access is denied server-side, and the application and database can be started and migrated using documented steps.

### 4. Build the manual recipe library

**Depends on:** Items 2 and 3.

- Create, edit, search and archive manually entered recipes, retaining ingredient source text, structured values when known, ordered instructions and optional fields.
- Represent missing or ambiguous quantities without inventing values. Implement the agreed recipe history mechanism.

**Done when:** A household member can maintain a recipe and open its stored ingredients and steps; archived recipes remain readable where referenced, and recipe changes follow the agreed historical policy.

### 5. Build manual weekly planning and finalization

**Depends on:** Items 2–4.

- Identify weeks by local Monday; show seven days and permit recipe or free-form dinner entries for Monday–Thursday.
- Assign, replace and clear entries, apply the agreed concurrency behavior, and explicitly finalize a plan.
- Enforce the confirmed historical cutoff and preserve recipe content as of that cutoff; keep future weeks editable until their own cutoff.

**Done when:** Both household members can plan and finalize a week, stale changes do not silently overwrite edits, and a historical week remains read-only and readable after later recipe changes. Calendar labels are delivered in item 8.

### 6. Build shopping-list generation and reconciliation

**Depends on:** Items 2, 4 and 5.

- Generate a list on finalization, retaining ingredient text, precise quantities, units and meal provenance. Combine only confidently matching ingredients and compatible units using documented conversions.
- Support manual additions, categories, exclusions and quantity overrides; distinguish on-hand from purchased states.
- Recalculate affected contributions after plan or recipe changes while preserving manual additions and applying the agreed review contract.

**Done when:** The examples in the PRD work, including 1 cup plus 1/2 cup, volume versus weight, onion versus onion powder, ambiguous quantities and a changed plan with retained manual items and review flags.

### 7. Validate the manual vertical slice

**Depends on:** Items 3–6.

- Exercise recipe entry → planning → finalization → shopping on mobile and desktop.
- Verify authorization, concurrent edits, transaction rollback, fractional precision, traceability, reconciliation and historical cutoff behavior.
- Record issues before expanding scope.

**Done when:** The household can complete the manual journey and the important behavior and failure cases pass the agreed checks.

### 8. Add calendar labels

**Depends on:** Foundation and the calendar decisions below; can be scheduled after the manual slice.

- Confirm Israel or diaspora schedule, observance set, and whether location-based times are in scope; select a calendar library or provider.
- Show Shabbat, preceding evenings and overlapping or multi-day holidays. Keep planning usable if calendar data is unavailable.

**Done when:** The seven-day view meets the PRD's calendar acceptance criteria and distinguishes a failure from an empty result.

### 9. Add ratings and scheduled suggestions

**Depends on:** Manual planning, recipe library and the suggestion decisions below.

- Agree on collective versus individual ratings, eligible recipes, ranking, protected edits and regeneration behavior.
- Choose a scheduler and specify catch-up window, missed-run handling, time-zone changes and partially edited drafts.
- Generate a Sunday-noon draft with safe retries and automatic catch-up; preserve finalized plans, user edits and locks.

**Done when:** A scheduled or catch-up run is observable and safe to repeat; insufficient recipes and failures have clear outcomes; users can always plan manually.

### 10. Investigate and add selected AI-assisted intake

**Depends on:** Manual recipe entry and a separate source, provider, privacy and security decision.

- Choose initial source types and assess feasibility, cost, data handling and retention.
- Design validated, reviewable drafts and manual fallback. Protect URL fetching and uploads; do not grant an assistant database credentials or direct publishing authority.

**Done when:** The selected intake path can produce a draft that a household member reviews and corrects before saving, and failure does not create a misleading completed recipe.

### 11. Plan cloud transition and household launch

**Depends on:** A decision to move beyond the temporary Pi proof of concept; may be planned earlier.

- Select cloud hosting and budget, Google sign-in and a secure process for linking existing household users to verified Google identities.
- Decide whether to migrate proof-of-concept data, and rehearse any required database migration.
- Define backups, restore tests, deletion and retention, availability expectations, access recovery and operational ownership.

**Done when:** The migration and launch plan is approved and its security, identity and recovery procedures have been verified. Backups and recovery are explicitly deferred for the temporary Pi proof of concept, not waived for launch.

## Later discovery

Public recipe publication requires a separate publication policy and source-rights decision. Detailed Shabbat and holiday meal planning, guests, dishes and preparation workflows require family discovery. Weighted planning characteristics and more elaborate recommendation behavior remain future design work.
