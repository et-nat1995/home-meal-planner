# Family Meal Planner — Product Requirements

**Status:** Draft for discussion  
**Date:** November 22, 2026  
**Audience:** Family users and the development team

## 1. Product vision

Make the recurring question “What are we eating this week?” easier to answer. A family gathers recipes from its various sources into one consistent library. Every Sunday at noon in the household’s time zone, the app proposes Monday–Thursday dinners using that library and the family’s preferences and current mood. The family adjusts and finalizes the plan, then gets a shopping list that accounts for shared ingredients. As plans change, the planner and list remain easy to correct. The same foundation should support planning further ahead for Shabbat and chagim without forcing every dish to have a complete recipe.

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
3. Automatically prepare a draft plan each Sunday from eligible library recipes, using known family preferences and the current week’s stated mood. Let users review and change it before finalizing.
4. Generate a ready-to-use shopping list from the finalized plan, combine compatible ingredient quantities, and let users mark what they already have, organize what remains by shopping category, and check items off while shopping.
5. Import recipe drafts from website links and images (cookbooks, handwritten recipes, or screenshots), with a review step before saving. Provide paste/manual entry for recipes previously discussed with an assistant; do not assume access to prior conversations.
6. Support two household members with authenticated access and persist data across visits. Keep household data private. Recipe sharing is a later feature.

The import methods are part of the desired first release, but can be delivered after the manual recipe → plan → list vertical slice. If extraction proves unreliable or costly, ship a clear manual fallback rather than silently saving incomplete recipes.

### Later / discovery

- Shabbat and chagim: the MVP weekly view accounts for these days or occasions, but detailed planning of meals, guests, dishes, and advance preparation comes later. No automatic religious calendar or rules are assumed.
- Selective recipe sharing and the recipient experience need design; no household data should become shareable by default. Broader multiple-household support, dietary and kosher rules, pantry inventory, grocery delivery, nutrition, and automated scheduling are uncommitted.

## 4. Core journeys and requirements

### Recipe library

Each imported or manually entered recipe is stored in the app in one consistent, editable format, with its ingredients and cooking steps available directly from the weekly plan; a source link or image alone is not the recipe. A recipe has a title, ingredient lines, ordered instructions, optional yield/servings, notes, tags, and source information. Ingredients may include quantity, unit, ingredient name, and preparation notes; retain the original text as well. Support incomplete entries and recipes with no exact quantities, visibly marked as needing attention. Search by name and tags; archive without breaking historical plans. Record separate kids’ and adults’ ratings for each recipe, with unrated distinct from disliked. Store planning characteristics such as ease, speed, comfort, luxury, and something new, and show useful indicators at a glance in the library. Exact scales and categories are design choices. When a recipe is edited, active plans using it show the updated recipe and recalculate affected shopping contributions; visibly flag changed quantities and affected user adjustments. Preserve historical weeks as records rather than rewriting past plans.

**Acceptance:** a user can import, review, and save a complete recipe, rate it for kids and adults, and see its planning indicators in the library. The user can open its ingredients and steps from a planned dinner without returning to its original source. Editing it updates active plans and flags affected list changes; archiving leaves planned meals and past weeks readable.

### Import and review

- **Images:** upload cookbook photos, handwritten recipes, or screenshots and extract recipe text into a draft. Let the user correct title, ingredients, quantities, and instructions; flag unreadable or missing fields. Handle multiple recipes or pages through review or manual entry; do not silently merge them. Handwriting quality and image legibility may limit extraction.
- **Link:** accept a public recipe URL, attempt to extract recipe content, and present the source and editable draft. Sites may block retrieval, lack structured data, or have access restrictions; show a useful error and manual fallback.
- **YouTube videos:** support is desired for recipes found in videos. Investigate available descriptions, transcripts, and supported access methods; ingredient quantities or instructions may be incomplete or unavailable. Present a reviewable draft when feasible and offer manual entry. Delivery timing and any video processing costs remain open.
- **Existing assistant recipes:** allow pasting text or entering it manually. Any future connected conversation import requires an available integration and explicit user selection; it is not assumed for this release.
- Save only after review; preserve the source URL or photo reference when the user chooses to retain it. Users can remove retained images. Respect source access and copyright constraints, and avoid republishing source content.

**Acceptance:** a user can review and correct an extracted draft before saving; failed extraction never creates a misleading completed recipe.

### Weekly planning

