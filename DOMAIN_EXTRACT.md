# DOMAIN_EXTRACT.md — plan to make `@heyhomie/domain` one shared code package

**Goal.** Today the canon lives once in docs (`DOMAIN_RULES.md`) but is *implemented twice*:
`heyhomie-mobile/packages/domain/pricing.ts` (TS) and `heyhomie-web/.../lib/cleaning/calculator.js`
(JS). They match by discipline. The strongest fix is one **code** package both import, so a
price/rule change is a single edit and divergence is impossible at the code level.

## Why it's not a quick edit
The two repos have separate builds:
- **Cross-repo relative imports don't work.** Metro (mobile) only resolves inside the workspace
  (`watchFolders`); Next 10 (web) won't bundle files outside its own root. So a shared file in
  `heyhomie-shared/` can't just be `import`ed by both.
- It therefore needs a real **distribution mechanism** — a decision + infra access (registry).

## ⚠ DECISION NEEDED — distribution mechanism
| Option | How | Cost / risk |
|---|---|---|
| **A. Publish to a registry** *(recommended)* | Publish `@heyhomie/domain` (or a slim `@heyhomie/pricing`) to a **private registry** — GitHub Packages is the low-friction choice (repo already on GitHub). Both repos add it to deps + `.npmrc` auth. | Registry auth + a publish step in CI + semver discipline. |
| **B. Public npm** | Same, on public npm. | Public exposure of pricing logic; otherwise simplest. |
| **C. git subtree** | Vendor the package folder into each repo via `git subtree`. | No registry, but sync is manual and error-prone. |
| **D. Monorepo merge** | Move `heyhomie-web` into the mobile monorepo. | Cleanest sharing, biggest restructure; touches web CI/deploy. |

**Recommendation: A (GitHub Packages, private).** Lowest friction given everything is already on
`github.com/ihorfroliak`, keeps pricing private, gives real versioning.

## Execution plan (once the mechanism is chosen)
1. **Carve a slim, dependency-free package.** Extract just the cleaning price/time canon into a
   standalone module with ZERO other domain imports:
   - the price table + `cleaningPrice()` + `visibleAddons`/`inGeneral` add-on structure + the time
     estimate. (Mobile `pricing.ts` currently imports `addOnsFor`/`AddOnId` from `cleaning.ts` — fold
     the minimal add-on shape it needs into the package so it stands alone.)
   - Ship it dual-format (ESM + CJS) so Next 10 can consume it, with `.d.ts` types.
2. **Publish** `@heyhomie/pricing@1.0.0` to the chosen registry; add `.npmrc` auth to both repos.
3. **Mobile** — `packages/domain/pricing.ts` becomes a thin re-export of the package (keep the barrel
   surface stable); the unit tests move/point to the package.
4. **Web** — `lib/cleaning/calculator.js` internals replaced by an import of the package; the UI keeps
   its shape. The 10/10 engine test + the mobile 17/17 pricing test become the package's test suite.
5. **Lock it in** — a CI check (or the package's own test) asserts the published table equals
   `DOMAIN_RULES.md §1–§7`. A price change is then: edit the package → bump → both repos `npm update`.

## Until then
`DOMAIN_RULES.md` remains the contract and the two implementations are kept byte-identical by hand
(they currently are: standard 119/30/40, general 194/50/65, the frequency/add-on/gear tables). The
server already serves the canon at `GET /pricing/cleaning`, which is the interim single runtime source.
