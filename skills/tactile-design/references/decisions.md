# Decisions

The designer's recurring decisions, distilled from hundreds of review notes and the reasons recorded next to the code. Where they changed their mind, only the final call is listed. Use this file to predict what they would decide about something new.

## 1. How they think

| Habit | What it looks like | What to do |
|---|---|---|
| **Small, pointed steps** | One element and one change per note, in units of 2px, one ink step, or 5% of opacity or scale. Reverts fast when a change overshoots. | Propose the smallest change that fixes the problem. Don't bundle unrequested changes. |
| **Reuse before inventing** | Asks to use what already exists and to match an earlier design or animation exactly. Earlier designs are the reference. | Find the existing recipe and copy it exactly. In an existing product, find the role constants (primary and secondary button strings, pill, sheet action, zebra and table helpers) and import them. Treat library primitives (a stock Button, Input, Badge, Table) as legacy unless they already carry the recipe: check press scale, hover, fill and border before reusing one. Invent only when nothing fits, and say so. |
| **Remove before adding** | "Remove" is the most common verb: separators, borders, containers, icons, arrows, eyebrows, timers, badges, shortcuts, descriptions. Added decoration (stripes, patterns, glows, shapes) usually comes back out. | When something feels busy, take something away first. |
| **Match the neighbour** | Most notes are "match X to Y": height, padding, radius, colour, text size. | Treat unmatched siblings as bugs. Check neighbours before choosing any value. |
| **A rule becomes global at once** | Asks for every instance at once: every modal, every badge in every size, an app-wide audit. | When you change a pattern, find and change every instance. |
| **Hierarchy from contrast steps** | Primary fill > white with shadow > fill > text. One primary. Hue saved for meaning. | Rank with fill and ink before size; never with bold or colour. |
| **Show only what this state needs** | New and returning users see different pages. Content is gated by plan, progress and flags. Features are hidden, not deleted. | Ask "who is looking, and what can they do now?" before adding anything. |
| **Light first, dark on purpose** | Leaves dark mode alone until it is named. Elevation is designed per theme; sizes never fork. | A light-mode restyle doesn't touch dark. Dark changes colour and shadow only. |
| **Pixel-level checks** | Spots 1px seams, crops, double borders, misaligned strokes. Wants every state previewed with mock and worst-case data, and a key to flip the theme. | Zoom 4–8× in both themes before saying done. Preview empty, loading, error, long and short data. |
| **Motion as a quick sequence** | Choreographed in steps, never all at once, and cut wherever it costs speed. | Stage: content out, frame moves, content in. Instant for drag, resize, keyboard and frequent actions. |
| **Asks why, then picks the simpler option** | Asks whether something is common practice, and which option makes more sense and why. | Give a short reason with each recommendation, then default to the simpler choice. |
| **Plain words** | Short bullets, no walls of text, in the UI and in replies. | Write the way the UI should read. |

## 2. Action weight

| Decision | Why |
|---|---|
| Primary is the ink-12 fill: black in light, near white in dark. | The darkest thing on a light page wins without needing a shadow. |
| The primary's ring is 1px in the surface colour (white on a white panel), not grey. | It makes the fill read a pixel smaller, level with white buttons. |
| No grey buttons in light: every button is white or the primary fill. | A grey fill reads as disabled and muddies the hierarchy. |
| Cancel is white everywhere. | Cancel is a secondary action, so it wears the secondary look. |
| One primary per page or modal. | Two strong things cancel each other out. |
| Key chips only on each page's primary action. | A chip is emphasis; spreading it around removes the emphasis. |
| Destructive is a white button with red text and icon, never a red fill on the page. The solid red sits only on the final confirm inside the dialog. | The trigger shouldn't shout; the last step should feel unmistakable and never like the safe default. |
| In a "give something up" dialog, the keep option is the primary. | The safe choice gets the strongest weight. |
| Confirm buttons name the action ("Yes, delete"). | The label says what will happen, so the user doesn't need to reread the question. |
| Disabled until the action can work. | A button that errors on press is worse than one that waits. |
| Round pills for utilities and in-row actions; control-radius rectangles for page and dialog actions. | Shape tells you whether an action belongs to the content or to the surface. |
| Loading happens inside the same button. | The user's eye stays where they clicked; nothing jumps. |
| Enter submits; Escape closes. Cancel isn't pre-focused. | Keyboard users get the expected path without a trap. |
| Tertiary is plain text with only the link word underlined. | It's available without competing with the buttons. |

