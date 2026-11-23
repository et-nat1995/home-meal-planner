# Family Meal Planner — Product Requirements

**Status:** Draft for discussion  
**Date:** November 22, 2026  
**Audience:** Family users and the development team

## 1. Product vision

Make the recurring question “What are we eating this week?” easier to answer. A family gathers recipes from its various sources into one consistent library. Every Sunday at noon in the household’s time zone, the app proposes Monday–Thursday dinners using that library and the family’s ratings, with room for future mood filters. The family adjusts and finalizes the plan, then gets a shopping list that accounts for shared ingredients. As plans change, the planner and list remain easy to correct. The same foundation should support planning further ahead for Shabbat and chagim without forcing every dish to have a complete recipe.

This document proposes a starting point, not settled product policy. Open questions and stack choices need confirmation before implementation.

## 2. People and outcomes

- **Initial users:** the user and his wife, who need to collect trusted recipes, plan dinners, and shop from a reliable list. Their household plans and shopping lists are private.
- **Potential guests:** visitors who can eventually browse the public recipe library without signing in. Public access must never expose household plans, shopping lists, ratings, or other private household information.
- **Success:** the family can go from its recipe collection to an editable four-day plan and a usable shopping list without manually recomputing shared ingredients.

Proposed validation: use the planner for four consecutive weeks; record how long planning takes, how often the generated list needs quantity corrections, and which imports require substantial editing. Establish a baseline before setting numerical targets.

## 3. Scope and priorities

### First usable release

1. Create, edit, search, and archive family recipes, including manually entered recipes.
2. Show a seven-day week with automatic Shabbat and Jewish holiday identification; plan dinner Monday through Thursday and manually assign, replace, or clear a meal.
3. Automatically prepare a draft plan each Sunday from eligible library recipes, using family ratings where available. Provide the groundwork for future editable characteristics and weighted filters; the exact scoring design is deferred. Let users review and change the draft before finalizing.
4. Generate a ready-to-use shopping list from the finalized plan, combine compatible ingredient quantities, and let users mark what they already have, organize what remains by shopping category, and check items off while shopping.
5. Provide a secure AI-assisted recipe intake path and manual entry so source material can become reviewable, structured recipe records. The exact integration, supported source types, and rollout order require design; do not assume access to prior conversations or direct database access.
6. Support only the two authorized household members for private features, with Google sign-in as the preferred direction. No household invitations. A public, read-only recipe library is a later feature.

AI-assisted intake is desired, but its integration and supported sources need investigation after the manual recipe → plan → list vertical slice. Manual entry remains a fallback, and extracted data must be reviewed before saving.

### Later / discovery

- Shabbat and chagim: automatic calendar identification is in the MVP; detailed planning of meals, guests, dishes, and advance preparation comes later.
- A public, unauthenticated, read-only recipe library is desired later; private household data must never become public. Publication scope, source rights, and presentation need design. Broader multiple-household support, dietary and kosher rules, pantry inventory, grocery delivery, nutrition, and automated scheduling are uncommitted.

## 4. Core journeys and requirements

### Recipe library

Each imported or manually entered recipe is stored in the app in one consistent, editable format, with its ingredients and cooking steps available directly from the weekly plan; a source link or image alone is not the recipe. A recipe has a title, ingredient lines, ordered instructions, optional yield/servings, notes, tags, and source information. Ingredients may include quantity, unit, ingredient name, and preparation notes; retain the original text as well. Support incomplete entries and recipes with no exact quantities, visibly marked as needing attention. Search by name and tags; archive without breaking historical plans. Record an adult rating of 1–5 (1 is worst) and a kids’ yes/no like rating, with unrated distinct from disliked. Whether these are collective or recorded per individual remains open. Design for editable planning characteristics such as ease, speed, comfort, luxury, and something new, and future at-a-glance indicators in the library. Their taxonomy, weights, UI, and use in suggestions are follow-up design work, not required fully implemented MVP features. When a recipe is edited, active plans using it show the updated recipe and recalculate affected shopping contributions; visibly flag changed quantities and affected user adjustments. Preserve historical weeks as records rather than rewriting past plans.

**Acceptance:** a user can import, review, and save a complete recipe, record its adult and kids’ ratings. The design can accommodate future planning indicators in the library. The user can open its ingredients and steps from a planned dinner without returning to its original source. Editing it updates active plans and flags affected list changes; archiving leaves planned meals and past weeks readable.

### Import and review

