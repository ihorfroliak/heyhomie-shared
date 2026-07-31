# DOMAIN_RULES.md — the canonical cleaning rules

**The anti-divergence anchor.** Every price, discount and rule a customer can see — on the web
calculator or in the mobile booking flow — must match this file. Numbers are the canonical PLN
price list (source: the web cleaning calculator); structural rules are confirmed against the
mobile domain code, which already implements the same shapes.

> Sources cited inline: **[web]** = `HeyHomie Web - Cleaning Service v1.dc.html` (`data-dc-script`);
> **[mob]** = `heyhomie-mobile/packages/domain/*`.

---

## 1. Pricing model — flat, per-room (NOT per-m², NOT per-hour to the customer)

Price is built from a **base + per-room + per-bathroom**, then multiplied by a **frequency factor**.
Kitchen and hallway are folded into the base — never a question, never a line item. **[web]**

Mobile already treats price as "**exact prices come from the backend**" (`catalog.ts`) — so there
is no competing hardcoded price on the mobile side. **This table IS those backend prices.** **[mob]**

### 1.1 Base prices by cleaning type **[web `TYPES`]**
| Type | Base (kitchen + hall) | + per room | + per bathroom | What it includes |
|---|---|---|---|---|
| **Standardowe** (standard) | **119 zł** | **30 zł** | **40 zł** | regular clean, no appliance interiors |
| **Generalne** (general) | **194 zł** | **50 zł** | **65 zł** | + appliance interiors, grout, cabinets inside |

`price(type, rooms, baths) = base + rooms·room + baths·bath` → then × frequency factor (§2).
Rooms/bathrooms come from the size counters (§6). Second kitchen (split-level, <1% of orders) is
a free-text note, priced by hand — not a calculator input. **[web]**

### 1.2 Worked example
Standard, 2 rooms, 1 bathroom, once:
`119 + 2·30 + 1·40 = 219 zł`. General same flat: `194 + 2·50 + 1·65 = 359 zł`.

---

## 2. Frequency & discounts **[web `FREQS`]**
| Frequency | id | Factor | Shown as | Cyclic? |
|---|---|---|---|---|
| One-off | `once` | ×1.00 | — | no |
| Weekly | `weekly` | ×0.85 | −15% | yes |
| Every 2 weeks | `biweekly` | ×0.90 | −10% | yes |
| Monthly | `monthly` | ×0.95 | −5% | yes |

Discount applies to the whole calculated price. Cyclic plans unlock the gear-fee waiver (§7). **[web]**

---

## 3. Add-ons **[web `ADDONS`]**
Prices are add-to-cart; `min` is the extra on-site minutes the worker plans for.

| Add-on | id | Price | + min | In general? | Qty? |
|---|---|---|---|---|---|
| Oven inside | `oven` | 40 zł | 30 | ✅ included | — |
| Fridge inside | `fridge` | 35 zł | 25 | ✅ included | — |
| Range hood + filters | `hood` | 45 zł | 30 | ✅ included | — |
| Cabinets inside | `cabinets` | 35 zł | 30 | ✅ included | — |
| Microwave inside | `microwave` | 25 zł | 15 | ✅ included | — |
| Bins cleaned | `bins` | 20 zł | 15 | ✅ included | — |
| Balcony / terrace | `balcony` | 30 zł | 25 | ✅ included | — |
| Windows | `windows` | 25 zł / sash | 20 | ❌ paid | ✅ qty |
| Ironing | `ironing` | 45 zł / hour | 60 | ❌ paid | ✅ qty |
| Extra hour | `hours` | 55 zł / hour | 60 | ❌ paid | ✅ qty |

### 3.1 Type drives the add-on list (the key UX rule) **[web + mob]**
Add-ons flagged **in general** are already part of a *general* clean, so on general they **disappear
from the add-on list** (not sold twice) — 7 rows collapse, 3 remain (windows, ironing, extra hour).
On *standard* all 10 are offered. The panel shows "**3 opcje — reszta w cenie**".
- **Mobile confirms this rule:** `cleaning.ts` `visibleAddOns(plan)` filters out
  `includedInGeneral` add-ons on general — same logic, different list ids (see §11). **[mob]**

---

## 4. Area (metraż) — informational, NOT priced **[web]**
Wheel 25–250 m², step 5. **Area does not change the price** — price depends on room count. Area is
captured so the homie knows how much time/supplies to bring, and it appears in the order summary.
Say this under the wheel, or the user drags it and wonders why nothing moves.

---

## 5. On-site time estimate (worker/ops layer, not a customer price) **[mob `cleaning.ts`]**
Complementary to the price: the mobile domain estimates **time** so scheduling/payout work.
`minutes = bathrooms·60 + kitchens·60 + rooms·30 + corridor 30`, clamped to a **3h minimum**
(`MIN_MISSION_MINUTES=180`); travel buffer 15 min separate. Add-on `min` values (§3) add to this.
Price (§1) and time (§5) are two projections of the same booking — keep both consistent.

---

