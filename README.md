# tactile-design

A design-engineering skill for agents and people. It teaches how to build **tactile, layered, quiet** product interfaces, and how to make the decisions behind them.

Tactile: controls look like objects. White buttons sit on the page with a hairline and a small shadow, and squeeze slightly when pressed. Fields sink in as wells. The one action that matters is solid black.

Layered: every surface knows what it sits on. In light mode a raised surface is white with a soft shadow. In dark mode it is one step lighter with a hairline edge, because shadows don't read on a dark ground. Sizes never change between themes.

Quiet: one type weight, a neutral palette, 0.5px lines, very little colour. Hierarchy comes from contrast steps, not from bold text or bright hues. Motion is short and always has a reason.

The skill is distilled from building a production dashboard through hundreds of rounds of pixel-level design review. It records the rules and the thinking behind them, so an agent can apply them to a new screen instead of copying an old one.

## What it covers

- **Decision order**: what to settle first (hierarchy and the next step), then how much data, placement, component, surface, type, colour, motion, phone, and the final review.
- **Buttons and controls**: primary, secondary, utility pills, icon buttons, ghost and destructive tiers; fields, toggles, switches, choice rows, badges; press, hover, focus, disabled, loading and copy states.
- **Surfaces and layering**: ground, panel, raised, well and overlay; shadow recipes for light and dark; modals, bottom sheets, menus, popovers, toasts, cards and tables.
- **Layout and hierarchy**: column widths, spacing rhythm, proportion rules, where actions go, how much data to show, empty and loading states, copy.
- **Motion**: whether to animate at all, curves, durations, springs, patterns with exact values, interruption, reduced motion.
- **Mobile**: touch targets, type floor, stacking, dialogs as sheets, sheet drag physics, native-feeling touch.
- **Tokens**: a role-based token system (12-step neutral, hue scales, hairlines, shadows, radii, durations, easings, spacing) with a template for remapping to your own typeface and colours.

## Layout

```
tactile-design/
├── README.md
├── LICENSE
└── skills/
    └── tactile-design/
        ├── SKILL.md                      # posture, decision order, rules at a glance, never-ship list
        └── references/
            ├── tokens.md                 # role-based tokens, default values, remapping template
            ├── buttons-and-controls.md
            ├── surfaces-and-layering.md
            ├── layout-and-hierarchy.md
            ├── motion.md
            ├── mobile.md
            └── decisions.md              # recurring decisions and how they were made
```

## Install

### Claude Code

Copy or symlink the skill folder into your skills directory.

```sh
# copy
cp -R skills/tactile-design ~/.claude/skills/

# or symlink, so a git pull updates it
ln -s "$(pwd)/skills/tactile-design" ~/.claude/skills/tactile-design
```

For a single project, use `.claude/skills/` inside that project instead of `~/.claude/skills/`.

With the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add kosta4a/tactile-design
```

Claude loads the skill when a task matches its description (building or reviewing product UI). You can also ask for it by name: "Use tactile-design to build the settings page" or "Review this modal against tactile-design".

### Other agents

The skill is plain Markdown. Point your agent at `skills/tactile-design/SKILL.md` and let it open the reference files as needed.

## Adapt it to your brand

The skill names **roles and ratios**, not hex codes. The concrete values (a warm 12-step neutral, Inter, a 13px base) are only the defaults. To use your own:

1. **Neutral.** Pick any 12-step neutral scale (gray, slate, mauve, olive, sand, or your own) for light and dark, once, at setup. Pairing it with your brand hue is fine (slate with blue, mauve with purple). Map it to `--ink-1` ... `--ink-12`. Steps 1–2 are grounds, 3–5 fills, 6–8 lines, 9–10 faint text, 11 secondary text, 12 strong text. Then run the contrast gates in `tokens.md`.
2. **Brand colours.** Turn each into a 12-step light scale and a 12-step dark scale (the Radix custom palette generator does this). Use step 3 as a tint, 6 as an edge, 9 as a solid, 11 as text, and the scale's contrast colour for text on step 9. Status colours (success, danger, warning, info) keep their meaning; a brand hue close to one becomes that status's scale. Never mix brand hue into the neutral steps.
3. **Typeface.** Pick one sans for everything and one mono for machine values (IDs, code, keys). Then follow "Typeface fit" in `tokens.md`: `font-size-adjust` matches its size to Inter's, a side-by-side test picks the one weight, and tracking and font features are checked.
4. **Shadow ink.** Set `--shadow-ink` to your light ink-12. Every light-mode shadow reads it; dark shadows stay black.
5. **Radius.** Set `--radius-control` to match your icon set (4–8px). The rest of the radius ladder follows from it.
6. **Keep the ratios.** Control heights, the 0.5px hairline, the shadow ladder, durations and the spacing rhythm carry over unchanged.

`references/tokens.md` has a complete CSS custom-property template with light and dark values, the surface step-shift classes and a Tailwind v4 mapping.

## Credits

- Distilled from designing and building a production dashboard, including the designer's own review notes and the reasons recorded next to each decision.
- Motion principles, and mobile native-feel and sheet drag conventions, follow the teaching of **Emil Kowalski**. The skill paraphrases his ideas and credits them where they apply. His course is at [animations.dev](https://animations.dev). Any mistakes in the paraphrase are ours.
- The colour model uses the 12-step scale structure of [Radix Colors](https://www.radix-ui.com/colors). The default neutral is Radix's sand scale.
- Touch-target and sheet conventions follow Apple's Human Interface Guidelines.

## Contributions

This repository doesn't take pull requests or issues. Fork it and adapt it freely under the license.

## License

MIT. See [LICENSE](LICENSE).
