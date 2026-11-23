# Family Meal Planner — Product Requirements

**Status:** Draft for discussion  
**Date:** November 22, 2026  
**Audience:** Family users and the development team

## 1. Product vision

Make the recurring question “What are we eating this week?” easier to answer. A family collects meals it likes, chooses or generates a Monday–Thursday plan, and gets a shopping list that accounts for shared ingredients. As plans change, the planner and list remain easy to correct. The same foundation should support planning further ahead for Shabbat and chagim without forcing every dish to have a complete recipe.

This document proposes a starting point, not settled product policy. Open questions and stack choices need confirmation before implementation.

## 2. People and outcomes

- **Initial users:** the user and his wife, who need to collect trusted recipes, plan dinners, and shop from a reliable list. Their household plans and shopping lists are private.
- **Potential guests:** people with whom the family may choose to share recipes. Recipe sharing must not grant access to household plans, shopping lists, or other private household information.
- **Success:** the family can go from its recipe collection to an editable four-day plan and a usable shopping list without manually recomputing shared ingredients.

Proposed validation: use the planner for four consecutive weeks; record how long planning takes, how often the generated list needs quantity corrections, and which imports require substantial editing. Establish a baseline before setting numerical targets.

## 3. Scope and priorities

### First usable release

1. Create, edit, search, and archive family recipes, including manually entered recipes.
2. Plan dinner Monday through Thursday for a chosen week; manually assign, replace, clear, or add a meal to a day.
3. Suggest a plan from eligible recipes while keeping the user in control. A suggestion is a draft until accepted.
4. Generate a shopping list from the current plan, combine compatible ingredient quantities, and allow corrections, additions, and check-offs.
5. Import recipe drafts from website links and images (cookbooks, handwritten recipes, or screenshots), with a review step before saving. Provide paste/manual entry for recipes previously discussed with an assistant; do not assume access to prior conversations.
6. Support two household members with authenticated access and persist data across visits. Keep household data private. Explore sharing selected recipes separately from the private family space.

The import methods are part of the desired first release, but can be delivered after the manual recipe → plan → list vertical slice. If extraction proves unreliable or costly, ship a clear manual fallback rather than silently saving incomplete recipes.

### Later / discovery

- Shabbat and chagim: multiple meals and courses, advance preparation, and dishes without a recipe. First understand the household's observance, calendar, and workflow; no automatic religious rules are assumed.
- Selective recipe sharing and the recipient experience need design; no household data should become shareable by default. Broader multiple-household support, dietary and kosher rules, pantry inventory, grocery delivery, nutrition, and automated scheduling are uncommitted.

## 4. Core journeys and requirements

### Recipe library

A recipe has a title, ingredient lines, instructions, optional yield/servings, notes, tags, and source information. Ingredients may include quantity, unit, ingredient name, and preparation notes; retain the original text as well. Support incomplete entries and recipes with no exact quantities, visibly marked as needing attention. Search by name and tags; archive without breaking historical plans. Editing a recipe must not silently change a previously accepted plan or its shopping list: planned meals use an editable snapshot, with an explicit future option to refresh it.

**Acceptance:** a user can add a family recipe, find and edit it, and archive it while an existing planned meal remains readable.

### Import and review

- **Images:** upload cookbook photos, handwritten recipes, or screenshots and extract recipe text into a draft. Let the user correct title, ingredients, quantities, and instructions; flag unreadable or missing fields. Handle multiple recipes or pages through review or manual entry; do not silently merge them. Handwriting quality and image legibility may limit extraction.
- **Link:** accept a public recipe URL, attempt to extract recipe content, and present the source and editable draft. Sites may block retrieval, lack structured data, or have access restrictions; show a useful error and manual fallback.
- **YouTube videos:** support is desired for recipes found in videos. Investigate available descriptions, transcripts, and supported access methods; ingredient quantities or instructions may be incomplete or unavailable. Present a reviewable draft when feasible and offer manual entry. Delivery timing and any video processing costs remain open.
- **Existing assistant recipes:** allow pasting text or entering it manually. Any future connected conversation import requires an available integration and explicit user selection; it is not assumed for this release.
- Save only after review; preserve the source URL or photo reference when the user chooses to retain it. Users can remove retained images. Respect source access and copyright constraints, and avoid republishing source content.