## 6. Size counters **[web]**
Two counters: **rooms** and **bathrooms**. Kitchen + hallway are always in the base and never
counted. These feed both the price (§1) and the time (§5).

---

## 7. "About the object" — facts, not purchases **[web]**
Two toggles that change how the homie works, **priced separately from add-ons on purpose**:
- **Pets in the home** — **free**, zero price impact. It changes the work, not the bill.
  Mobile agrees: "*Pets welcome — the price stays the same*" (`cleaning.ts`). **[mob]**
- **Vacuum/mop/bucket on site** (`GEAR_FEE = 15`, `FREE_GEAR_AFTER = 10`): **[web]**
  - **General** clean → gear brought **free, always**.
  - **Standard one-off** → **+15 zł** if not on site.
  - **Standard cyclic** → 15 zł per visit **until the 10th**, then free. The note under the toggle
    changes with the chosen frequency.

---

## 8. Fragile disclosure (pre-clean, protects both sides) **[web `FRAGILE`]**
Chips the customer can flag + a free-text field: natural stone · uncoated brass/copper · antiques ·
already-damaged items · unlacquered parquet · faulty appliances. The homie gets this before entry;
it's documented on the order. Every clean is insured; pre-declared damage is logged (with a photo if added).

---

## 9. What we DON'T do (say it before booking) **[web `EXCLUSIONS`]**
1. **No moving furniture** — clean around/under anything liftable one-handed.
2. **No blinds/shutters** — vacuum from outside when closed.
3. **No chandeliers** — nothing above 3 m or needing disassembly; wipe reachable lamps.
4. **No ceilings** — cobwebs from corners yes, ceiling surface no.
5. **No stairwells** — *possible on request* (extra hour + a note, or contact support).
6. **No room disinfection** — single items (e.g. fridge interior) *possible on request via support*.

---

## 10. Cancellation, payment, invoicing
- **Free cancellation up to 24 h before** the visit. **[web]** Mobile matches exactly:
  `scheduling.ts` `CANCELLATION_WINDOW_HOURS = 24` → a full cancel inside 24 h is "late" and charged
  a fee; a within-cycle reschedule (nudging one visit) is exempt. **[mob]**
- **Always pay after the service** — nothing is charged at booking. The nightly settlement runs the
  morning after completion (card auto-charge, or a Stripe-hosted pay-later link). **[mob `payment.ts`]**
- **NIP / company invoice** — chosen in the following step; once saved, every future invoice goes to
  the company automatically. **[web]**

---

## 11. Other services (separate, own pricing) **[web `SERVICES`] ↔ [mob `catalog.ts`]**
| Service | Web price | Mobile catalog id |
|---|---|---|
| Upholstery cleaning | from 199 zł / item | `upholstery_cleaning` |
| Kitchen deep clean | from 249 zł | `kitchen_deep` |
| Bathroom deep clean | from 219 zł | `bathroom_deep` |
| Windows | from 25 zł / sash (min 4) | `window_cleaning` |
| Office cleaning | quote | `office_cleaning` |
| (mobile-only also) | — | `flower_delivery`, `post_renovation` |

The two catalogs map 1:1 on cleaning; mobile carries two extra categories (flowers, post-reno).

---

## 12. Room checklist (binding scope) **[web `CHECKLIST`]**
Seven zones: whole-apartment · kitchen · bathroom · bedroom · living room · hallway · balcony.
Each item is tagged: **•** always · **◆** general-only · **＋** paid add-on in standard / included in
general. The homie ticks it in the app; the customer sees the result in their account after the visit.
The full item list is the canvas `CHECKLIST` array — keep the web page and the worker app checklist
in sync from that single list. Explicit "what we don't do" block (§9) sits under it.

---

## Reconciliation — mobile ↔ web (what agrees, what needs a decision)

**Already aligned (no action — both sides already implement the same shape):**
- Type-drives-add-ons / included-in-general (`visibleAddOns` ↔ `inGeneral`).
- 24 h free-cancellation window.
- Kitchen + hallway always in base; pets free; pay-after-service.
- Service catalog maps 1:1 on cleaning.

**✅ DECIDED (2026-07) — 1. Pricing = this table is canon.**
The web calculator table (§1–§3) is the **canonical price list**. Mobile had no hardcoded prices, so
there is no conflict: the "backend prices" mobile refers to ARE these numbers. **Action:** the
backend/API must return exactly this table so web + mobile show the same price for the same order.
Changing a price = edit this file first, then the API.

**✅ DECIDED + DONE (2026-07) — 2. Add-on id set = web canon.**
The web ids (`oven/fridge/hood/cabinets/microwave/bins/balcony/windows/ironing/hours`, §3) are canon.
Mobile `cleaning.ts` **is now aligned** (commit `5d0134e`): renamed `extra_hours→hours`, dropped
`dishes_large`/`ventilator` (general checklist items, not bookable), added `cabinets`/`bins`, made
`balcony` included-in-general, and matched the planning minutes. Prices stay backend-side per §1.

**Everything in this file is canon.** Change a rule here first, then propagate to every surface.