## 3. Controls and sizes

| Decision | Why |
|---|---|
| Every control is 36px in both themes: buttons, inputs, selects, dropdown rows, rail rows. | One height makes a row of mixed controls line up without thought. |
| Button radius 6px (raised from 4px). | Slightly softer, matching the slightly rounded icon set. With another icon set, set `--radius-control` to match it (4–8px). |
| Popovers, modals, tables and the panel at 8px; badges and toggles fully round. | A small radius family keeps nesting predictable. |
| 13px base text, not 14px. | Dense product UI reads better at 13px with a 20px line. |
| All weights one step lighter; one weight renders everywhere. | Weight jumps look loud; size and ink give calmer hierarchy. |
| Button labels tracked −1%. | A touch tighter reads more solid at small sizes. |
| Segmented toggle: 28px pill in a 36px track, track one step darker than its surface; toggles hug. | It sits at control height and reads as a well holding a raised choice. |
| The toggle's spring never lets the pill touch the track edge. | Overshoot that escapes its container looks broken. |
| Choice lists are separate rows with small gaps and no lines. Selected = a stronger fill, no stroke. | Lines between rows add noise; the fill alone says "chosen". |
| Checkbox = the radio made square. Multi-select squares, single-select circles. | One family, one rule for which to use. |
| Icons 16px in buttons with the text 2px closer; often "2px smaller" than first drawn. | Icons support the label; they shouldn't outweigh it. |
| External-link arrows only where a link truly leaves the app. | Arrows everywhere stop meaning anything. |
| Fields are wells with no border; focus deepens the fill. | A field should look like a place to put something, not a button. |

## 4. Elevation and theme

| Decision | Why |
|---|---|
| Elevation is designed separately for light and dark. | Shadows don't read on dark grounds. |
| Light: raised = white + shadow. Every overlay is white with a shadow. | White on an off-white ground plus a soft edge feels like a physical layer. |
| Dark: raised = lighter steps + a 0.5px edge, never a shadow. Buttons in table rows are two steps brighter than their row. | Lightness is the only elevation cue that reads on near-black. |
| Dark ground is below ink-1; the panel is ink-1. | In the dark, the order turns over: the darkest thing is furthest back. |
| The active nav item is a fill only: no shading, no border. | Navigation shouldn't look like a button. |
| Modals always dim the page. A side panel dims only when fullscreen. | Dimming says "the rest is paused"; a side panel doesn't pause anything. |
| Sizes, spacing and shapes are identical in both themes. Never a dark-only class that changes layout. | Switching theme must never shift a pixel. |

## 5. Lines

| Decision | Why |
|---|---|
| Every line is 0.5px; one shade darker in dark. | Fine lines separate without drawing attention; in dark, lighter lines glow. |
| No double borders, ever. Inner radius belongs to the parent. | Doubled edges are the most visible sign of careless nesting. |
| Remove lines more often than add them. | Space and fill group things more quietly than boxes. |
| Menu and drawer separators run edge to edge, with equal space above and below. | Half-width separators look like mistakes. |
| A sticky header's border appears only once content scrolls under it. | A line with nothing beneath it is noise. |
| A soft fade in the background colour beats a hard cut-off. | It says "more" without a scrollbar. |

## 6. Colour

| Decision | Why |
|---|---|
| Choose the neutral once, at setup (pairing it with the brand hue is fine). After that, never mix brand hue into neutral steps. | The neutral carries the structure; changing it breaks every contrast step. |
| Badge formula: hue-3 fill, hue-11 text, 0.5px stroke in its own colour, a faint tinted shadow in light. Every badge, every size. | One formula means any hue works and nothing needs a one-off. |
| Hue means status or identity, never rank: success green, danger red, warning yellow, info blue, inactive grey; identity hues for a category or tier. Status always wins a clash. | If colour ranks importance, it can no longer carry meaning. |
| Status dots share the status text's colour and pulse on one clock. | Dots and words agree; rows beat together instead of flickering. |
| Highlights inside text stay monochrome (ink-12). | A hued word inside a sentence looks like a link. |
| No gradients, patterns or decorative stripes. | All were tried; all came back out. |

## 7. Type

