# heyhomie-shared — the one product core

**Single cross-repo source of truth for HeyHomie.** Product vision, brand tokens and
domain rules live here ONCE so the client web site and the three mobile apps share one
reality instead of three drifting copies.

## Who consumes this
| Surface | Repo | Reads this for |
|---|---|---|
| **Client web** (heyhomie.io) | `Projects/heyhomie-web/heyhomie-client` (Next.js 10, twin.macro, next-intl) | pricing, brand, copy, checklist |
| **Mobile client / worker / admin** | `Projects/heyhomie-mobile` (Expo RN monorepo; `packages/domain` + `packages/design`) | pricing, brand, domain rules |
| **Backend / platform** | `Projects/homies` | pricing, domain rules (booking/orders) |

## Files
- **[PRODUCT.md](PRODUCT.md)** — vision, positioning, the surfaces, locked decisions.
- **[DOMAIN_RULES.md](DOMAIN_RULES.md)** — the canonical cleaning rules: pricing table, frequency
  discounts, add-ons, included-in-general logic, cancellation, area, gear fee, other services,
  checklist. **This is the anti-divergence anchor.** Every calculator/booking flow must match it.
- **[BRAND.md](BRAND.md)** — design tokens (colour, type, spacing, radii). One palette both sides consume.
- **[ROADMAP.md](ROADMAP.md)** — per-surface status + what's next.

## Sync protocol (how this stays true)
1. **Read the relevant file here BEFORE changing pricing, brand, product copy or domain logic
   in any repo.** Each repo's `CLAUDE.md` points here.
2. **Change the rule HERE first**, then propagate to the consuming code. Never edit a price or
   token in one repo without updating this doc and the other surfaces.
3. Anything marked **`⚠ DECISION NEEDED`** is an unresolved divergence between surfaces — the
   owner (Ihor) resolves it; until then, treat the marked value as provisional.
4. When a rule moves from provisional → decided, delete the marker and record the choice.

## Status of this doc
Built 2026-07 from the real code of both sides:
- web numbers extracted from `HeyHomie Web - Cleaning Service v1.dc.html` (the Claude-Design canvas),
- mobile rules extracted from `packages/domain/{cleaning,catalog,scheduling,payment}.ts` and
  `packages/design/tokens.ts`.
Divergences are recorded honestly in DOMAIN_RULES §Reconciliation and BRAND §Reconciliation —
they are the two things that need a decision, not silent unification.

## Long-term (the strongest version)
Extract the domain rules as ONE code package (`@heyhomie/domain`, pure TS) imported by both the
web calculator and the mobile apps, so divergence becomes impossible at the code level. Until
then, **this doc is the contract** and each side mirrors the numbers. See ROADMAP.
