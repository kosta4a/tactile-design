# Tokens

The system is a set of **roles**. Each role has a default value. Components are written against roles ("ink-12", "hue-3 with hue-11 text", "two steps above its ground"), never against hex codes, so the same components work with any palette and typeface.

**Recipes use tokens only.** Sizes, radii, colours, type and shadows come from variables; spacing comes from the 4px scale (`px-3.5`, `gap-1.5`). A literal px or hex in a recipe is a bug, because a remap can't reach it.

Defaults: Radix **sand** as the neutral, Inter as the sans, a 13px base.

## 1. The neutral scale (ink)

Twelve steps, each with a job. Any 12-step neutral works (gray, slate, mauve, olive, sand, or your own) as long as each step does its job and passes the gates in §12.

| Step | Job | Light (sand) | Dark (sand) |
|---|---|---|---|
| ink-1 | page ground | #fdfdfc | #111110 |
| ink-2 | app ground, raised surface in dark, stripe | #f9f9f8 | #191918 |
| ink-3 | quiet fill: field well, chip, selected row | #f1f0ef | #222221 |
| ink-4 | hover on a fill, focused well | #e9e8e6 | #2a2a28 |
| ink-5 | pressed fill, dark hairline | #e2e1de | #31312e |
| ink-6 | hairline, control ring | #dad9d6 | #3b3a37 |
| ink-7 | strong line, hovered border | #cfceca | #494844 |
| ink-8 | rare: disabled text on fills | #bcbbb5 | #62605b |
| ink-9 | faint: leading icons, group titles, empty-state text | #8d8d86 | #6f6d66 |
| ink-10 | tertiary: meta, timestamps, placeholders, chevrons, close × | #82827c | #7c7b74 |
| ink-11 | secondary text: descriptions, body, row icons | #63635e | #b5b3ad |
| ink-12 | strong text: titles, values, labels; the primary fill | #21201c | #eeeeec |

**Text a user must read to act passes 4.5:1 on its surface.** Default sand misses that on the faint steps:

- Light ink-10: 3.87:1 on white, 3.40:1 on an ink-3 well.
- Light ink-9: 3.34:1 on white, 2.93:1 on ink-3.
- Dark ink-10: 4.45:1 on ink-1. Dark ink-9: 3.65:1 on ink-1.

So either tune ink-9 and ink-10 darker (lighter in dark) until they pass, or keep them for placeholders, chevrons, icons, separators and a timestamp beside a value, and set essential text (rail labels, empty-state lines, figure labels, pager counts) in ink-11.

Alpha steps, for hairline rings that must work on any fill:

| Token | Light | Dark |
|---|---|---|
| ink-a3 | #20100010 | #f6f6f513 |
| ink-a4 | #1f150019 | #fefef31b |

**Choose the neutral once, at setup.** Pairing it with the brand hue is fine (slate with blue, mauve with purple, sage or olive with green, sand with yellow or orange). After that, never mix brand hue into neutral steps or swap a neutral step for a brand step.

## 2. Surface roles

`--surface-top` is the top surface in light: pure white by default.

