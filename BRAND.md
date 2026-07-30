# BRAND.md — design tokens, one source both sides consume

The two surfaces currently ship **different** tokens. This file records both honestly and proposes
one canon. Colour + type are the visible divergence; spacing/radii are close.

> **[web]** = `C` palette in `HeyHomie Web - Cleaning Service v1.dc.html`.
> **[mob]** = `heyhomie-mobile/packages/design/tokens.ts` (shipped in all 3 apps).

---

## 1. Colour — the two palettes side by side
| Role | Mobile (shipped) **[mob]** | Web design (new) **[web]** | Same? |
|---|---|---|---|
| Ink / primary text | `#14133A` | `#141338` | ~ (2-digit diff) |
| Mint / primary CTA | `#36F0C7` (`salad`) | `#77ECC8` (`green`) | ✗ different |
| Link / secondary accent | `#5465FC` (`blue`) | `#414483` (`blue`) | ✗ different |
| Muted / secondary text | `#727189` (`grey`) | `#52516B` (`grey`) | ✗ different |
| Light surface / fill | `#F4F7FF` (`bgLight`) | `#F6FBFF` (`light`) | ~ close |
| Border | `#E7EBF6` | `#EDEEEF` | ~ close |
| Accent / highlight | `#FF3C87` (`pink`) | `#EB4E87` (`pink`) | ~ close |
| Dark section bg | — | `#141338` (ink) | web-only |
| — extra web tokens | — | `mute #727187`, `yellow #F4D779`, `peri #C8CFF0` | web-only |

Semantic (mobile only, keep): success `#1D9E75` · warning `#EF9F27` · danger `#E24B4A` · info `#5465FC`.
Mission-status colours (mobile ops) stay mobile-side — not a brand-surface concern.

## 2. Typography — the biggest divergence
| | Mobile (shipped) **[mob]** | Web design (new) **[web]** |
|---|---|---|
| Heading font | **Quicksand** | **Manrope** (500/600/700/800) |
| Body font | **Lato** | **Manrope** |
| Accent (logo wordmark "homie") | — | **Montserrat** 500 |
| Sizes | caption 11 · small 13 · body 15 · h3 17 · h2 22 · h1 28 | body ~15.5 · h2 32 · h1 42 (web/desktop scale) |
| Weights | 400 / 500 / 700 | 500 / 600 / 700 / 800 |

## 3. Spacing / radii / shadow
- Spacing **[mob]**: 4 / 8 / 12 / 16 / 24 / 32. Web uses the same ballpark inline.
- Radii **[mob]**: sm 8 · md 12 · lg 16 · pill 999. Web uses 8 / 12 / 14 / 16 — **align on 8/12/16**.
- Shadow **[mob]**: card `#436CCB` 20% blur 15 y8. Web card: `0 14px 36px rgba(20,19,56,0.10)`.

---

## Reconciliation — BRAND

**✅ DECIDED (2026-07) — the web design is the refreshed brand canon.**
Manrope (+ Montserrat wordmark), mint `#77ECC8`, indigo `#414483`, the palette below. Applies to all
four surfaces. Do NOT hand-edit tokens in either repo without updating this file first.

**Migration status:**
- **Mobile `packages/design/tokens.ts` — DONE (2026-07):** colour values + font tokens migrated to the
  canon (keys kept; 426 refs unaffected). The 3 apps pick it up automatically.
- **Font rendering — PENDING:** no font is bundled in the apps yet (nothing loads expo-font), so text
  still falls back to the system font. To actually render Manrope: add `@expo-google-fonts/manrope`
  (+ Montserrat) and `useFonts` in each app's `_layout`. Tracked in ROADMAP.
- **Web:** already uses Manrope/Montserrat + this palette (it is where the canon came from).

### Canonical tokens
```
ink      #141338   // primary text, dark surfaces
mint     #77ECC8   // primary CTA
indigo   #414483   // links, secondary accent
slate    #52516B   // secondary text
light    #F6FBFF   // light surface / fill
border   #EDEEEF
pink     #EB4E87   // accent
yellow   #F4D779    peri #C8CFF0
font: Manrope (500–800) · wordmark Montserrat 500
radii: 8 / 12 / 16    spacing: 4 / 8 / 12 / 16 / 24 / 32
card shadow: 0 14px 36px rgba(20,19,56,0.10)
```
When decided: lock these (or the mobile set), delete the marker, and migrate the losing side.