- **Images:** upload cookbook photos, handwritten recipes, or screenshots and extract recipe text into a draft. Let the user correct title, ingredients, quantities, and instructions; flag unreadable or missing fields. Handle multiple recipes or pages through review or manual entry; do not silently merge them. Handwriting quality and image legibility may limit extraction.
- **Link:** accept a public recipe URL, attempt to extract recipe content, and present the source and editable draft. Sites may block retrieval, lack structured data, or have access restrictions; show a useful error and manual fallback.
- **YouTube videos:** support is desired for recipes found in videos. Investigate available descriptions, transcripts, and supported access methods; ingredient quantities or instructions may be incomplete or unavailable. Present a reviewable draft when feasible and offer manual entry. Delivery timing and any video processing costs remain open.
- **Existing assistant recipes:** allow pasting text or entering it manually. Any future connected conversation import requires an available integration and explicit user selection; it is not assumed for this release.
- **Secure AI funnel:** explore an integration that lets an authorized assistant submit structured recipe drafts through an authenticated, narrowly scoped server endpoint. Only the two household members or an explicitly authorized integration may write; no public write access or database credentials exposed to a browser or assistant. Integration authorization, revocation, audit, and review-before-save must be designed; availability of a particular assistant connection is unconfirmed.
- Save only after review; preserve the source URL or photo reference when the user chooses to retain it. Users can remove retained images. Respect source access and copyright constraints, and avoid republishing source content.

**Acceptance:** a user can review and correct an extracted draft before saving; failed extraction never creates a misleading completed recipe.

### Weekly planning

A week is identified by its local Monday date. Show one recipe or free-form dinner entry for each Monday–Thursday slot. Multiple recipe dishes per dinner and automatic serving scaling are later features; use the recipe’s initially entered quantities for the MVP. Each recipe dinner links to its stored ingredients and steps. Users can assign an existing recipe, replace or remove a planned item, or replace it with a flexible note such as leftovers, eating out, or adults going out while the kids have pizza. A free-form note such as “kids have pizza” does not create shopping ingredients automatically; the family can add them manually. Show all seven days and automatically identify Shabbat and Jewish holidays, including holidays that fall on weekdays. Shabbat should be visible for Friday evening and Saturday; label holiday dates and the preceding evening so the meal-planning view does not suggest that an occasion begins at midnight. Identify overlapping and multi-day occasions without hiding either label. Detailed occasion planning is future work. Maintain previous weeks for reference.

**Confirmed lifecycle decision:** A week remains editable until 12:00 a.m. on the following Monday in the household’s configured time zone. It then becomes historical and read-only. Preserve its plan, recipe content, and shopping-list state as they stood at that cutoff; later recipe edits must not rewrite them. Future weeks remain editable before their own cutoff.

**Sunday workflow:**

1. On Sunday at noon in the household’s configured time zone, automatically create one draft for the upcoming Monday–Thursday week. Let either household member open the draft, see the recipes for each night, and make changes. Notifications are undecided; the draft remains available on the website.
2. Use the adult 1–5 and kids’ yes/no ratings when available, without inventing unknown ratings. Leave room for editable characteristics and weighted weekly filters such as ease, speed, comfort, luxury, and novelty, but defer the detailed scoring and filter feature. How conflicting ratings are balanced needs design.
3. Replace individual dinners, add notes, or lock choices before regenerating. Avoid repeats within the proposed week when enough candidates exist. If too few recipes are available, fill what is possible and explain the gap. Users can always plan manually.
4. When satisfied, explicitly finalize the plan and generate its shopping list. Finalization does not prevent later changes: allow an override, update affected calculated list contributions, and clearly flag items needing review.

Suggestions draw exclusively from the household’s own non-archived recipe library; they never discover or add outside recipes. The family curates the library; do not infer dietary or kosher restrictions. Automatic generation must be safe to retry, create no duplicate weekly drafts, and never overwrite a finalized plan or the family’s edits. If it fails or has insufficient input, show the issue and allow manual generation or planning.

**Acceptance:** on Sunday at noon local time, a draft for the coming week appears without manual initiation; each selected recipe opens its stored ingredients and steps. Kids’ and adults’ ratings can influence suggestions, while missing preferences remain unknown; future characteristics and filters can be added without redesigning the library. Either user can change a day or regenerate without losing locked choices. Finalizing produces the shopping list, and a later override updates it while preserving user changes and flagging affected items.
### Shopping list

Generate the ready-to-use list when the family finalizes the week, from its current planned recipes and their entered quantities. A draft may show a preview, but it must be clearly distinguished from the finalized list. Each ingredient line retains its source meal, original quantity and unit, and a normalized ingredient identity where known. Sum quantities only when ingredient identity and units are compatible; convert within a supported, explicit unit family (for example, teaspoons to tablespoons) using exact or documented conversions. Do not automatically convert volume to weight, merge ambiguous names, or invent quantities. Show unresolved lines separately for review, including “to taste” or missing quantities. Support fractional quantities and round only for display, preserving underlying precision.

