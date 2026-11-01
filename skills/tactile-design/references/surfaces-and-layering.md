# Surfaces and layering

How things sit on top of each other, and how that reads in light and in dark. Shadow and colour tokens are defined in [tokens.md](tokens.md).

## 1. The layer stack (bottom to top)

| Layer | Light | Dark | Notes |
|---|---|---|---|
| **Ground** (window, rail) | ink-2 | a stop below ink-1 (#080807) | The rail sits straight on the ground. No border; the panel's edge divides them. |
| **Panel** (page body) | `--surface-top`, `--shadow-panel` | ink-1, `--shadow-panel` (darker, wider) | Floats 8px off the window's top, right and bottom; flush against the rail; radius 8. Pages scroll inside it. |
| **Raised** (cards, buttons, chosen toggle option) | `--surface-top` + `--shadow-button` | ground + 1 step (cards) or + 2 steps (buttons), 0.5px ring, no shadow | A card uses the same recipe as a button. |
| **Well** (fields, toggle tracks) | one step darker than its surface | one step lighter | Sinks instead of rising. |
| **Table** | white/ink-2 stripes, 0.5px ink-6 border, `--shadow-table` | ink-2/ink-3 stripes, border only | The border stands in for the button's ring. |
| **Overlay** (menu, popover, modal, side panel, search) | `--surface-top`, 0.5px ink-6 border, shadow by level | ink-2, 0.5px border, the same shadow at about 4× opacity | Overlays keep shadows in dark. |
| **Toast** | tinted pill | tinted pill | Above everything. |

## 2. Elevation is expressed differently per theme

**Light:** the ground is ink-2 and anything raised is the white surface top with a hairline ring and a soft drop. Lift = white + shadow.

**Dark:** a shadow doesn't read on a dark ground. A raised surface is told by being **lighter** and by a **hairline** at its edge.

- Cards: one step up (ink-1 to ink-2) with a 0.5px ring.
- Controls: two steps above their ground, hover one more. In table rows, buttons are two steps above the row and two more on hover (ink-4 on ink-2 rows, ink-5 on ink-3 stripes).
- Buttons, tables and badges lose their drops in dark.
- Two things keep shadows in dark, made **stronger**: the panel over the near-black ground, and floating overlays.

**Sizes never fork.**

- Every ring is a `box-shadow` (0.5px or 1px spread), so it never changes layout.
- Where light hides a border that dark shows, the border exists in both: `border border-transparent` in light, coloured in dark. The box is the same size.
- Put theme differences in tokens, not in scattered `dark:` utilities. A dark change may only alter colour or shadow values.

## 3. Hairlines and borders

- **One weight: 0.5px in ink-6** (ink-5 in dark). Images, screenshots and recordings get a 0.5px ink-6 frame.
- **The dark line rule only reaches what it targets.** If it is written as a selector list of hairline utilities (0.5px borders, 0.5px-tall or -wide rules, named ring shadows), a line drawn any other way, such as an inset box-shadow, keeps ink-6 in dark. Give it its own dark remap.
- **Bar edges are drawn over the bar.** On a top strip, top bar or bottom bar, draw the foot or top rule as an absolutely positioned 0.5px element over the edge, not as a border. Content then centres on the full bar height (52px, 56px).
- **No double borders.** A nested element with its own border inside a bordered parent, a doubled bottom line, a header band rounder than its dialog: all bugs. When a header strip meets a body, the strip's radius belongs to the parent (outer radius minus the border).
- **Remove lines before adding them.** No lines between choice rows, no borders on empty states, no box around a group that space already groups.
- **Separators in menus and drawers run edge to edge.** The space above and below a separator equals the side padding.
- **A sticky header's bottom line appears only once content scrolls under it.** At the top it's invisible.
- **A fade beats a hard cut.** Where content scrolls, a mask fade in the background colour says "more" better than a scrollbar: 36px on a rail, 20px on a sideways row, 24px under a sticky top. Animate the fade length (registered `@property`, 200ms) so it eases in as you leave an edge.
- **Overscroll:** `overscroll-behavior: none` everywhere. No rubber banding, no scroll handed to the page behind.

## 4. Shell

### Desktop
- **Rail:** 292px wide, on the ground. The width is the widest nav label at body size + 16px icon + 6px gap + row padding + rail padding, rounded up to 4px; 292 is that sum for the default labels in Inter. Pinned top (account or workspace switcher, search), scrolling middle with 36px fades, pinned foot (one notice, then the user row, 18px apart and 18px from the bottom). No rules between parts; space separates them.
- **Rail rows:** 36px, control radius, 16px icon, 6px gap, 13/20 ink-10 text (ink-11 if your ink-10 misses 4.5:1 on the rail; see [tokens.md](tokens.md) §1). Hover: ground + 1 with ink-12 text. Current page: ink-3 fill (ink-2 in dark) with no edge and no shadow. Hover stays a step below the current fill so they never match. Rows 1px apart.
- **Group titles:** 13/20 ink-9, set off by 16px of space above, not a rule. Groups are always open, with no chevrons.
- **Top strip of the panel:** 52px tall (a 36px control + 2 × 8px inset), 36px controls 8px in from every edge, a hairline at its foot. The page title is 13/20 ink-12, centred and absolutely positioned so it doesn't move with the switcher. Breadcrumbs at the left (they replace back buttons); page-level actions at the right. The top bar is part of the panel, not a separate layer.
- **Collapse in three beats:** contents fade out together (80ms), the width changes (220ms), contents fade back in. Contents are pinned at full width inside the frame, so nothing reflows or slides while it moves.

### Phone
See [mobile.md](mobile.md): the panel goes edge to edge, the rail becomes a top bar with a menu.

## 5. Cards

**When to use a card**

| Content | Container |
|---|---|
| Navigation, link lists, choice lists | plain rows: no box, fill on hover only |
| One unit of related data (a figure with its chart, a balance, a plan) | a card |
| A step that is itself an action | a clickable card |
| Many records with the same fields | a table |
| Grouped settings or choices on a phone | one raised card of rows |

**Data card:** white + `--shadow-button` (ink-2 + 0.5px ring in dark), radius 8, 24px padding, 16–24px gaps. Optional header strip: 46px (20px line + 2 × 13px), 10px left / 8px right padding, a 13/20 title, a 0.5px rule under it and its action at the right.

**Clickable card:** white + `--shadow-button`, radius 8, 12–16px padding. Hover ink-2 (ink-3 in dark). Press `scale(.99)`. Cards in a row are the same height.

**Selection card:** pressed in when picked (ink-3 fill, hairline only). See [buttons-and-controls.md](buttons-and-controls.md).

**Notice:** radius 12, 0.5px border, white, `--shadow-table`, padding 24 × 16, a 16/24 title over a 13/20 line, the action at the right (under it, full width, on a phone). A tinted notice: hue-3 fill, hue-6 edge, hue-11 text. No warning icon before the title.

**Rail notice:** one at a time, by priority (blocking problems before warnings before announcements). Control radius, raised, a 38px header strip (20px line + 2 × 9px), full-width 36px buttons stacked (primary on top). It can fold to its title plus a progress ring. Dismissed through ⋯ or ×, except a blocking state, which can't be dismissed. It finishes by fading out whole.

**Section headings sit outside cards.** The heading names the card, so the card doesn't repeat it.

## 6. Tables

| Part | Default |
|---|---|
| Frame | 0.5px ink-6 border, radius 8, `--shadow-table` in light |
| Header | the page colour (zebra base), 13/20 ink-12, 0.5px ink-6 rule under it; columns split by 0.5px left dividers |
| Rows | 38px desktop (20px line + 2 × 9px), 44pt phone; 13/20 text; 12–16px cell padding (16px on the main record tables, 12px on denser ones), matched within a table |
| Zebra | header is the page colour, so the body starts on a stripe. Light: ink-2 / white. Dark: ink-2 / ink-3 |
| Hover, selected | plain tokens that sort after the stripe, so they always win |
| Alignment | text left; numbers right; first and last cells line up with the card's padding |
| Columns | hug their content so nothing wraps. Free text (names, emails, URLs) caps at 32ch (about 240px at 13px), then truncates on one line with "…" and a tooltip. See "Worst-case data" in [layout-and-hierarchy.md](layout-and-hierarchy.md). |
| Actions column | last; hugs its contents with 12–16px either side, matching the table's cell padding; a 1:1 ⋯ menu or a 22px icon / "View" pill; only valid actions (a deleted item can't be viewed) |
| Header for actions | appears only once a row exists |
| Expired or inactive row | the whole row at 50% opacity, never one cell |
| Row highlight | a new or merged row fades ink-a4 to transparent over 700ms |