**Acceptance:** a user can review and correct an extracted draft before saving; failed extraction never creates a misleading completed recipe.

### Weekly planning

A week is identified by its local Monday date. Show Monday–Thursday dinner slots by default, with room for more than one planned item per slot. Users can assign an existing recipe, replace or remove a planned item, or replace it with a flexible note such as leftovers, eating out, or adults going out while the kids have pizza. A dinner can combine a free-form note with recipe items when needed; only ingredients explicitly associated with that dinner contribute to shopping. A change is saved and reflected in the shopping list. Maintain previous weeks for reference.

Suggestions choose exclusively from the household’s own non-archived recipe library, with enough information to plan; they never discover or add outside recipes; avoid repeats within the same proposed week when enough candidates exist. Offer individual replacements and preserve user-locked choices when regenerating. If too few recipes exist, fill what is possible and explain the gap. The family curates the library itself; do not infer dietary or kosher restrictions or introduce external recipes. Additional preferences and variety rules remain open.

**Acceptance:** generating, accepting, and overriding a day changes only the intended planned items; locked meals stay in place; the user can always plan manually.

### Shopping list

Generate a list from the current accepted planned meal snapshots and selected servings. Each ingredient line retains its source meal, original quantity and unit, and a normalized ingredient identity where known. Sum quantities only when ingredient identity and units are compatible; convert within a supported, explicit unit family (for example, teaspoons to tablespoons) using exact or documented conversions. Do not automatically convert volume to weight, merge ambiguous names, or invent quantities. Show unresolved lines separately for review, including “to taste” or missing quantities. Support fractional quantities and round only for display, preserving underlying precision.

A generated list separates calculated contributions from user additions and overrides. Users can add an item, edit or exclude a calculated item, and check items off. When a meal changes, recalculate calculated contributions, preserve manual items, and flag overrides or check-offs affected by changed quantities for review rather than silently discarding them. Display which meals contribute to an item. Pantry subtraction and package-size rounding are future decisions, not assumptions.

**Acceptance examples:**

- Two recipes call for 1 cup and 1/2 cup of the same normalized ingredient: show 1 1/2 cups, with both sources.
- One calls for 1 cup and another for 100 g of the same ingredient: keep separate unless a trustworthy ingredient-specific conversion is explicitly supplied.
- “2 onions” and “1 onion” may combine only when their units and normalized identity match; “onion powder” remains separate.
- Replacing a meal updates calculated contributions while preserving a manually added item and flagging affected checked items or overrides.

### Shabbat and chagim direction

The family expects to plan meals, guests, dishes, and advance preparation for Shabbat and chagim. The exact workflow remains to be worked out with real examples. The data model should allow arbitrary dates, named occasions, multiple meal slots (for example Friday dinner and Saturday lunch), courses, guests/servings, and free-form dishes or tasks without a library recipe. A dish may have shopping ingredients, preparation notes, both, or neither. A future prep timeline may link a task to an occasion and intended preparation date. Treat holiday dates, local time zones, observance practices, and calendar integrations as unresolved; no automatic calendar or halachic guidance is promised in the first release.

**Future acceptance:** a user can plan an occasion in advance, include a dish with no recipe, and add its known ingredients to the occasion's shopping list without fabricating a recipe.

## 5. Data and technical direction

Recommended starting architecture: a responsive web application with a single backend and relational database, deployed as one maintainable product. A TypeScript/React frontend with a server-side TypeScript application and PostgreSQL is a reasonable default, subject to a short architecture decision before coding. Use database migrations and transactions for plan/list updates. Store uploaded photos in private object storage with references in the database, not directly in public URLs. Use separate authenticated identities for the two household members as the proposed default, with email-based sign-in or invitations to be chosen during architecture design. Protect routes and enforce membership on the server for every data operation; hiding pages in the frontend is insufficient. Model recipe sharing as explicit, scoped access separate from household membership. Choose hosting, framework, authentication provider, OCR/extraction service, and budget after discussing deployment preferences and expected use.