A week is identified by its local Monday date. Show Monday–Thursday dinner slots by default, with room for more than one planned item per slot. Each recipe dinner links to its stored ingredients and steps. Users can assign an existing recipe, replace or remove a planned item, or replace it with a flexible note such as leftovers, eating out, or adults going out while the kids have pizza. A dinner can combine a free-form note with recipe items; only ingredients explicitly associated with that dinner contribute to shopping. Show the full week so Shabbat and chagim have a place in the view; detailed occasion planning and automatic holiday identification are future work. Maintain previous weeks for reference.

**Sunday workflow:**

1. On Sunday at noon in the household’s configured time zone, automatically create one draft for the upcoming Monday–Thursday week. Let either household member open the draft, see the recipes for each night, and make changes. Notifications are undecided; the draft remains available on the website.
2. Use separate kids’ and adults’ recipe ratings and weighted recipe characteristics, such as ease, speed, comfort, luxury, and novelty, together with selected filters for the coming week. Show these characteristics in the library for quick recognition. Unknown ratings remain unknown; never invent preferences. If filters change after automatic generation, allow regeneration or individual replacements. Rating scales, whether ratings are per person or per group, exact weights, and conflict handling need design.
3. Replace individual dinners, add notes, or lock choices before regenerating. Avoid repeats within the proposed week when enough candidates exist. If too few recipes are available, fill what is possible and explain the gap. Users can always plan manually.
4. When satisfied, explicitly finalize the plan and generate its shopping list. Finalization does not prevent later changes: allow an override, update affected calculated list contributions, and clearly flag items needing review.

Suggestions draw exclusively from the household’s own non-archived recipe library; they never discover or add outside recipes. The family curates the library; do not infer dietary or kosher restrictions. Automatic generation must be safe to retry, create no duplicate weekly drafts, and never overwrite a finalized plan or the family’s edits. If it fails or has insufficient input, show the issue and allow manual generation or planning.

**Acceptance:** on Sunday at noon local time, a draft for the coming week appears without manual initiation; each selected recipe opens its stored ingredients and steps. Separate kids’ and adults’ ratings and selected recipe characteristics can influence suggestions, while missing preferences remain unknown. Either user can change a day or regenerate without losing locked choices. Finalizing produces the shopping list, and a later override updates it while preserving user changes and flagging affected items.
### Shopping list

Generate the ready-to-use list when the family finalizes the week, from its current planned recipes and selected servings. A draft may show a preview, but it must be clearly distinguished from the finalized list. Each ingredient line retains its source meal, original quantity and unit, and a normalized ingredient identity where known. Sum quantities only when ingredient identity and units are compatible; convert within a supported, explicit unit family (for example, teaspoons to tablespoons) using exact or documented conversions. Do not automatically convert volume to weight, merge ambiguous names, or invent quantities. Show unresolved lines separately for review, including “to taste” or missing quantities. Support fractional quantities and round only for display, preserving underlying precision.

Start with all ingredients needed, then let the family mark items or quantities already on hand so the remaining list represents what to buy. Keep that review state distinct from items checked off during shopping. Group the list into Protein, Carb, Dairy, Veg, Fruit, and Misc; allow a user to correct an item’s category and default uncertain items to Misc. Exact handling of partial quantities on hand can be refined during design.

A generated list separates calculated contributions from user additions and overrides. Users can add an item, edit or exclude a calculated item, and check items off. When a meal changes, recalculate calculated contributions, preserve manual items, and flag overrides or check-offs affected by changed quantities for review rather than silently discarding them. Display which meals contribute to an item. Persistent pantry inventory and package-size rounding are future decisions, not assumptions.

**Acceptance examples:**

- Two recipes call for 1 cup and 1/2 cup of the same normalized ingredient: show 1 1/2 cups, with both sources.
- One calls for 1 cup and another for 100 g of the same ingredient: keep separate unless a trustworthy ingredient-specific conversion is explicitly supplied.
- “2 onions” and “1 onion” may combine only when their units and normalized identity match; “onion powder” remains separate.
- Replacing a meal or editing a planned recipe updates calculated contributions while preserving a manually added item and flagging affected on-hand, checked, or overridden items.
- An ingredient marked as already on hand is distinguishable from an item checked off at the store, and remaining items appear in their shopping categories.

### Shabbat and chagim direction

