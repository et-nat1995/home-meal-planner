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

## Reconciliation contract

- Each source contribution has a stable ID for its planned slot and ingredient line while that line exists. Each calculated list item has its own opaque, list-scoped ID; it is not derived from a name, quantity, position or unit. Manual items have separate IDs and never participate in automatic merging. Store contribution-to-item membership and prior user decisions separately from calculated totals.
- Recompute groups using the matching rules in the architecture. An old item keeps its ID only if its contribution ID set exactly matches one new group. A quantity, ingredient or unit change with the same membership retains the ID but triggers review. A merge or split creates new IDs for every affected group; retire the old IDs and retain links to them as review context. Never choose one old item as an arbitrary winner. A new group with no prior contributions gets a new ID and default states. Repeating reconciliation with identical input must not create new IDs or duplicate review records.
- Carry active on-hand, purchased, exclusion, category correction and quantity override only if ingredient, unit, exact total and contribution IDs are unchanged. Otherwise, preserve prior decisions and old and new calculated values as review context, but clear those decisions from the active result. An unresolved review remains unresolved on subsequent recalculations, even if the inputs later return to their old values. Carry all relevant predecessor contexts to every affected successor; do not infer how an old decision should be divided or combined.
- A quantity override is an absolute replacement for the displayed shopping quantity in that item's unit, not a delta or a change to source contributions. It must be nonnegative and explicitly entered. A changed group does not use an old override until a household member explicitly confirms or replaces it. Exclusion, on-hand and purchased are distinct decisions; while review is pending none applies to the new requirement. A category correction likewise requires confirmation after a changed group; use the calculated category in the meantime.
- Review shows each successor's current total and sources alongside all linked prior totals, sources and decisions. The member resolves each resulting item independently by accepting the newly calculated requirement or explicitly setting any desired override, category, exclusion, on-hand or purchased state. Resolution clears that item's flag only; it does not resolve sibling items. Save the decision against the current list revision, and reject a stale resolution if recalculation intervened. If all contributions to an item disappear, retire it and retain its prior context in a list-level removed-items review until acknowledged; do not create a zero-quantity shopping requirement. Manual items and their states remain untouched.
- Plan or recipe change, recalculation, ID and context updates, and revision increment occur atomically. Retired IDs are never reused. Exact quantities and documented unit conversions determine equality; formatting-only changes do not trigger review.

## Acceptance examples

| Before | Change | Expected result |
| --- | --- | --- |
| Item A: 1 cup flour from line 1, purchased. | Same line, ingredient, unit and quantity after recalculation. | A remains purchased with no review. A repeated recalculation is identical. |
| A: 1 cup flour, purchased, with an absolute override of 2 cups. | Line 1 changes to 1½ cups. | A keeps its ID; 1½ cups is active, unpurchased and needs review. The former check and 2-cup override are context only. The member may explicitly set 2 cups again or accept 1½ cups. |
| A: 1 cup flour from line 1, on hand; B: ½ cup flour from line 2, purchased, formerly separate. | The lines become confidently matching and compatible. | New item C totals 1½ cups; A and B retire. C needs review and is neither on hand nor purchased; both old decisions are visible as context. |
| A: 1½ cups flour from lines 1 and 2, excluded. | Line 2 becomes a different ingredient. | A retires; two new items each need independent review with A's exclusion shown as context. Neither is excluded unless explicitly set. |
| A: 1 cup flour, on hand; a separate manual milk item is purchased. | Flour's only line is removed. | A retires into removed-items review; no flour requirement remains. Manual milk stays purchased. Acknowledging removal clears that review. |
| A: 1 cup flour, purchased. | It changes to 2 cups, then back to 1 cup before review. | A retains its ID and remains under review; the old check is context, not an active purchased state. |

## Remaining scope decisions

Whether to maintain lists for multiple upcoming weeks, list retention and draft-list editing remain open. They do not change the reconciliation rules for a given list.
