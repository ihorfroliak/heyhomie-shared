# ROADMAP.md — per-surface status + what's next

Compact, cross-surface. Detail lives in each repo's own state docs
(`heyhomie-mobile/docs/*`, `heyhomie-web/docs/website/WEBSITE_PROJECT_STATE.md`).

## Surface status
| Surface | State (2026-07) |
|---|---|
| **Client web** | Under git (`heyhomie-web-update`, on `main`, undeployed). **`/cleaning` rebuilt from the new design** against DOMAIN_RULES + BRAND — live price calculator (canon engine), scope sections (included / exclusions / fragile / others), pl/en i18n, and a sessionStorage capture of the calculator selection. 5 clean commits (`a1084c2`→`11959ff`), isolated from the in-progress SEO WIP. City SEO content is separate WIP in the same repo. |
| **Mobile client/worker/admin** | Expo SDK **54** (upgraded from 51). Core loop + auth wired to the Fastify/pg backend; ~20 admin ops screens still demo. Recent audit fixes landed (auth CAS, async scrypt, secret guard, randomUUID, TZ, auth throttle) on branch `upgrade/sdk-54`. |
| **Backend / platform** (`homies` + `heyhomie-mobile/server`) | Fastify+pg orders backend (auth, tenancy, migrations, SSE). Real Stripe/email not yet wired. |

## Cross-surface next steps (in order)
1. ✅ **DONE — both decisions resolved (2026-07):** pricing table = canon; web design = brand canon.
2. ✅ **DONE — mobile token migration:** `packages/design/tokens.ts` values + font tokens → canon.
   **Pending:** bundle the actual fonts (`@expo-google-fonts/manrope` + Montserrat + `useFonts` in each
   `_layout`) so Manrope renders instead of the system fallback.
3. ✅ **DONE — mobile add-on ids** aligned to the canonical set (commit `5d0134e`, DOMAIN_RULES §3/§11).
4. ✅ **DONE — Web `/cleaning` rebuild** against DOMAIN_RULES + BRAND (`a1084c2`→`11959ff`):
   pure price engine (unit-tested 10/10 vs canon) · calculator widget · 4 scope sections · pl/en
   i18n (co-located dict — messages/*.json were WIP) · sessionStorage draft capture (helper 7/7).
   All eslint-clean; the `tw=`/`css=` props were avoided (not configured in this repo).
5. ✅ **DONE — canonical price table in the domain + served by the API** (mobile `a8fab5f`):
   `packages/domain/pricing.ts` is the authoritative TS source (matches DOMAIN_RULES §1–§7 + the web
   calculator; 17 unit assertions), and the Fastify server serves it at **public `GET /pricing/cleaning`**
   (live-test verified). The web `lib/cleaning/calculator.js` holds the same numbers and stays matched
   to this table until `@heyhomie/domain` is extracted as a shared package (see long-term move below).
6. **Mobile fonts** — apply the Manrope tokens for real. **Investigated (2026-07) + verified in the Expo
   web preview:** the two low-churn global hooks DO NOT work in this stack (RN 0.81 / React 19 / RN-web):
   - `Text.defaultProps` → **ignored by React 19** (fonts loaded via `useFonts`, but 0 elements computed to
     Manrope).
   - patching `Text.render` to inject the family → **crashes react-native-web** (`Failed to set indexed
     property on CSSStyleDeclaration` — the web render output already carries a resolved style object, not
     an RN style array) → white screen.
   So it needs the proper theming layer: a **shared `<Text>` wrapper in `packages/ui`** that every screen
   uses (real refactor — components currently import RN `Text` directly and set `fontWeight` but no
   `fontFamily`), plus multi-weight loading (google-fonts exposes `Manrope_400Regular`…`_800ExtraBold`).
   The attempt was reverted to a clean working state; `@expo-google-fonts/manrope`+`montserrat` are the
   right packages when this is done deliberately.

## `/cleaning` — remaining follow-ups (each needs an owner / environment, not blind edits)
- **Booking prefill (consume side)** — read `readCleaningDraft()` on `/[city]` when `selectedService=cleaning`
  and map it onto the order-session config, then `clearCleaningDraft()`. Needs the **backend services-config
  contract** + the **booking-flow owner** + touches the WIP `[city].js` + a **runtime env** to test. Write side
  is already live (isolated).
- **Live-render + component tests (web)** — blocked today: `_app.getInitialProps` fetches `/cities`, so the dev
  server needs the **backend up**. Verified so far by eslint + the pure-engine/helper unit tests only.
- ✅ **DONE — section prose translated (pl/en)** (web `8ae7fde`): exclusion bodies, room samples, fragile
  chips/notes and other-service names now render in EN on `/en`. Text moved to `cleaningCopy.js scope`
  (pl+en), numbers/ids stay in `cleaningContent.js`, index-aligned; verified by a node structural check (16/16).
  `/cleaning` is now fully bilingual. (Copy still lives in the co-located dict, not next-intl messages —
  migrate when the messages WIP lands.)
- **Polish** — `$`-transient styled-props (drop dev console warnings) · a full-checklist page · mobile Manrope
  font rendering (step 6).

## The strongest long-term move
Extract **`@heyhomie/domain`** (pure TS: pricing, add-on/included logic, time estimate, cancellation)
as ONE package imported by the web calculator AND the mobile apps. Then a price/rule change is one
edit, impossible to diverge. Until then this doc set is the contract.

## How to update this file
When a surface changes state or a step completes, update the table/list here **and** the owning
repo's state doc. Keep it short — this is the cross-surface index, not the detail.