**Pager outside, below.** 12px under the card: the count on the left ("Showing 1–10 of 48", ink-10), white Previous / Next on the right. Only when there's a page to move to. Lists on summary pages stop at 5–6 rows.

**Empty state:** hide the header and the pager. A bare centred panel: ink-2 fill, 44px padding, radius 8 (0.5px ring in dark), an 18px icon (24px on a phone, not in a box), two 13/20 lines in ink-9 (ink-11 where ink-9 misses 4.5:1): what's missing, then what to do. With filters on: "Try removing a filter, or clear them all."

**Failed load:** the empty-state layout with two lines ("Couldn't load runs." / what to try) and one secondary "Try again" button.

**Loading:** keep the frame and column widths; 12px pulsing ink-4 bars with control radius in place of text. Skeleton rows: the page size, capped at 10.

**Table inside a card:** runs edge to edge along the card's foot under its own hairline (negative margins equal to the card padding). First and last cells are inset by the card's padding.

## 7. Modals (desktop)

**Choose a modal** for a focused task (a form, a purchase, revealing a secret) or a confirm. An action opens its dialog over the current page; it doesn't navigate away.

| Width | Use |
|---|---|
| 620px | default: forms, purchases, key reveal |
| 460px | confirm, downgrade |
| 440px | compact info or steps popup |