Start with all ingredients needed, then let the family mark whole items already on hand or manually edit a quantity; if what is on hand is insufficient, they can leave the full amount to buy. Keep that review state distinct from items checked off during shopping. Group the list into Protein, Carb, Dairy, Veg, Fruit, and Misc; allow a user to correct an item’s category and default uncertain items to Misc. Do not require pantry tracking, automated partial-quantity subtraction, or package-size calculations.

A generated list separates calculated contributions from user additions and overrides. Users can add an item, edit or exclude a calculated item, and check items off. When a meal changes, recalculate calculated contributions, preserve manual items, and flag overrides or check-offs affected by changed quantities for review rather than silently discarding them. Display which meals contribute to an item. Persistent pantry inventory and package-size rounding are future decisions, not assumptions.

**Confirmed update decision:** Changes to recipes used in editable weeks and changes to finalized plans automatically update affected calculated shopping-list contributions. When a quantity change affects an on-hand or checked-off item, retain its previous state for context and mark it as needing review; do not present it as conclusively covered or purchased until a household member resolves the review. How overrides and review states behave when groups merge or split remains open.

**Acceptance examples:**

- Two recipes call for 1 cup and 1/2 cup of the same normalized ingredient: show 1 1/2 cups, with both sources.
- One calls for 1 cup and another for 100 g of the same ingredient: keep separate unless a trustworthy ingredient-specific conversion is explicitly supplied.
- “2 onions” and “1 onion” may combine only when their units and normalized identity match; “onion powder” remains separate.
- Replacing a meal or editing a planned recipe updates calculated contributions while preserving a manually added item and flagging affected on-hand, checked, or overridden items.
- An ingredient marked as already on hand is distinguishable from an item checked off at the store, and remaining items appear in their shopping categories.

### Shabbat and chagim direction

