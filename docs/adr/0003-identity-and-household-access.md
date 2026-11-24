# ADR-0003: Identity and household access

**Date:** 2026-11-23  
**Status:** Accepted  
**Decider:** Technical architect

## Context

Only two authorized household members may access private features. The proof of concept runs on a home network; Google sign-in is the preferred longer-term direction for cloud deployment.

## Decision

Use two local accounts for the proof of concept. Keep household users and membership separate from sign-in identities, and enforce verified household membership on the server for every private data operation. Later, explicitly link verified Google identities to the existing users through a secure migration process.

## Alternatives considered

- **Google sign-in immediately:** Aligns with the long-term direction but adds OAuth, domain and HTTPS setup to the initial proof of concept.
- **Shared household access:** Simpler, but does not provide the two individually authorized identities required by the PRD.

## Consequences

The proof of concept avoids the initial Google integration while retaining a path to it. It still needs secure password storage, sessions, provisioning, revocation and recovery. A network location alone does not grant authorization. HTTPS is needed for sign-in from other devices on the home network.

## Open details

Select a maintained local authentication solution and specify account recovery and session behavior. Design and test the Google identity-linking procedure; do not assume matching by email alone is sufficient.
