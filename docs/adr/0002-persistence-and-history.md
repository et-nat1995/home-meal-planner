# ADR-0002: Persistence and history

**Date:** 2026-11-23  
**Status:** Accepted  
**Decider:** Technical architect

## Context

The application must maintain consistent plan and shopping-list data, support two users editing, and keep historical plans readable as recipes change. The Pi uses an SD card during the proof of concept; cloud deployment is intended later.

## Decision

Use PostgreSQL from the proof of concept onward and transactions for related plan and shopping-list updates. A week becomes historical and read-only at midnight on the following Monday in the household's configured time zone. Preserve its plan and recipe content as they stood at that cutoff; historical shopping-list snapshots are not required for the MVP.

## Alternatives considered

- **SQLite:** Simpler initial operation, but moving to managed PostgreSQL later would require a database migration.
- **Show current recipes in historical plans:** Simpler history, but changes the confirmed product policy and can rewrite the meaning of past weeks.

## Consequences

PostgreSQL adds a service to operate but provides the same database technology for a possible cloud move. Historical preservation requires a reliable revision or equivalent design that does not depend on a perfectly timed midnight job. Backup destination and recovery policy are deferred during the temporary Pi proof of concept and must be addressed before cloud launch.

## Open details

Specify recipe revisions, cutoff enforcement and time-zone-change semantics. Decide how to export and migrate any proof-of-concept data worth retaining, and establish backup, restore and retention policies before cloud launch.