| Decision | Why |
|---|---|
| One sans for everything; mono only for machine strings. A display face for large titles was tried and dropped. (If a brand requires one, see "Non-default faces" in [tokens.md](tokens.md).) | One family is calmer; mono marks "this is data, copy it exactly". |
| Mobile: 15px for body, controls and meta; 14/20 for descriptions under a larger lead; fields 16px. | Readable at arm's length; iOS doesn't zoom fields at 16px. |
| Title-to-description gaps are tight and tuned in 2px steps. | A title and its description are one unit. |
| A badge next to large text grows with it; input text equals button text. | Size follows the neighbour. |

## 8. Placement and proportion

| Decision | Why |
|---|---|
| Actions go above the data they act on. | You choose what to do before you read the result. |
| Paging goes below the table, outside its card. "View all" goes after the content. | Moving through data happens after reading it. |
| A section's action sits right of its title and description. | It's found where the eye finishes reading the heading. |
| Page title centred in the top bar at body size; breadcrumbs at the left replace back buttons. | Location is always visible; there's one way back. |
| Equal padding on all sides of strips, bars and cards; space above equals space below. | Unequal padding reads as misalignment. |
| One left edge for all text; centre text, not markers. | The eye returns to one line. |
| Numbers right-aligned. | Digits compare by place value. |
| Columns hug so nothing wraps, up to a cap for free text; inputs in a modal fill. | Data stays on one line; forms stay aligned. |
| Filters stay on one row and scroll sideways on narrow screens. | A second row of filters pushes the data down and looks broken. |
| Nudges in 1, 2 or 4px; blocks in 8, 16 or 32px. | Product UI rewards density once the hierarchy is clear. |

## 9. Amount of data

| Decision | Why |
|---|---|
| Lists stop at 5–6 rows, then pager or "View all". | A summary page answers "is everything OK?", not "show me everything". |
| One big number with a small label beats many widgets. | The eye needs a single landing point. |
| Remove duplicate widgets; fold secondary numbers into the card they belong to. | The same number twice makes the user wonder which is right. |
| Never show fake data to users. Sections show only if they exist; completed steps hide. | Fake or stale content breaks trust faster than an empty state. |
| Empty state: one row tall, no border, a small icon not in a box, a title and a one-line description. | Empty is a state, not an event; it shouldn't shout. |
| Loading uses the empty layout with a spinner. | The page doesn't change shape when the data lands. |
| Charts: bars for counts and health, a line only where the trend is the question; no left-to-right sweep; the latest bar pulses. | A line invites optimising a trend; sweeps are decoration; the pulse means "now". |

## 10. Modals, sheets, toasts

| Decision | Why |
|---|---|
| Three-part modal (header band, body, footer) everywhere; two parts for a simple confirm. | One structure across the app; users learn where the buttons are. |
| Reuse one modal and change its content in place, holding its size if possible. | Closing and reopening reads as an error. |
| Confirmations state the consequence plainly. | The user should know exactly what breaks. |
| One dropdown component everywhere; rows at button height; the picked row is a step darker, no tick. | Consistency, and a fill is quieter than a tick. |
| Don't move an open menu while items shift. | A menu that jumps under the cursor causes misclicks. |
| Phone sheets: grab handle, no ×, no title band, info grouped in one raised card, buttons below it. | Native sheet habits; buttons inside information cards get lost. |
| Copy gets no toast; the icon becomes a tick. | Feedback belongs where the user is looking. |
| A toast wider than 600px only fades. | A wide expansion reads as a stretch, not a reveal. |
| Errors from a modal on a list page become a toast. | The modal is gone; the toast is the only visible place. |
| One rail notice at a time, by priority; a blocking one can't be dismissed. | Stacked notices compete; a blocking state must stay visible. |

## 11. Motion

| Decision | Why |
|---|---|
| The same fade-up entrance on every page. | Consistency makes it disappear into the experience. |
| Content swaps fade in place, not scale in from the bottom. | A swap isn't an arrival. |
| Choreograph in steps: content out, frame resizes, content in. Lines reveal before text. | Simultaneous changes read as a glitch. |
| Instant on drag, resize and keyboard; handles appear once a panel has settled. | Motion during direct manipulation feels like lag. |
| Springs stay inside their containers. | Escaping overshoot looks broken. |
| Celebration shapes only at moments of success, never on errors or routine saves. | Delight that happens every time stops being delight. |
| Timing tuned by feel: often a bit quicker, especially on tap. | Defaults are a start; check on a device. |