Proposed core entities:

- **User, household, and membership:** separate identities and a private ownership/access boundary for the two initial users; extensible to more.
- **Recipe sharing grant or share link (future):** explicitly scoped access to selected recipes, separate from household membership; sharing mechanism and permissions remain undecided.
- **Recipe, recipe ingredient, source:** editable library record and original source; archive instead of deleting referenced data.
- **Plan, meal slot, planned item:** dated slots and recipe snapshots or free-form items, with servings and lock state.
- **Shopping list, calculated contribution, manual item, override/check state:** derived ingredient contributions and user changes kept distinct.
- **Import draft and asset:** extraction status, editable result, source, and optional retained image; failed or abandoned drafts can be removed.
- **Occasion and prep task:** future extensions linked to dates and meal slots.

The backend owns access checks, persistence, import orchestration, ingredient normalization and arithmetic. The frontend presents drafts, uncertainties, and conflict resolution. Start with deterministic ingredient matching and explicit conversions; AI extraction can propose structured data but must not be treated as authoritative. Recompute lists from stored contributions, not from rounded display values.

## 6. Reliability, privacy, and operations

- Restrict household recipes, plans, images, and lists to authorized household members by default. Any future recipe share must expose only explicitly selected recipe content, never plans or shopping lists. Define authentication, invitations, link revocation, and shared-device behavior before launch.
- Validate uploads and URLs, limit size and processing time, and protect server-side fetching against access to internal or private network addresses. Treat imported content as untrusted.
- Explain when photos or recipe text are sent to a third-party extraction provider; select retention and deletion policies before enabling it. Avoid logging recipe images or sensitive account data.
- Provide clear error states and retry/manual paths for unavailable imports. Save edits durably and avoid losing work on refresh or errors.
- Back up the database and retained images, verify restore, and provide a way to export or delete household data. Define backup retention and recovery targets with the hosting choice.
- Support mobile and desktop, keyboard navigation, readable contrast, and accessible form labels. Make the main planning and shopping flows usable on a phone.
- Record operational errors and import outcomes without logging private recipe content. Decide acceptable availability, storage limits, and provider costs before launch.

## 7. Delivery milestones

1. **Foundation:** resolve product questions and record architecture decisions; set up application, database, auth, and deployment approach.
2. **Vertical slice:** manual recipes → manually editable weekly plan → accurate, traceable shopping list. Verify core arithmetic and update behavior with automated tests.
3. **Suggestions and imports:** add draft generation, locks, image and website import, and explicit failure/review paths. Investigate YouTube import feasibility and timing. Test with representative real family recipes and images.
4. **Household trial:** use for several weeks, collect corrections and friction, and prioritize improvements.
5. **Occasion planning:** design Shabbat and chagim workflows with the family based on actual examples before implementation.

Each milestone should be reviewable and committed at a meaningful boundary. No production launch is implied by this PRD.

## 8. Decisions to discuss

1. How should the two household members sign in and join the private household: email magic links, passwords, or another method? How should invitations and recovery work?
2. For sharing, should selected recipes be accessible by a private link, by invited email, or publicly? Should recipients be able to copy or edit them? When is sharing needed?
3. Should a dinner support serving-size changes and multiple recipe dishes in the first release? How should an unquantified note such as “kids have pizza” appear on the shopping list?
4. Should shopping lists assume an empty pantry, and how should staples, package sizes, and user quantity overrides behave?
5. What are one or two actual Shabbat or chag examples, including meals, guests, dishes, dates, and advance preparation?
6. Can representative cookbook photos, handwriting, screenshots, website links, YouTube videos, and pasted assistant recipes be supplied for import evaluation? Is video import required for the first release?
7. What are the preferences for hosting, ongoing cost, data location, and AI providers?
8. Is editing a recipe after planning intended to update existing plans, or is the proposed snapshot behavior preferable?

## 9. Handoff

The product concept and first delivery slice are ready for discussion. Confirm the questions above and choose the stack, hosting, authentication, and extraction providers before implementation. Start with the manual vertical slice so shopping arithmetic and plan changes can be validated with real recipes; then add imports and occasion workflows.
