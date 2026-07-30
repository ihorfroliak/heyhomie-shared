# PRODUCT.md — HeyHomie, one vision

## What HeyHomie is
A **cleaning marketplace for the Polish market** (heyhomie.io). Customers book home cleaning
(standard / general) plus adjacent services (windows, kitchen/bathroom deep-clean, upholstery,
office); vetted "homies" fulfil the visit; **payment is always taken after the service**, not at
booking. Cleaning is the **#1 service** and the growth wedge.

## Positioning
- **Beat CleanWhale** (the incumbent) on transparency + UX: a calculator that shows the exact price
  and time before booking, an honest "what we don't do" list before the order (not buried in terms),
  a binding room-by-room checklist the worker ticks and the customer sees afterwards.
- **Local SEO** as the acquisition engine — city pages (Kraków first, then Warsaw, Wrocław, Poznań,
  Katowice, Rzeszów), Polish-locative content, schema.org, no fake reviews.
- Trust levers: pay-after-service, free cancellation ≤24 h, insured visits, pre-declared fragile items.

## The four surfaces (and their repos)
| Surface | Repo | Stack | Who uses it |
|---|---|---|---|
| **Client web** (heyhomie.io) | `Projects/heyhomie-web/heyhomie-client` | Next.js 10, Pages Router, React 17, twin.macro, next-intl, Redux, Stripe | customers (booking + SEO) |
| **Mobile — client** | `Projects/heyhomie-mobile/apps/client` | Expo / RN, expo-router | customers on phone |
| **Mobile — worker** | `Projects/heyhomie-mobile/apps/worker` | Expo / RN | homies (jobs, check-in/out, checklist) |
| **Mobile — admin** | `Projects/heyhomie-mobile/apps/admin` | Expo / RN | ops (orders, homies, payouts) |
| Backend / platform | `Projects/homies` (+ `heyhomie-mobile/server` Fastify+pg) | — | orders, auth, payments |

The three mobile apps share `packages/{domain,api,ui,design}`; all order state flows through the
**frozen `OrderGateway` contract** (`packages/api/orderContract.ts`) — see the mobile repo's
`docs/INDEX.md`. The web site is a separate stack but must show the **same prices, rules and brand**
— that is what this shared core enforces.

## Languages
- Web: **pl** (default, unprefixed) + **en** (`/en`). Fixed URL architecture — do not renegotiate.
- Mobile: **pl / en / uk**.
- Source copy is Polish; the cleaning calculator/checklist strings are canon in DOMAIN_RULES.

## Locked decisions (do not re-litigate without owner sign-off)
- **Pay after service** (nightly settlement, card auto-charge or Stripe pay-later link). Never charge at booking.
- **Price is per-room flat**, not per-m² and not per-hour to the customer (area is informational). See DOMAIN_RULES §1/§4.
- **Kitchen + hallway always in the base**; second kitchen is a hand-priced note.
- **Type drives the add-on list** (general folds 7 add-ons into the base).
- **Free cancellation ≤ 24 h**; insured visits; fragile items declared up front.
- **Frozen OrderGateway contract** on mobile — no change without a versioned major.
- Web URL architecture fixed (pl unprefixed, en `/en`, 6 cities, no IP redirects).

## Open product decisions
Tracked as `⚠ DECISION NEEDED` in DOMAIN_RULES (pricing-as-canon, add-on ids) and BRAND (which brand
is canon). Roadmap in ROADMAP.md.