The MVP weekly view automatically identifies Shabbat and chagim, while detailed occasion planning comes after the Monday–Thursday MVP. The family expects eventually to plan meals, guests, dishes, and advance preparation for Shabbat and chagim. The exact workflow remains to be worked out with real examples. The data model should allow arbitrary dates, named occasions, multiple meal slots (for example Friday dinner and Saturday lunch), courses, guests/servings, and free-form dishes or tasks without a library recipe. A dish may have shopping ingredients, preparation notes, both, or neither. A future prep timeline may link a task to an occasion and intended preparation date. Use the household’s local time zone and a configurable Israel or diaspora holiday schedule; diaspora is a proposed default for the initial household, subject to confirmation. Decide which minor and modern observances to display during design. Proposed addition: allow the household to enter a US ZIP code or choose a location to display location-based candle-lighting and Shabbat or holiday ending times alongside relevant events. Show the resolved location, time zone, source, and calculation convention; allow the household to configure applicable candle-lighting and ending-time conventions. Confirm scope and defaults before implementation, including consecutive Shabbat/holiday transitions. Until a valid location and convention are set, show event labels without inventing times; if lookup fails, show a clear unavailable state. Location-derived times are approximate and depend on local practice. Do not infer observance restrictions or automatically suppress dinner suggestions solely from a calendar label without a product decision. Consider [Hebcal’s calendar API or JavaScript library](https://www.hebcal.com/home/developer-apis), subject to integration and attribution requirements. If calendar data is unavailable, make that visible rather than implying there are no holidays.

**MVP acceptance:** navigating any seven-day week shows Shabbat on Friday evening and Saturday, identifies relevant holidays across weekdays and multi-day or overlapping dates, and uses the configured Israel or diaspora schedule. A calendar failure is visible; ordinary meal planning remains usable.

**Future acceptance:** a user can plan an occasion in advance, include a dish with no recipe, and add its known ingredients to the occasion's shopping list without fabricating a recipe.

## 5. Data and technical direction

Recommended starting architecture: a responsive web application with a single backend and relational database, deployed as one maintainable product. Next.js with TypeScript and PostgreSQL is the recommended starting point for public recipe pages and private server-backed planning, subject to an architecture decision before coding. TanStack Start is a credible full-stack alternative; framework choice does not determine database or hosting. Use database migrations and transactions for plan/list updates. Store uploaded photos in private object storage with references in the database, not directly in public URLs. Prefer Google sign-in for two specifically authorized household accounts; no invitation flow for the private household. Verify identity on the server and restrict access using stable account identifiers; decide secure initial provisioning and recovery before launch. Protect routes and enforce membership on the server for every data operation; hiding pages in the frontend is insufficient. Design a separate public, read-only recipe projection for future sharing, with private household data excluded; publication scope remains to be decided. Choose hosting, framework, authentication provider, OCR/extraction service, and budget after discussing deployment preferences and expected use.

Proposed core entities:

- **User, household, and membership:** separate identities and a private ownership/access boundary for the two initial users; extensible to more.
- **Public recipe projection (future):** unauthenticated read-only recipe pages, separated from private household data; publication scope remains undecided.
- **Recipe, recipe ingredient, source:** editable library record and original source; archive instead of deleting referenced data.
- **Recipe preference, characteristics, and weekly planning input:** adult 1–5 and kids’ yes/no ratings; unknown ratings stay unknown. Extensible characteristics and future weekly filters; weighting is open.
- **Plan, meal slot, planned item:** dated slots, recipe references or free-form items, servings, and lock state; plan lifecycle includes draft and finalized. Active plans follow recipe updates; retain historical versions for past weeks.
- **Calendar settings:** household time zone and Israel/diaspora schedule; proposed optional location (US ZIP code or place), resolved time zone, and candle-lighting and ending-time conventions. Location belongs to the private household; expose it to a calendar provider only as needed.
- **Scheduled generation:** a time-zone-aware Sunday noon job with retry/idempotency and failure visibility; must preserve household edits and finalized plans.
- **Shopping list, calculated contribution, manual item, category, on-hand and shopping check state:** derived ingredient contributions and user changes kept distinct.
- **Import draft and asset:** extraction status, editable result, source, and optional retained image; failed or abandoned drafts can be removed.
- **Occasion and prep task:** future extensions linked to dates and meal slots.

The backend owns access checks, persistence, import orchestration, ingredient normalization and arithmetic. The frontend presents drafts, uncertainties, and conflict resolution. Start with deterministic ingredient matching and explicit conversions; AI extraction can propose structured data but must not be treated as authoritative. Recompute lists from stored contributions, not from rounded display values.

## 6. Reliability, privacy, and operations

- Restrict household recipes, plans, images, and lists to authorized household members by default. Future public recipe pages must expose only intended recipe fields, never plans, shopping lists, private ratings, or raw source assets. Define account provisioning, recovery, and shared-device behavior before launch.
- Validate uploads and URLs, limit size and processing time, and protect server-side fetching against access to internal or private network addresses. Treat imported content as untrusted.
- Explain when photos or recipe text are sent to a third-party extraction provider; select retention and deletion policies before enabling it. Avoid logging recipe images or sensitive account data.
- Provide clear error states and retry/manual paths for unavailable imports. Save edits durably and avoid losing work on refresh or errors.
- Back up the database and retained images, verify restore, and provide a way to export or delete household data. Define backup retention and recovery targets with the hosting choice.
- Support mobile and desktop, keyboard navigation, readable contrast, and accessible form labels. Make the main planning and shopping flows usable on a phone.
- Record operational errors and import outcomes without logging private recipe content. Decide acceptable availability, storage limits, and provider costs before launch.

## 7. Delivery milestones

1. **Foundation:** resolve product questions and record architecture decisions; set up application, database, auth, and deployment approach.
2. **Vertical slice:** manual recipes → manually editable weekly plan → accurate, traceable shopping list. Verify core arithmetic and update behavior with automated tests.
3. **Suggestions and intake:** add ratings, scheduled Sunday noon draft generation, finalization, locks, and a secure AI-assisted intake path with review and manual fallback. Investigate source support and integration feasibility; defer detailed weighted filters.
4. **Household trial:** use for several weeks, collect corrections and friction, and prioritize improvements.
5. **Occasion planning:** design Shabbat and chagim workflows with the family based on actual examples before implementation.

Each milestone should be reviewable and committed at a meaningful boundary. No production launch is implied by this PRD.

## 8. Decisions to discuss

1. Are the adult and kids’ ratings collective, or should each person have a separate rating using those scales?
2. How should the two Google identities be provisioned and recovered safely, and which service should handle sign-in?
3. Which AI intake integration can actually be connected securely, and which sources should it support first? How should recipe drafts be reviewed?
4. Which recipe characteristics and weighted filters should be added later, and how should conflicting ratings affect suggestions?
5. Confirm the Israel or diaspora holiday schedule and which minor or modern observances should appear. Should holiday labels affect dinner suggestions, or remain informational? Confirm whether location-based candle-lighting and ending times are needed for the MVP, and which timing conventions the family follows.
6. Which hosting, database, scheduler, and AI providers meet the desired low cost and custom-domain needs? Should Sunday generation trigger a notification?
7. When the public library is prioritized, which recipes and fields should be published, and how should source rights be handled?

## 9. Handoff

The product concept and first delivery slice are ready for discussion. Resolve the implementation-critical questions above and choose hosting, authentication, and the intake integration before implementation. Start with the manual vertical slice so shopping arithmetic and plan changes can be validated with real recipes; then add imports and occasion workflows.