## 12. Copy

| Decision | Why |
|---|---|
| Plain, short, specific: "Live / All", "Done". | Fewer words scan faster. |
| Descriptions must help, usually one line. | A description that restates the title is noise. |
| No eyebrow labels; no em dashes; footnotes break after each full stop. | Quieter, more readable copy. |
| Sentence case for labels; capitals for product names and acronyms. | Title Case reads as shouting in dense UI. |
| "Your balance", not "free". | Value the user holds shouldn't be framed as a giveaway. |
| Shorten long words on small screens. | Truncation hides meaning; a shorter word keeps it. |

## 13. Pitfalls to avoid

- **Define each curve once.** `--ease-out` is the strong curve for motion, hover and colour alike.
- **One curve for measured size changes,** used everywhere (the strong ease-out by default, or Material standard).
- **One control radius token,** and comments that agree with it.
- **One backdrop recipe:** black 30% in light, 90% in dark, 2px blur, in every dialog.
- **One disabled look per control kind:** .6 for buttons, switches and fields, .4 for icon buttons; in dark the primary goes solid (ink-4 fill, ink-8 text).
- **One focus system:** a 2px ink-12 outline on buttons, the hover fill on rows, a deeper fill plus a keyboard cue on fields.
- **Sentence case and the real "…"** in every label.
- **The number above its label** in every figure.
- **ink-11 for every description.**
- **Fields stay 16px on touch;** everything else follows the 15px floor.
- **Use the red-9 token,** never a hard-coded red.
- **Enter from .95, never from 0.** Overshoot only for tiny confirmations and the rare tier.
- **Charts fade in;** bars never grow on load.
- **Library primitives that lack the recipe are legacy.** Bring them in line or use the role constants.

## 14. Handling a new request

1. **Read the note literally.** One element, one change. If it's ambiguous, ask one short question.
2. **Find the neighbour and the existing recipe.** Match it.
3. **Make the smallest change**, in the system's units (2px, one ink step, 5% of opacity or scale).
4. **Apply it everywhere** the same pattern appears, in the same change.
5. **Leave dark alone** unless dark is named, and never change sizes in only one theme.
6. **Preview every state** (empty, loading, error, long, short, worst case) in both themes.
7. **Check at 4–8× zoom.** Seams, double borders, radii, alignment, wrapped labels.
8. **Report in plain bullets**: what changed, where, and anything that conflicts with an earlier rule.

## 15. Worked example

A new **usage page**: what has the account used this period, and is anything about to run out? Walked through the decision order in [SKILL.md](../SKILL.md).

1. **Hierarchy.** Most of the time there is no next step, so no primary. When a quota is near its limit, "Upgrade plan" becomes the one primary. Export and the date range are secondary or a utility pill.
2. **Amount of data.** One figure (used this period) with its meter. Then one natural period of bars (30 days). Then a table of the top 5 items with "View all".
3. **Placement.** 1200px column (a data page). The range toggle and Export sit right of the section title, above the chart. Paging, if any, goes under the table's card.
4. **Component.** A data card for figure, meter and chart; a table for the items; the segmented toggle for 7 / 30 days. No modal.
5. **Surface.** Light: white cards with `--shadow-button` on the panel. Dark: ink-2 cards with a 0.5px ring, no shadow.
6. **Type.** 20/28 section titles; a 24/32 figure over a 13/20 label (ink-11 if ink-10 misses 4.5:1); 13/20 table text.
7. **Colour.** Neutral everywhere. The meter takes its identity hue and turns red at 10% remaining. No hue on the chart unless it means status.
8. **Motion.** Page entrance only. Bars fade in 80ms on a range change. Figures never move.
9. **Phone.** Toggle and Export go under the title, full width. The table scrolls sideways with its first column sticky. The pager count gets its own line.
10. **Review.** 4–8× zoom in both themes. Worst case: a 7-digit figure goes compact (1.2M); a 60-character item name truncates. Check the empty (no usage yet) and failed-load states.

The report, in the §14 format:

- Changed: two primaries (Upgrade, Export) to one; Export is now secondary, and Upgrade shows only near the limit.
- Changed: six small stat tiles to one figure with its meter.
- Moved: the pager from inside the table card to 12px below it.
- Flag: the figure label in ink-10 misses 4.5:1 on white with the default neutral, so it uses ink-11.
