# ADR-0001: Application and deployment

**Date:** 2026-11-23  
**Status:** Accepted  
**Decider:** Technical architect

## Context

Two household members need a maintainable meal planner with private data. An unused Raspberry Pi 4 with 8 GB of RAM is available for a temporary, home-only proof of concept. A more secure cloud deployment is intended later.

## Decision

Use a modular Next.js application with React and TypeScript and a PostgreSQL database. Run the initial proof of concept on the Pi, accessible from the home network. The application server owns authorization, business rules and database operations. Organize capabilities as logical modules in one application.

## Alternatives considered

- **TanStack Start:** A viable full-stack React framework with a different routing and rendering model. Next.js was preferred for this project.
- **Separate frontend and backend services:** Independent deployment is possible, but adds operational and integration complexity without a current need.
- **Cloud hosting immediately:** Reduces local infrastructure responsibility but introduces costs and hosting decisions before the proof of concept.

## Consequences

One application and a small initial audience keep the deployment manageable. The household operates and maintains the Pi during the proof of concept. Cloud hosting, production availability, network exposure and operational ownership require separate decisions before the move.

## Open details

Select the deployment and development workflow, local DNS and HTTPS approach, and cloud provider. HTTPS is needed for sign-in from other devices on the home network.
