# ADR-0005: Scheduled draft generation

**Date:** 2026-11-23  
**Status:** Accepted  
**Decider:** Technical architect

## Context

The PRD calls for a draft of the upcoming Monday–Thursday plan on Sunday at noon in the household's time zone. The temporary Pi host may be unavailable at that time.

## Decision

Schedule draft generation for Sunday noon local time and attempt automatic catch-up after an outage. Record enough job state to make retries safe and visible. Repeated runs must not create duplicate weekly plans or overwrite a finalized plan, household edits or locked choices.

## Alternatives considered

- **Manual recovery after a missed run:** Simpler, but requires household action after an outage.
- **Generate only when a user opens the app:** Cannot by itself meet the scheduled-draft requirement.

## Consequences

The application needs idempotent generation, recorded outcomes and an actionable failure state. A Pi that is powered off cannot create the draft at noon; catch-up occurs after service resumes. Manual planning remains available.

## Open details

Select the scheduler and define the catch-up window, retries, time-zone changes, partially edited draft behavior, rating rules and insufficient-candidate handling before implementing suggestions.