Always `max-width: 100%` with a 16px backdrop gutter; `max-height: calc(100dvh - 32px)`.

**Card:** ink-2 (white in light), 0.5px ink-6 border, radius 8, `--shadow-modal`, z 100.

**Three parts, split by full-width 0.5px hairlines:**

1. **Header band:** padding 20 / 20 / 16 (sides, top, bottom). Title 16/24 ink-12. Description 13/20 ink-11, 4px under. Light: white like the body. Dark: a lifted ink-3 band over the ink-2 body. Takes the 7.5px top corners.
2. **Body:** 16px padding (24px for roomy forms), 16–24px gaps, inputs fill the width. The only part that scrolls.
3. **Footer:** 12px padding, Cancel left, confirm right, 12px gap, buttons hug their labels.

**Confirm variant (two parts):** the band holds the message; then the actions row. No body.
- State the consequence plainly: "This will delete the key from 3 projects." The title asks the question.
- Destructive: title in red-11, confirm solid red-9.

**No × on footer-driven modals.** Cancel, Escape and a backdrop click close them. Only footer-less info popups keep a 22px × at 20px from the top right, ink-10, hovering to an ink-3 fill.

**Backdrop:** black 30% in light, 90% in dark, 2px blur. Modals always dim. A side panel dims only when it's fullscreen. These are the house Modal's values; a library Dialog that drifts (80% dark, no blur, a 300ms sheet) should be brought in line.

**Behaviour**
- `role="dialog"` with `aria-labelledby` on the title.
- On open, focus the first field (not Cancel), or the confirm button in a two-part confirm.
- Trap Tab inside the modal; return focus to the trigger on close.
- Lock page scroll while it's open.
- Once the form has changed, a backdrop click does nothing (only Cancel or Escape close it), or it asks before discarding.
- Never stack two modals; replace the content in place.

