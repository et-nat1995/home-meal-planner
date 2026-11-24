# ADR-0004: Shopping-list reconciliation

**Date:** 2026-11-23  
**Status:** Accepted  
**Decider:** Technical architect

## Context

Changes to planned meals and recipes recalculate shopping requirements. Manual items and prior user actions must not silently disappear or make a changed requirement appear fully covered.

## Decision

Keep calculated recipe contributions separate from manual additions and user adjustments. Carry on-hand, purchased and override states forward automatically only when ingredient, unit, total quantity and contributing lines are unchanged. When they change, retain prior states as context and mark resulting items for review; do not treat uncertain items as conclusively on hand or purchased. Manual additions survive recalculation.

## Alternatives considered

- **Reset all states on every recalculation:** Simpler, but discards useful household work even for unchanged items.
- **Automatically carry states through changes:** Less review, but risks presenting an incompletely covered requirement as complete.

## Consequences

Reconciliation favors traceability and caution, at the cost of more review and implementation detail. For example, a checked item changing from one cup to one and a half cups retains the earlier check as context but requires review of the new requirement. Related plan changes and recalculation must be transactional.

## Open details

Specify durable item identity, merge and split behavior, override semantics, prior-state carryover and review resolution with concrete examples before implementation.
