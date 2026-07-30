# ROADMAP.md — per-surface status + what's next

Compact, cross-surface. Detail lives in each repo's own state docs
(`heyhomie-mobile/docs/*`, `heyhomie-web/docs/website/WEBSITE_PROJECT_STATE.md`).

## Surface status
| Surface | State (2026-07) |
|---|---|
| **Client web** | Homepage + city SEO content live in repo (undeployed batch). **Next: rebuild `/cleaning` service page from the new design** (calculator + checklist + others + office) against DOMAIN_RULES. Repo has **no git yet (R1)** — git-init first. |
| **Mobile client/worker/admin** | Expo SDK **54** (upgraded from 51). Core loop + auth wired to the Fastify/pg backend; ~20 admin ops screens still demo. Recent audit fixes landed (auth CAS, async scrypt, secret guard, randomUUID, TZ, auth throttle) on branch `upgrade/sdk-54`. |
| **Backend / platform** (`homies` + `heyhomie-mobile/server`) | Fastify+pg orders backend (auth, tenancy, migrations, SSE). Real Stripe/email not yet wired. |

## Cross-surface next steps (in order)
1. ✅ **DONE — both decisions resolved (2026-07):** pricing table = canon; web design = brand canon.
2. ✅ **DONE — mobile token migration:** `packages/design/tokens.ts` values + font tokens → canon.
   **Pending:** bundle the actual fonts (`@expo-google-fonts/manrope` + Montserrat + `useFonts` in each
   `_layout`) so Manrope renders instead of the system fallback.
3. **Web `/cleaning` rebuild** against DOMAIN_RULES + BRAND (in-convention: twin.macro + next-intl pl/en; git-init first per web R1). ← **NEXT**
4. **Align mobile add-on ids** to the canonical set (DOMAIN_RULES §3/§11).
5. **Backend returns the canonical price table** (DOMAIN_RULES §1) so web + mobile show identical prices.

## The strongest long-term move
Extract **`@heyhomie/domain`** (pure TS: pricing, add-on/included logic, time estimate, cancellation)
as ONE package imported by the web calculator AND the mobile apps. Then a price/rule change is one
edit, impossible to diverge. Until then this doc set is the contract.

## How to update this file
When a surface changes state or a step completes, update the table/list here **and** the owning
repo's state doc. Keep it short — this is the cross-surface index, not the detail.
