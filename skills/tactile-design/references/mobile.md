# Mobile

A phone layout isn't the desktop layout squeezed. It follows the platform's habits: thumb-sized targets, stacked actions, sheets from the bottom, presses that answer like native controls. Apply these rules to every phone change without being asked.

## 1. Breakpoints and pointers

- **Phone:** under 48rem (768px), `screen` only, so a printed page keeps desktop sizes.
- **Touch:** `(pointer: coarse)`. Some rules (16px fields, 44pt hit areas, no key chips) apply on any touch screen, tablets included, not only under 768px.
- Put phone and theme overrides in unlayered CSS (or a later layer) so they beat utilities without `!important`.

## 2. Native feel (always on)

| Rule | How | Why |
|---|---|---|
| Hover only on a fine pointer | hover styles under `(hover: hover) and (pointer: fine)` | hover states stick after a tap otherwise |
| The hover look shows on press instead | same styles on `:active` under `(pointer: coarse)`, wrapped in `:where()` so a control's own `active:` style still wins | every control answers a press as a native one does |
| No tap highlight | `-webkit-tap-highlight-color: transparent` | buttons press with their own scale |
| No double-tap delay | `touch-action: manipulation` on controls | taps feel instant |
| No accidental selection | `user-select: none; -webkit-touch-callout: none` on controls; links and content stay selectable | long-press doesn't select button text |
| No rubber banding | `overscroll-behavior: none` everywhere | the page doesn't bounce or chain scroll to what's behind |
| Real viewport height | `100dvh` | `100vh` hides content under the browser bars |
| Safe areas | `env(safe-area-inset-*)` on bars, sheets and fixed actions | nothing under the notch or home indicator |
| No scrollbars | hide them on phones | a fade or a cut-off item says "more" |
| No hover-only information | info tips open on tap; the reason a control is disabled shows as text under it | there is no hover |
| Fields don't zoom | 16px field text on any touch screen | iOS zooms into anything smaller |

```css
/* Tailwind v4: hover only for a mouse or trackpad */
@custom-variant hover {
  @media (hover: hover) and (pointer: fine) {
    &:hover { @slot; }
  }
}

/* A 44x44 hit area round a small control, look unchanged */
@media (pointer: coarse) {
  .touch-target { position: relative; }
  .touch-target::after {
    content: ""; position: absolute; left: 50%; top: 50%;
    width: max(100%, 44px); height: max(100%, 44px);
    transform: translate(-50%, -50%);
  }
}
```

## 3. Sizes

| Item | Phone |
|---|---|
| Buttons, fields, selects, toggle tracks | 44px (36px desktop recipes lift automatically) |
| Icon buttons, pills, small marks | keep their look; `touch-target` gives a 44pt hit area |
| Gap between small icon buttons | wide enough that hit areas don't overlap (22px icons sit 22px apart) |
| List rows | at least 44px |
| Rows in a sheet or grouped card | 48pt |
| Table rows | 44pt |
| Badges | 22px tall, 14px text |
| Empty-state icon | 24px |
| Side gutter | 20px; each block owns its own sides |

## 4. Type

| Role | Phone | Why |
|---|---|---|
| Floor | 15px for body, controls and meta: every size from 9px to 15px becomes 15px, as does text with no size of its own | small text is unreadable at arm's length |
| Body line height | 24px (from 20px) | a 15px line needs more room |
| Descriptions, footnotes, toggle options, filter pills (`mobile-small`) | 14/20 | the one exception to the floor: text directly under a 15px or larger lead may drop a step, so what it explains still leads |
| Field text | 16px | stops iOS zooming |
| Titles | 20–24px | |

The hierarchy on a phone: titles 20–24, body 15, descriptions 14.

## 5. Shell

- **Panel goes edge to edge:** no float, no radius, no shadow.
- **Top bar:** 56px plus the top safe area. White in light, the ground colour in dark, with a 0.5px hairline at its foot.
  - Left: a breadcrumb starting from the logo mark (20px), 15px text; parent crumbs ink-10, chevrons ink-8. A child page names itself in the breadcrumb instead of showing a Back button.
  - Right: a 44px menu button. Its two lines turn into an × (160ms, transform only).