**Multi-step in place.** Form, then spinner, then result happen inside the same card, with no close and reopen. The card eases between measured heights over 300ms with the strong ease-out while contents crossfade. Better still: hold the final size from the start so only the content fades. Give a spinner step a fixed height. Move focus into each new step.

**Anything opened from a modal** (select, menu) sits above it (z 110).

**Errors** from a modal on a list page become a toast after it closes, not an inline block on the page.

## 8. Side panel (desktop)

(Not in the source; defaults for the designer to confirm.)

- **Use it** to inspect or edit one record while the list stays visible. A focused task or a confirm is still a modal; a whole new context is a page.
- 400–480px wide, full height inside the 8px panel float, radius 8 on the inner edge.
- Same header band, body and footer as a modal.
- Overlay surface and `--shadow-modal`, z 100. No dim unless it's fullscreen.
- Enters `translateX(100%)` to 0 in 240ms on `--ease-out`; exits in 200ms. Escape closes it.
- On a phone it becomes a bottom sheet.

## 9. Bottom sheets (phone)

Every dialog becomes a bottom sheet under 768px. Full anatomy and drag physics in [mobile.md](mobile.md). The surface rules:

- Full width, 14px top corners, hairline on the top edge only.
- No header band and no ×. The title becomes a 20/28 section title on the sheet; a grab handle above the sheet replaces the ×.
- Rises in 320ms (the house Modal's value; bring a library Dialog's 300ms in line).
- **Related information joins into one raised card**, like a table: white + `--shadow-button` in light, ink-3 on the ink-2 sheet in dark; control radius; 0.5px rules between rows; rows at least 48pt.
- **Buttons never sit inside that card.** They go below it, 12px under, 8px apart, as full-width 44pt secondary buttons. They must look like real buttons.

## 10. Menus, popovers, tooltips

**Menu**
- Portalled to the body; positioned from the trigger, 6px below it (or centred on it). Min width 160px (320px for a workspace switcher).
- Ink-2 (white in light), 0.5px ink-6 border, radius 8, `--shadow-menu` with a 0.5px ring, 4–6px padding.
- Items per [buttons-and-controls.md](buttons-and-controls.md): 36px; in light hover `--root-ink-2` and the picked row ink-3, with no tick.
- Enters 180ms from `scale(.96)` + fade, origin at the trigger edge. Closes instantly.
- Closes on outside press, Escape, resize, or page scroll (scrolling its own list doesn't count).
- Don't move an open menu while items shift; reposition on the next open.
- On a phone it stays 20px from the screen edges, or becomes a sheet.
- Use one dropdown component everywhere.

**Info popover:** 12px × 10px padding, 12/16 ink-11, `--shadow-popover`. On a phone it opens on tap.

**Action tooltip:** names an icon-only action. Ink-3 fill with a 0.5px ink-6 border (or ink-12 inverted), 12/16 text, **zero delay**: it names the action the moment it's hovered. Never the only place information lives.

## 11. Toasts

- **One at a time.** Bottom centre, 24px from the bottom, z 120. On a phone: 12px above the bottom bar or home indicator, 20px from the sides.
- **Pill:** 48px tall, fully round. The tone's -3 fill, a hairline ring in its -6, title in -11 (-9 in dark), an optional 22px solid -9 action button with -contrast text. The whole pill is the tap target.
- Width: at least 400px with an action; otherwise it hugs the title plus 32px.
- Auto-dismiss after 6s.
- **Use a toast** for a result the user can't otherwise see: a background job finished, an action from a closed modal failed, a reward landed. Toasts may carry one action ("View project").
- **Don't toast** a copy (the icon turns into a tick) or anything already visible on screen.
- **Decorative shapes only for celebrations.** Routine saves get none; errors never do.
- Motion in [motion.md](motion.md).