The MVP weekly view makes room for Shabbat and chagim, while detailed occasion planning comes after the Monday–Thursday MVP. The family expects eventually to plan meals, guests, dishes, and advance preparation for Shabbat and chagim. The exact workflow remains to be worked out with real examples. The data model should allow arbitrary dates, named occasions, multiple meal slots (for example Friday dinner and Saturday lunch), courses, guests/servings, and free-form dishes or tasks without a library recipe. A dish may have shopping ingredients, preparation notes, both, or neither. A future prep timeline may link a task to an occasion and intended preparation date. Treat holiday dates, local time zones, observance practices, and calendar integrations as unresolved; no automatic calendar or halachic guidance is promised in the first release.

**Future acceptance:** a user can plan an occasion in advance, include a dish with no recipe, and add its known ingredients to the occasion's shopping list without fabricating a recipe.

## 5. Data and technical direction

Recommended starting architecture: a responsive web application with a single backend and relational database, deployed as one maintainable product. A TypeScript/React frontend with a server-side TypeScript application and PostgreSQL is a reasonable default, subject to a short architecture decision before coding. Use database migrations and transactions for plan/list updates. Store uploaded photos in private object storage with references in the database, not directly in public URLs. Use separate authenticated identities for the two household members as the proposed default, with email-based sign-in or invitations to be chosen during architecture design. Protect routes and enforce membership on the server for every data operation; hiding pages in the frontend is insufficient. Model recipe sharing as explicit, scoped access separate from household membership. Choose hosting, framework, authentication provider, OCR/extraction service, and budget after discussing deployment preferences and expected use.

Proposed core entities:

- **User, household, and membership:** separate identities and a private ownership/access boundary for the two initial users; extensible to more.
- **Recipe sharing grant or share link (future):** explicitly scoped access to selected recipes, separate from household membership; sharing mechanism and permissions remain undecided.
- **Recipe, recipe ingredient, source:** editable library record and original source; archive instead of deleting referenced data.
- **Recipe preference, characteristics, and weekly planning input:** separate kids’ and adults’ ratings, visible characteristics, and selected weekly filters; unknown ratings stay unknown. Exact rating scales and weighting are open.
- **Plan, meal slot, planned item:** dated slots, recipe references or free-form items, servings, and lock state; plan lifecycle includes draft and finalized. Active plans follow recipe updates; retain historical versions for past weeks.
- **Scheduled generation:** a time-zone-aware Sunday noon job with retry/idempotency and failure visibility; must preserve household edits and finalized plans.
- **Shopping list, calculated contribution, manual item, category, on-hand and shopping check state:** derived ingredient contributions and user changes kept distinct.
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
3. **Suggestions and imports:** add ratings and weighted filters, scheduled Sunday noon draft generation, finalization, locks, image and website import, and explicit failure/review paths. Investigate YouTube import feasibility and timing. Test with representative real family recipes and images.
4. **Household trial:** use for several weeks, collect corrections and friction, and prioritize improvements.
5. **Occasion planning:** design Shabbat and chagim workflows with the family based on actual examples before implementation.

Each milestone should be reviewable and committed at a meaningful boundary. No production launch is implied by this PRD.

## 8. Decisions to discuss

1. How should the two household members sign in and join the private household: email magic links, passwords, or another method? How should invitations and recovery work?
2. Should ratings be one rating per child and adult, or one collective kids’ rating and one collective adults’ rating? What scale feels natural?
3. Which recipe characteristics and weekly filters should be available initially, and how should their weights balance against family ratings? How should “something new” be identified?
4. Should a dinner support serving-size changes and multiple recipe dishes in the first release? How should a note such as “kids have pizza” appear on the shopping list?
5. How should partial quantities already on hand be entered, and should package sizes affect the list?
6. Can representative cookbook photos, handwriting, screenshots, website links, YouTube videos, and pasted assistant recipes be supplied for import evaluation? Is video import required for the first release?
7. What are the preferences for hosting, ongoing cost, data location, and AI providers? Should Sunday generation trigger a notification?
8. What should the full weekly view show for Shabbat and chagim before detailed planning exists? Later, use real examples to design meals, guests, dishes, and preparation.
9. When recipe sharing is prioritized later, should access be via private link or invited email, and what may recipients do?

## 9. Handoff

The product concept and first delivery slice are ready for discussion. Confirm the questions above and choose the stack, hosting, authentication, and extraction providers before implementation. Start with the manual vertical slice so shopping arithmetic and plan changes can be validated with real recipes; then add imports and occasion workflows.