- **Menu panel:** fixed from under the bar to the bottom of the screen. Fades in with a 4px drop (150ms in, 120ms out). Stays mounted and `inert` while closed, so dialogs opened from it survive. Order: search, navigation, account, settings, then a full-width secondary button for a low-priority action. Edge-to-edge rules between groups.
- **Bottom context bar** (optional): white in light, hairline on top, 20px padding (the home indicator replaces the bottom padding when it's taller). Holds one full-width 44px raised button that opens a sheet. Publish its height as a custom property; anything fixed to the bottom sits 12px above `max(bar, safe-area)`.
- **Top controls** sit at `calc(22px + env(safe-area-inset-top))`; content starts at `calc(64px + env(safe-area-inset-top))` where there's no bar.

## 6. Layout changes

| Desktop | Phone |
|---|---|
| Actions right of the title | under it, full width, stacked; page action stacks keep their desktop 12px gap |
| Side-by-side button pair | stacked full width. Pages: primary on top. Dialogs and flows: confirm on top (iOS alert order), 8px apart in dialog, sheet and flow footers. |
| Previous / Next | stay side by side, half and half |
| Multi-column grid | one column; banners go edge to edge |
| Segmented toggle that doesn't fit | a dropdown pill that opens a sheet |
| Search in a toolbar | first, full width, 44pt, raised (white + button shadow) in light |
| Filter row | scrolls sideways under the gutter with 20px edge fades; never wraps |
| Hover tooltip | opens on tap |
| Flow actions in the content | fixed at the foot of the screen (see 9) |
| Footer © line | hidden |
| Long labels | shortened ("org" for "organization", whole amounts drop ".00") |

```css
@media screen and (max-width: 47.99rem) {
  /* :not(:root) lifts specificity so a flex-row on the same element can't override it */
  .mobile-stack:not(:root)         { flex-direction: column;         align-items: stretch; }
  .mobile-stack-reverse:not(:root) { flex-direction: column-reverse; align-items: stretch; }
  :is(.mobile-stack, .mobile-stack-reverse) > * { width: 100%; justify-content: center; }
}
```

The utilities set no gap. Page action stacks keep their own 12px; dialog, sheet and flow footers set 8px on phones.

## 7. Dialogs become bottom sheets

**Shape**
- Full width, rises from the bottom. Top corners 14px; an inner band 13.5px.
- Hairline on the top edge only.
- Max height `calc(100dvh - env(safe-area-inset-top) - 24px)`; the 24px stays clear for the grab handle.
- The sheet's colour runs about 200px below its foot, so pulling up never shows a gap.
- The home-indicator inset is a transparent bottom border, so the sheet's own padding stays its own.

**Grab handle (replaces the ×)**
- A 36 × 5 pill whose foot sits 12px above the sheet, on the backdrop.
- Light: white at 90% with a faint shadow (`0 0 0 .5px rgba(0,0,0,.08), 0 1px 3px rgba(0,0,0,.2)`). Dark: white at 70%.
- Its hit area is an 88 × 44 button (width × height) labelled "Close": a tap closes the sheet.

**Content**
- No title band, no header hairline. The title is a 20/28 ink-12 section title on the sheet itself.
- Body padding 20px on the sides and top.
- A sheet in parts gives each part its own title, 12px above its content; parts sit 24px apart.
- Related information joins into **one raised card** of rows: white + button shadow in light, ink-3 on the ink-2 sheet in dark; radius 6; 0.5px rules between rows; rows at least 48pt, text 12px in, 16/24 text. The chosen row is ink-3 (ink-4 in dark), and a press shows the same.
- **Buttons never sit inside the card.** Below it, 12px under, 8px apart: full-width 44pt secondary buttons, icon before the label. They must look like real buttons, with enough contrast.
- Footer stacked, confirm on top, 8px gaps, bottom padding `max(24px, calc(12px + env(safe-area-inset-bottom)))`.

**Motion:** rises in 320ms, sinks in 200ms, solid the whole way (no fade, no blur). Reduced motion: fade only.

**Drag physics**
- Drag from the handle or the header. The sheet's own controls never start a drag; only drag regions set `touch-action: none`.
- 4px of slop before a press counts as a drag. The click that ends a drag is swallowed.
- Velocity is measured over the last 100ms.
- **Close** when dragged past 25% of the sheet's height, or on a flick faster than 0.11 px/ms. Slide out in 300ms on `--ease-drawer`.
- **Otherwise spring back** in 400ms on `--ease-drawer`.
- Dragging up only gives, tiring out towards 32px: `offset = (1 - 1 / (d * 0.55 / 32 + 1)) * 32`. This is the only rubber band in the app.
- Drag uses the CSS `translate` property so it stacks on the enter/exit `transform`.
- Drag only on a phone, and never while the sheet can't close (busy, paying).

**Search on a phone** becomes a full-screen sheet: the field across the top (56px) with Cancel at its end, results below to the foot.

## 8. Tables on a phone

- The frame scrolls sideways with no scrollbar.
- Columns hug their widest cell, header included (a grid with a subgrid per row), and cells never wrap.
- The first column stays sticky unless that would leave less than 88px beside it. At rest its edge is a hairline; once scrolled, light mode adds `6px 0 10px -6px rgb(var(--shadow-ink) / .16)` (the hairline alone in dark).
- Scrolled to the end, the last column fills the remaining width.
- The whole row opens the item, with an ink-3 press state.
- Dates drop their time zone.
- Pager: the count gets its own line; Previous and Next share the row half and half.
- Charts keep their axis fixed while the bars scroll.

## 9. Fixed bottom action bar (flows)

- Fixed to the foot, on the page ground, with a 0.5px top hairline.
- Padding: 20px sides, 12px top, bottom `max(20px, env(safe-area-inset-bottom))`.
- Actions stacked full width, primary on top.
- The content gets `calc(160px + env(safe-area-inset-bottom))` of room at its foot so it scrolls out from under the bar.

## 10. Check on a phone

- Test on a real device, not only a resized browser.
- Every target at least 44pt; no two hit areas overlap.
- Nothing hides under the notch, the home indicator or the browser bars.
- No hover-only information; no sticky hover after a tap.
- Fields don't zoom the page.
- Sheets drag and close as described; nothing rubber-bands except the sheet's upward give.
- Both themes, at zoom.

## Credit

Native-feel rules and sheet drag values follow Emil Kowalski's teaching (his mobile-native guidance and Vaul).