| Role | Light | Dark | Why |
|---|---|---|---|
| Ground (window, rail) | ink-2 | a stop below ink-1 (#080807) | In dark the order turns over: the darkest thing is furthest back. |
| Panel (the page body) | `--surface-top` | ink-1 | The working area is the lightest plane in light and sits one step up in dark. |
| Raised (cards) | `--surface-top` | ink-2 (ground + 1) | Light raises with a shadow; dark raises by being lighter. |
| Control fill (secondary button, pill) | `--surface-top` | ground + 2 steps | In dark a control sits one step higher than a surface, so it reads as pressable. |
| Well (fields) | ink-3 | ink-3 | A field sinks: darker than its surface in light, lighter in dark. |
| Overlay (modal, menu, popover) | `--surface-top` | ink-2 | Overlays are white in light and keep their shadow in both themes. |
| Hover on any fill | local ground + 1 step | local ground + 1 step | One step is enough to read. |
| Selected on any fill | local ground + 2 steps | local ground + 2 steps | Hover always stays a step below selected, so the two never match. Hovering a selected row keeps it selected. |
| Tooltip | ink-12 fill, ink-1 text, or ink-3 fill with a hairline | inverted | Short labels for icon-only actions. |

## 3. The step-shift rule

Components are drawn as if they sit on an ink-1 page. When a container's ground is a different step, it **remaps steps 1–7 inside itself** so every child keeps the same contrast against its ground. Text steps (8–12) never move, so ink is the same everywhere.

| Surface (light) | Remap | Effect |
|---|---|---|
| Rail on an ink-2 ground | 1→2, 2→3, 3→4 ... 7→8 | A child's ink-2 hover becomes ink-3, an ink-3 fill becomes ink-4, an ink-6 line becomes ink-7. |
| Panel | 1→surface top | The page is the top surface; everything else is root. |
| Overlay | 1→surface top, 2→surface top, 3–7 root | A white card; fields inside are true ink-3. Hovers inside use `--root-ink-2`. |
| Raised inside a rail or panel | reset to root | A raised surface is its own ground again. |

In dark the surfaces use the root scale: the ground is already below ink-1, so the panel (ink-1) and overlays (ink-2) step up naturally.

Implementation notes:

- A step cannot be defined from its own neighbours on the same element (each one would chain to the last). Keep fixed `--root-ink-N` handles per theme and remap `--ink-N` from those.
- A variable built from a working step (`--line`, `--shadow-primary`) resolves where it is declared. Redeclare it on each surface class so it follows the remap.

```css
.surface-rail, .surface-panel, .surface-overlay, .surface-raised {
                   --line: var(--ink-6); --shadow-primary: 0 0 0 1px var(--ink-1); }
.surface-rail    { --ink-1: var(--root-ink-2); --ink-2: var(--root-ink-3); --ink-3: var(--root-ink-4);
                   --ink-4: var(--root-ink-5); --ink-5: var(--root-ink-6); --ink-6: var(--root-ink-7);
                   --ink-7: var(--root-ink-8); }
.surface-panel   { --ink-1: var(--surface-top); }
.surface-overlay { --ink-1: var(--surface-top); --ink-2: var(--surface-top); --ink-3: var(--root-ink-3);
                   --ink-4: var(--root-ink-4); --ink-5: var(--root-ink-5); --ink-6: var(--root-ink-6);
                   --ink-7: var(--root-ink-7); }
.surface-raised  { --ink-1: var(--root-ink-1); --ink-2: var(--root-ink-2); --ink-3: var(--root-ink-3);
                   --ink-4: var(--root-ink-4); --ink-5: var(--root-ink-5); --ink-6: var(--root-ink-6);
                   --ink-7: var(--root-ink-7); }
/* Scope these to light, e.g. :root:not([data-theme="dark"]) .surface-rail { ... } */
```

## 4. Hue scales (accent and status)

Every coloured scale has the same 12 positions as the neutral. Only a few are used.

| Step | Role |
|---|---|
| hue-3 | tint fill: badges, callouts, toasts, status rows |
| hue-4 | tint hover |
| hue-6 | edge on a tint (also what 30% of hue-11 over hue-3 lands on) |
| hue-9 | solid: dots, chart bars, done markers, the destructive confirm, a switch that's on |
| hue-10 | solid hover |
| hue-11 | text on a tint, red text on a white button |
| hue-12 | high-contrast text, rare |
| hue-contrast | text on a hue-9 solid: white for most hues, dark for yellow, amber, lime, mint and sky (Radix ships these, e.g. `--yellow-contrast`) |

**The tinted pattern:** hue-3 fill + hue-11 text (+ a hue-6 hairline). Used for every badge, callout, toast and status label. In a Tailwind-style alias, map `-500` to step 9 and both `-400` and `-700` to step 11 (step 11 is already the right text tone in each theme).

**What hue means.** Hue is reserved for meaning, never for ranking importance. The roles are fixed; the example column is how the source product used them.

| Role | Default hue | Meaning | Example (source product) |
|---|---|---|---|
| success / live | green | running, live, done. Only when the count is above 0. | live sessions |
| danger | red | failed, error, deleted, destructive | out of balance |
| warning | yellow or amber | caution, needs attention | time estimates, admin |
| info | blue | information, default, beta | rewards, money in |
| inactive | neutral | stopped, paused, off | stopped items |
| identity | any non-status hue | a category, tier or product; carried to its badge, bar and avatar tint | plan badges |

**Status roles are fixed; status meaning always wins.** A brand hue within about 25° of a status hue in OKLCH becomes that status's scale and is never used for identity; identity then takes a non-status hue. One hue never means both status and identity on a screen.

**Brand colours:** generate a full 12-step light scale and a 12-step dark scale per brand colour (Radix's custom palette method does this, including alpha steps and the contrast colour). Alias each standard hue family to its nearest brand colour (red, tomato, ruby, crimson to your red; sky, blue, indigo, iris to your blue). Keep the brand scales available under their own names for places that mean the brand itself.

## 5. Lines

| Token | Default | Rule |
|---|---|---|
| `--hairline` | 0.5px | Every structural line: borders, dividers, rules, table cells. |
| `--line` | ink-6 (light), ink-5 (dark) | In dark every 0.5px line is one step darker, so lines don't glow. Remap on the element that draws the line, so it stays relative to its surface. |
| Control ring | 1px in the button shadow | The only 1px line. |
| Marks and strokes | 1.5px | Radio and checkbox rings, icon strokes, spinners. |
| Scrollbar (desktop) | 8px, thumb ink-6 with a 1px ink-2 border | Dark thumb ink-3. None on phones. |

A ring drawn as `box-shadow: 0 0 0 0.5px var(--line)` replaces a border without affecting layout.

## 6. Shadows

Every light shadow is drawn in the neutral's ink-12 colour (`--shadow-ink`, rgb 33 32 28 with sand), never pure black, so a tinted neutral gets one shadow colour. Dark shadows stay black.

| Token | Light | Dark | Used for |
|---|---|---|---|
| `--shadow-button` | `0 1px 2px 0 rgb(ink / .12), 0 0 0 1px rgb(ink / .08)` | `0 0 0 0.5px ink-5` | Secondary buttons, raised cards, pills. The shadow is the edge; no border. |
| `--shadow-primary` | `0 0 0 1px var(--ink-1)` | `none` | The primary. A 1px ring in the local surface colour makes the fill read a pixel smaller, and never halos on an ink-2 rail. |
| `--shadow-toggle` | same as `--shadow-button` | `0 1px 2px rgb(0 0 0 / .32), 0 0 0 1px ink-6` | The chosen option of a toggle, round pills. Dark needs an edge plus a drop to read. |
| `--shadow-raised` | `0 0 0 0.5px ink-a4, 0 1px 2px rgb(ink / .02)` | `0 0 0 0.5px ink-a3` | Surfaces raised inside a page or rail. |
| `--shadow-table` | `0 1px 2px 0 rgb(ink / .12)` | `none` | Tables: the button's drop without its ring (the table border stands in). |
| `--shadow-panel` | `0 0 0 0.5px ink-a4, 0 1px 2px rgb(ink / .015), 0 2px 12px -2px rgb(ink / .025)` | `0 0 0 0.5px ink-a3, 0 1px 2px rgb(0 0 0 / .5), 0 4px 16px -2px rgb(0 0 0 / .6)` | The page body floating on the ground. Dark is darker and wider, to read on near-black. |
| `--shadow-popover` | `0 8px 24px rgb(ink / .12)` | black at .45 | Popovers |
| `--shadow-menu` | `0 12px 32px rgb(ink / .12)` | black at .45 | Menus, dropdowns, search. Pair with a 0.5px ink-6 ring. |
| `--shadow-modal` | `0 24px 64px rgb(ink / .16)` | black at .5 | Dialogs, side panels |
| `--shadow-badge` | `inset 0 0 0 0.5px color-mix(in srgb, currentColor 30%, transparent), 0 1px 2px color-mix(in srgb, currentColor 14%, transparent)` | inset ring only | Every badge. Drawn from its own text colour, so each hue tints its own edge. |

`rgb(ink / a)` is short for `rgb(var(--shadow-ink) / a)`.

**The ladder:** 1/2 (controls) → 8/24 (popover) → 12/32 (menu) → 24/64 (modal). Each level up roughly doubles offset and blur.

**Dark rule:** buttons, tables and badges lose their drops in dark. Only two things keep (stronger) shadows: the panel on the near-black ground, and floating overlays.

## 7. Radii

Change `--radius-control` only, to match your icon set and typeface corners (4–8px). Everything else follows from it.

| Token | Default | Formula | Where |
|---|---|---|---|
| `--radius-xs` | 3px | control / 2 | key chips |
| `--radius-sm` | 4px | control − 2px | checkbox marks |
| `--radius-control` | 6px | the one you set | every button, field, select, menu item, rail row; cards of rows in a sheet |
| `--radius-container` | 8px | control + 2px | panel, cards, tables, popovers, menus, desktop dialogs |
| `--radius-feature` | 12px | container × 1.5 | large notices and feature cards |
| `--radius-sheet` | 14px | fixed (a platform convention) | top corners of a phone sheet |
| full | 999px | | pills, badges, counts, dots, avatars, toggle tracks |

**Nested radius = outer radius minus the border.** A header band inside an 8px dialog with a 0.5px edge gets 7.5px; inside a 14px sheet, 13.5px. A phone panel that runs full bleed has radius 0.

## 8. Type

**Faces.** One sans for every word of UI, titles included. One mono for machine values only (IDs, code, keys, terminal text). For a serif, rounded or brand heading face, or a non-Latin script, see "Non-default faces" in §12.

**The weight rule.** Every UI weight renders at one weight: the one at which your sans reads solid at 13px (Inter: Medium, 500). `normal`, `medium` and `semibold` all draw at that weight, and bold is almost never used. Hierarchy comes from size and ink step, not weight. To pick the weight for another face, use the weight test in "Typeface fit" (§12).

**Line heights are absolute px**, so children inherit a px value rather than a ratio.

| Role | Token | Desktop | Phone | Ink | Notes |
|---|---|---|---|---|---|
| Display | `--text-display` | 32/40, −0.02em | 32/40, or 24/32 if the title wraps past 2 lines | 12 (lead words 11) | rare; one per page at most |
| Display figure | `--text-figure` | 24/32 | 24/32 | 12 | `tabular-nums` |
| Section title, in-column page title | `--text-section` | 20/28 | 20/28 | 12 | |
| Card, dialog, step title | `--text-title` | 16/24 | 16/24 | 12 | `tabular-nums` for numbers |
| Top-strip page title | `--text-body` | 13/20 | | 12 | centred in the 52px strip |
| Body, controls | `--text-body` | 13/20 | 15/24 | 12 values, 11 descriptions | the workhorse |
| Descriptions, footnotes, pills | `--text-body` | 13/20 | 14/20 | 11 | phone `mobile-small`, so what they explain still leads |
| Meta | `--text-meta` | 12/16 | 15/20 (or 14/20) | 10 | |
| Badge | `--text-meta` | 12/20 in a 20px pill | 14 in a 22px pill | hue-11 | |
| Field text | `--text-field` | 13/20 | 16 on any touch screen | 12 | 16px stops iOS zooming |

**Tracking:** −0.01em on every button label, −0.02em on display titles, +0.02em on mono in inputs. Positive tracking otherwise only on tiny uppercase. These values suit a face as loosely spaced as Inter; see "Typeface fit" for other faces.

## 9. Spacing and sizes

**Grid:** 4px with 2px half-steps. The ladder in use: 2, 4, 6, 8, 12, 16, 20, 24, 32, 48, 64.

| Where | Spacing |
|---|---|
| Inside a control or row (icon to label, chip padding) | 4–8px |
| Between items in a group, button to button | 8–12px |
| Between fields, between blocks in a form | 16–20px |
| Card padding, dialog body | 16–24px |
| Between sections | 24px gap inside 32px of section padding |
| Above the first title / page foot | 48px / 64px |

| Size | Token | Default |
|---|---|---|
| Control height | `--control-h` | 36px desktop, 44px phone |
| Compact control | `--control-h-compact` | 32px |
| Pill, toggle option, icon button | `--control-h-pill` | 28px |
| Inline row action | `--control-h-inline` | 24px |
| Row icon button, switch height | `--control-h-icon` | 22px |
| Badge; search key pill | `--badge-h` | 20px |
| Button key chip | `--key-chip` | 18px |
| Table row | `--row-h` | 38px desktop, 44pt phone |
| Sheet row | | 48pt |

Heights follow from line height plus padding: 36 = 20 + 2 × 8; 38 = 20 + 2 × 9. If the type scale changes, recompute them the same way.

## 10. Motion tokens

Full rules in [motion.md](motion.md).

| Token | Default | Role |
|---|---|---|
| `--ease-out` | `cubic-bezier(.23, 1, .32, 1)` | strong ease-out: anything the user caused (press, swap, enter, exit), and hover and colour changes too |
| `--ease-move` | `cubic-bezier(.22, 1, .36, 1)` | long on-screen moves of big objects (400–450ms) |
| `--ease-drawer` | `cubic-bezier(.32, .72, 0, 1)` | a sheet after the finger lets go |
| `--ease-in-out` | `cubic-bezier(.77, 0, .175, 1)` | one-off: a card folding to its header, where both ends stay on screen |
| `--dur-hover` | 150ms | hover, press, colour |
| `--dur-swap` | 200ms | content swaps, overlay fade, exits |
| `--dur-modal` | 240ms | modal card in |
| `--dur-move` | 300ms | larger state changes, height following content |

Define each curve once.

## 11. Z-index ladder

| Layer | z |
|---|---|
| Phone nav menu | 40 (below dialogs, so modals opened from it appear above) |
| Third-party component dialogs, popovers, tooltips (primitives mounted outside the app's own modals; often z 50) | 50 |
| Fullscreen media viewer | 95 |
| App modals, side panels, menus, search | 100 |
| Anything opened from a modal (select, menu) | 110 |
| Toast, context menu | 120 |

## 12. Remap to your palette and typeface

1. **Neutral.** Choose a 12-step neutral for light and dark, once (see §1). Fill `--root-ink-1..12`. Then check these gates:
   - ink-12: at least 7:1 on ink-1 to ink-3.
   - ink-11: at least 4.5:1 on ink-3.
   - ink-10: at least 4.5:1 on ink-1 if it carries meta or labels; otherwise only placeholders, chevrons and icons, at 3:1 or more.
   - ink-9: icons and non-essential text only, at 3:1 or more.
   - hue-11: at least 4.5:1 on hue-3.
   - ink-6: at least 1.35:1 against `--surface-top` (sand: 1.41), so a 0.5px hairline shows.
2. **Alpha steps.** Take ink-a3 and ink-a4 from the same generator as your scale, or derive them: light `--ink-a3: color-mix(in oklab, var(--root-ink-12) 6%, transparent)` and `--ink-a4` at 10%; dark 7% and 11% of dark ink-12. Check that ink-a3 over ink-1 roughly equals ink-3.
3. **Dark ground.** Take dark ink-1 and lower its OKLCH L by the ink-1 to ink-2 gap (about 0.04), at the same hue and chroma. Sand: L 0.177 becomes 0.134, which is #080807.
4. **Surface top.** Pure white by default. If your neutral's lightest step is tinted, set `--surface-top` to a value lighter than root ink-1 and re-check the ink-6 hairline against it.
5. **Hues.** Generate 12 light + 12 dark steps for each brand colour. Fill the hue roles (3, 4, 6, 9, 10, 11, contrast). Map status roles to the nearest hue and apply the collision rule in §4. If your brand colour as step 9 gives less than 4.5:1 with both white and ink-12 text, keep it as step 9 for fills without text, and use hue-10 or hue-11 where a solid carries a label.
6. **Primary.** By default `--primary-bg` is ink-12 and `--primary-fg` is ink-1. If the brand requires a coloured primary, opt in: accent-9 fill, accent-contrast text (at least 4.5:1), accent-10 hover, and keep the 1px surface ring in light. Remove that hue from status use. Everything else stays: one per surface, secondary stays white, destructive stays red-9 and must look clearly different from the accent.
7. **Shadow ink.** Set `--shadow-ink` to your light ink-12 as RGB. All light shadows read `--shadow-ink`; dark shadows stay black.
8. **Typeface.** Set `--font-sans` and `--font-mono`, then run "Typeface fit" below.
9. **Radius.** Change `--radius-control` only, to match your icon set and typeface corners (4–8px). The ladder follows.
10. **Leave the rest.** Heights, hairline, shadow geometry, durations, curves and spacing are the system. Change them only on purpose, and everywhere at once.

### Typeface fit

The type scale was tuned for Inter. Fit another face in four steps.

1. **Size.** `font-size-adjust: 0.546` on `:root` (Inter's x-height divided by its em) makes any face match Inter's apparent size at the default px values. It is inherited, so it covers the mono too. Where it isn't available, multiply every size in the type table by `0.546 / (your sxHeight / unitsPerEm)` and round to 0.5px. Keep line heights on the 4px grid, and raise a line height by one 4px step if ascender plus descender is over 1.2em. If sizes scale by a factor f, scale the column widths in [layout-and-hierarchy.md](layout-and-hierarchy.md) by f too.
2. **Weight.** Render the same 13px string in Inter 500 and in your face at 400, 500 and 600. Pick the weight with the closest ink coverage; ties go lighter. On a variable font, pick between 450 and 550. If the face has no medium, use 400 and keep hierarchy in ink. Never use a synthesised weight.
3. **Tracking.** Keep −0.01em and −0.02em only if the face is as loose as Inter. Use 0 for tightly spaced faces. Never go below −0.02em at 16px or smaller.
4. **Requirements.** Tabular figures (`tnum`), a real ellipsis glyph (…), and the chosen weight as a real master. Without `tnum`, set aligned figures in the mono.

### Non-default faces

- **Serif, rounded or mono UI face:** fine, if it passes "Typeface fit".
- **Brand heading face:** sets display titles (32/40) and at most section titles (20/28), in one weight. Never in controls, tables, badges or body text.
- **Non-Latin scripts (CJK, Arabic, Devanagari):** base 14/22, phone 16/24, no negative tracking, no uppercase rules, and `font-size-adjust` set per fallback face.
- **Mono:** match the sans's x-height with `font-size-adjust`, or set it at 0.92em as a fallback. Most monos look larger than the sans at the same px size.

### Mapping from other token systems

| shadcn | This system |
|---|---|
| background | `--panel` (ink-1) |
| foreground | ink-12 |
| card, popover | `--raised`, `--overlay` |
| muted, input | ink-3 |
| muted-foreground | ink-11 |
| border | `--line` (ink-6) |
| primary / primary-foreground | `--primary-bg` / `--primary-fg` |
| ring | ink-12 |
| destructive | red-9 |

Tailwind grays to ink steps: 50 → 1, 100 → 2–3, 200 → 4–5, 300 → 6–7, 400 → 8–9, 500 → 10, 600 → 11, 900–950 → 12. (700–800 have no direct step; use 11 or 12.)

### CSS template

```css
:root {
  /* Neutral (default: Radix sand, light). Fixed handles; never remapped. */
  --root-ink-1: #fdfdfc; --root-ink-2: #f9f9f8; --root-ink-3: #f1f0ef; --root-ink-4: #e9e8e6;
  --root-ink-5: #e2e1de; --root-ink-6: #dad9d6; --root-ink-7: #cfceca; --root-ink-8: #bcbbb5;
  --root-ink-9: #8d8d86; --root-ink-10: #82827c; --root-ink-11: #63635e; --root-ink-12: #21201c;
  --ink-a3: #20100010; --ink-a4: #1f150019;

  /* Working steps: components use these; surfaces remap 1-7. */
  --ink-1: var(--root-ink-1); --ink-2: var(--root-ink-2); --ink-3: var(--root-ink-3);
  --ink-4: var(--root-ink-4); --ink-5: var(--root-ink-5); --ink-6: var(--root-ink-6);
  --ink-7: var(--root-ink-7); --ink-8: var(--root-ink-8); --ink-9: var(--root-ink-9);
  --ink-10: var(--root-ink-10); --ink-11: var(--root-ink-11); --ink-12: var(--root-ink-12);

  /* Surfaces */
  --surface-top: #ffffff; /* the top surface in light; lighter than root ink-1 */
  --ground: var(--root-ink-2);
  --panel: var(--surface-top);
  --raised: var(--surface-top);
  --overlay: var(--surface-top);
  --line: var(--ink-6);
  --hairline: 0.5px;

  /* Primary action */
  --primary-bg: var(--ink-12);
  --primary-fg: var(--ink-1);
  --primary-hover: color-mix(in oklab, var(--primary-bg) 93%, var(--primary-fg)); /* lightens dark fills, darkens light ones */

  /* Hues: any 12-step scales. Fill the steps you use; -contrast is the text on -9. */
  --accent-3: ; --accent-6: ; --accent-9: ; --accent-10: ; --accent-11: ; --accent-contrast: ;
  --red-3: ; --red-6: ; --red-9: ; --red-10: ; --red-11: ; --red-contrast: ;
  --green-3: ; --green-9: ; --green-11: ; --green-contrast: ;

  /* Shadows: every light shadow reads --shadow-ink */
  --shadow-ink: 33 32 28; /* light ink-12 as RGB */
  --shadow-button: 0 1px 2px 0 rgb(var(--shadow-ink) / .12), 0 0 0 1px rgb(var(--shadow-ink) / .08);
  --shadow-primary: 0 0 0 1px var(--ink-1);
  --shadow-toggle: var(--shadow-button);
  --shadow-raised: 0 0 0 0.5px var(--ink-a4), 0 1px 2px rgb(var(--shadow-ink) / .02);
  --shadow-table: 0 1px 2px 0 rgb(var(--shadow-ink) / .12);
  --shadow-panel: 0 0 0 0.5px var(--ink-a4), 0 1px 2px rgb(var(--shadow-ink) / .015),
                  0 2px 12px -2px rgb(var(--shadow-ink) / .025);
  --shadow-popover: 0 8px 24px rgb(var(--shadow-ink) / .12);
  --shadow-menu: 0 12px 32px rgb(var(--shadow-ink) / .12);
  --shadow-modal: 0 24px 64px rgb(var(--shadow-ink) / .16);
  --shadow-badge: inset 0 0 0 0.5px color-mix(in srgb, currentColor 30%, transparent),
                  0 1px 2px color-mix(in srgb, currentColor 14%, transparent);
  --backdrop: rgb(0 0 0 / .3);

  /* Shape: set --radius-control; the rest follow */
  --radius-control: 6px;
  --radius-xs: calc(var(--radius-control) / 2);
  --radius-sm: calc(var(--radius-control) - 2px);
  --radius-container: calc(var(--radius-control) + 2px);
  --radius-feature: calc(var(--radius-container) * 1.5);
  --radius-sheet: 14px; /* platform convention; fixed */

  /* Sizes */
  --control-h: 36px; --control-h-compact: 32px; --control-h-pill: 28px;
  --control-h-inline: 24px; --control-h-icon: 22px; --badge-h: 20px; --key-chip: 18px;
  --row-h: 38px;

  /* Type */
  --font-sans: "Inter", system-ui, sans-serif;
  --font-mono: ui-monospace, "SF Mono", Menlo, monospace;
  --font-weight-ui: 500;
  --text-meta: 12px;    --leading-meta: 16px;
  --text-body: 13px;    --leading-body: 20px;
  --text-title: 16px;   --leading-title: 24px;
  --text-section: 20px; --leading-section: 28px;
  --text-figure: 24px;  --leading-figure: 32px;
  --text-display: 32px; --leading-display: 40px;
  --text-field: var(--text-body);
  --tracking-label: -0.01em; --tracking-display: -0.02em;
  font-size-adjust: 0.546; /* Inter's x-height / em; other faces match its apparent size */

  /* Durations */
  --dur-hover: 150ms; --dur-swap: 200ms; --dur-modal: 240ms; --dur-move: 300ms;

  /* Curves. With Tailwind, move these into a plain @theme instead (see below). */
  --ease-out: cubic-bezier(.23, 1, .32, 1);
  --ease-move: cubic-bezier(.22, 1, .36, 1);
  --ease-drawer: cubic-bezier(.32, .72, 0, 1);
  --ease-in-out: cubic-bezier(.77, 0, .175, 1); /* one-off: a card folding */
}

/* Phone and touch: values only. */
@media screen and (width < 48rem) {
  :root { --control-h: 44px; --row-h: 44px; --text-body: 15px; --leading-body: 24px;
          --text-meta: 15px; --leading-meta: 20px; }
}
@media (pointer: coarse) {
  :root { --text-field: 16px; }
}

/* Dark values. Colour and shadow only: never padding, height, radius or border width. */
:root[data-theme="dark"] {
  --root-ink-1: #111110; --root-ink-2: #191918; --root-ink-3: #222221; --root-ink-4: #2a2a28;
  --root-ink-5: #31312e; --root-ink-6: #3b3a37; --root-ink-7: #494844; --root-ink-8: #62605b;
  --root-ink-9: #6f6d66; --root-ink-10: #7c7b74; --root-ink-11: #b5b3ad; --root-ink-12: #eeeeec;
  --ink-a3: #f6f6f513; --ink-a4: #fefef31b;

  --ground: #080807;
  --panel: var(--root-ink-1);
  --raised: var(--root-ink-2);
  --overlay: var(--root-ink-2);
  --line: var(--ink-5);

  --shadow-button: 0 0 0 0.5px var(--ink-5);
  --shadow-primary: none;
  --shadow-toggle: 0 1px 2px rgb(0 0 0 / .32), 0 0 0 1px var(--ink-6);
  --shadow-raised: 0 0 0 0.5px var(--ink-a3);
  --shadow-table: none;
  --shadow-panel: 0 0 0 0.5px var(--ink-a3), 0 1px 2px rgb(0 0 0 / .5), 0 4px 16px -2px rgb(0 0 0 / .6);
  --shadow-popover: 0 8px 24px rgb(0 0 0 / .45);
  --shadow-menu: 0 12px 32px rgb(0 0 0 / .45);
  --shadow-modal: 0 24px 64px rgb(0 0 0 / .5);
  --shadow-badge: inset 0 0 0 0.5px color-mix(in srgb, currentColor 30%, transparent);
  --backdrop: rgb(0 0 0 / .9);
}
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* repeat the dark block */ }
}
```

### Tailwind v4 mapping

```css
@theme inline {
  --color-ink-1: var(--ink-1); /* ... through ink-12 */
  --color-line: var(--line);
  --color-panel: var(--panel); --color-raised: var(--raised); --color-overlay: var(--overlay);
  --color-primary: var(--primary-bg); --color-primary-foreground: var(--primary-fg);
  --radius-button: var(--radius-control);
  --text-xs: var(--text-meta);     --text-xs--line-height: var(--leading-meta);
  --text-sm: var(--text-body);     --text-sm--line-height: var(--leading-body);   /* the base is 13px, not 14 */
  --text-base: var(--text-title);  --text-base--line-height: var(--leading-title);
  --text-xl: var(--text-section);  --text-xl--line-height: var(--leading-section);
  --text-2xl: var(--text-figure);  --text-2xl--line-height: var(--leading-figure);
  --text-3xl: var(--text-display); --text-3xl--line-height: var(--leading-display);
  --font-weight-normal: var(--font-weight-ui);   /* one weight */
  --font-weight-medium: var(--font-weight-ui);
  --font-weight-semibold: var(--font-weight-ui);
  --breakpoint-md: 48rem;
}

/* The curves, defined once. A plain @theme emits them as variables, so the
   ease-out utility and var(--ease-out) are the same value. Leave them out of :root. */
@theme {
  --ease-out: cubic-bezier(.23, 1, .32, 1);
  --ease-move: cubic-bezier(.22, 1, .36, 1);
  --ease-drawer: cubic-bezier(.32, .72, 0, 1);
  --ease-in-out: cubic-bezier(.77, 0, .175, 1);
}
```

**Gotcha:** in Tailwind v4 the bare `ease-out` class is `cubic-bezier(0, 0, .2, 1)` unless `--ease-out` is redefined in `@theme`. Writing `ease-[var(--ease-out)]` (or `[transition-timing-function:var(--ease-out)]`) in recipes avoids the trap either way.
