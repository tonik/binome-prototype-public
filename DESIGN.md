# Binome / Mira — Design Guide

Handoff notes for developers. There is no Figma file: the single source of truth
for the visuals is the working prototype `designs/mira-app.html` (single file, vanilla JS,
no build step). Where this document and the prototype disagree, the prototype's
CSS wins — update this file.

---

## 1. Principles

- **Flat and quiet.** Neutral greys, hairline borders, almost no shadow. Colour is
  used sparingly: purple for "on / active", the Ask Mira gradient for the AI entry
  points, black for primary actions.
- **Few neutrals.** The whole app uses 10 grey tokens (section 2). Do not add new
  greys — pick the closest token.
- **Pills everywhere.** Buttons, nav rows, inputs and chips are fully rounded
  (`border-radius: 999px`); cards are 12px.
- **Mono for labels.** Small uppercase eyebrow labels use IBM Plex Mono.

---

## 2. Colour tokens

Defined in `:root` at the top of `designs/mira-app.html`.

### Neutrals

| Token | Value | Use |
|---|---|---|
| `--c-ink` | `#111111` | Active nav item, primary buttons (New routine), send button |
| `--c-text` | `#171717` | Headings, card titles, values |
| `--c-text-2` | `#262626` | Profile name |
| `--c-muted` | `#858585` | Secondary text, inactive nav, icons, placeholders, eyebrow labels |
| `--c-border` | `#ececed` | Card borders, dividers, **the rule between nav and content** |
| `--c-border-strong` | `#e4e4e4` | Off-state toggle, avatar background, hover borders |
| `--c-bg` | `#fafafa` | App canvas and sidebar background |
| `--c-surface` | `#ffffff` | Content panel, cards, inputs |
| `--c-hover` | `rgba(17,17,17,.04)` | Row hover, active nav fill, pill hover |
| `--c-hover-2` | `rgba(17,17,17,.08)` | Stronger hover / pressed |

### Error

| Token | Value | Use |
|---|---|---|
| `--c-error` | `#E90004` | Failing label, risk text/badge text, unread count badge, Delete action |

Legacy aliases (`--ink`, `--name`, `--slate`, `--slate-icon`, `--line`,
`--sidebar-border`, `--pill-border`) still exist and point at the tokens above.
Use the `--c-*` names in new code.

### Brand and accents

| Colour | Value | Use |
|---|---|---|
| Brand turquoise | `--c-brand-turquoise` `#00F4D7` | Brand colour |
| Brand purple | `--c-brand-purple` `#8855D2` | The only purple: active toggle track, active routine status label, selected options, chart colour 1, **tint of every shadow**. Always use the token |
| Brand pink | `--c-brand-pink` `#FF00AB` | Brand colour |
| Content accent | `--accent` = `--c-brand-purple` | Tabs, radios and progress bar in Tasks / Settings (not used in the shell) |
| Success green | `#16a34a` (no token) | "Done" / "Connected" labels, completed-item checks, positive delta on Home tiles |
| Ask Mira artwork | `--mira-flow` | Background image of both Ask Mira buttons (embedded as a data URI; see section 7 for the asset) |

Status tints that are deliberately not grey: risk badge `#f8d2d6` / `#b32430`,
warning badge `#f6e7c4` / `#8a6a24`, selected-option tint `#faf7fe` (with a brand-purple border).

### Shadows

All shadows are tinted `rgba(136, 85, 210, α)` (= brand purple `#8855D2`). Keep opacity, blur
and offset as designed; only the colour is the brand purple. Two ring-style
`box-shadow`s are not tinted because they act as borders: the active routine card
(`0 0 0 1px var(--c-text)`) and the selected settings option (brand purple).

---

## 3. Typography

| Role | Font | Size / weight |
|---|---|---|
| UI text | **General Sans** (400, 500, 600, 700 embedded) | 14px / 500 for nav, buttons; 14px / 400 for body |
| Page title | General Sans | 28px / 600, letter-spacing −0.28px |
| Card title | General Sans | 16px / 600 |
| Chat heading | General Sans | 26px / 500 |
| Eyebrow labels | **IBM Plex Mono** | 12px / 400, uppercase, letter-spacing 0.06em, colour `--c-muted` |

Eyebrow labels are: the nav section label ("Apps"), Home section titles, the
routine labels ("When", "Where it goes"), and the routine status ("Active" /
"Paused"). They must all share the same style — if you add another, reuse
`.nav-group__label` as the reference.

Fallback stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`.

---

## 4. Layout

```
┌───────────┬──────────────────────────────────────────┐
│ Sidebar   │ Content panel (white, flat)              │
│ 240px     │ padding 28px 40px 40px                   │
│ #fafafa   │ left rule 1px --c-border                 │
│ pad 14/8  │                                          │
└───────────┴──────────────────────────────────────────┘
                                  [Ask Mira] fixed 24px from right/bottom
```

- **No floating card.** The content panel runs edge to edge (no margin, no
  radius). The only separator from the sidebar is a 1px `--c-border` line. The
  chat side panel on the right follows the same rule.
- Sidebar items inset 8px from the screen edge; nav items are 38px tall pills.
- Only the content panel scrolls; the sidebar never does.
- Max content widths: Routines/Tasks 1320px, Home 880px, Settings 920px, chat
  empty state 768px.
- Breakpoints used: 1040, 900, 860, 760, 720px (content padding drops to 18px at 720).

### Spacing

Mostly a 4/8px rhythm. Gaps worth knowing: 8px between suggestion pills, 16px card
padding, and card dividers that run full-bleed (they use a −16px side margin to
reach the card border).

### Radii

`999px` pills and buttons · `12px` cards, panels, popovers · `9–11px` small
controls and icon buttons · `8px` small buttons · `50%` avatars and toggle knobs.

---

## 5. Components

### Ask Mira — side-nav row
38px full-pill, background = `--mira-flow` (cover). White label (14px/500), 16px
round avatar on the left, white "new chat" icon on the right. Hover and active
state add a dark overlay (`::after`, `rgba(17,17,17,.1)`), **not** a grey fill, so
the artwork stays visible. Below it sits the list of recent chats.

### Ask Mira — floating button
48px tall pill, fixed bottom-right (24px; 16px on mobile), same artwork, white
15px/500 label, 22px avatar. Hover = the same dark overlay.

### Nav item
38px pill, 16px icon + 14px/500 label. Inactive: `--c-muted`. Active: `--c-ink`
text with `--c-hover` fill.

### Routine card
White, 1px `--c-border`, radius 12, padding 16.
Header: title (16/600) left; status label + toggle right. Description (14px,
`--c-muted`). Full-bleed 1px divider. Two rows ("When", "Where it goes"), each an
18px icon + mono label over a 14px value. Manual routines show a small outlined
"Play" pill on the right of the "When" row.

### Toggle
32×20 track, 16px knob inset 2px. Off: `--c-border-strong`. On: `--c-brand-purple`, knob
slides 12px. The status label next to it turns the same purple when on.

### Chat empty state (Ask Mira page)
Heading "What can I handle for you?" above a single wide pill input, then
suggestion pills.
- **Input:** max 768px, min-height 54px, radius 999, border `--c-border`, soft wide
  glow. Left: `+` icon button. Placeholder "Ask Mira". Right: mic icon and a 36px
  round `--c-ink` send button with a white up-arrow.
- **Suggestion pills** (empty state only): *Plan my week*, *What's slipping?*,
  *Create a routine*, *Prep for tomorrow's call*. Outlined pill, 14px/500, 8px
  gap, wrap and centre. Clicking one fills the input and focuses it — it does not send.
- The side-panel composer and the New-routine composer are separate (`.composer`
  without `--hero`) and keep their own look.

### Logo
Binome wordmark, 11px tall, top-left of the sidebar.

---

## 6. Behaviour notes

- State is global in the prototype: `activeId`, `activeChat`, `activeMode`;
  `render()` rebuilds the sidebar and `renderContent()` fills the panel.
  `setActive(id)` is the single entry point for navigation.
- Hover/focus transitions are short (0.12–0.15s ease). The chat side panel slides
  in over 0.4s.
- Respect `prefers-reduced-motion` (already handled for the side panel; keep doing
  so for new animation).

---

## 7. Known gaps / open points

- Contrast: `--c-muted` (`#858585`) on white is about 3.5:1 — is
  below WCAG AA for small text. Raise it if accessibility becomes a requirement.
- Success green `#16a34a` has no token yet.
- The Ask Mira artwork is embedded as base64 for the single-file prototype. In a
  real build, export the artwork from the repo source `Inputs/Other/colorFlow (2) 1.png`
  and serve it as a static asset under a clean name (e.g. `mira-flow.png`).
- Suggestion pills only fill the input; sending and the in-conversation screen
  are not built yet.
- Legacy alias tokens can be removed once all rules use `--c-*`.
- **Personal sources (mail, calendar) are not handled separately in v1.** Blocks that read
  from a personal source (Gmail, calendar) behave like any other block: the insight runs on
  its owner's access and everyone the dashboard is shared with sees its data. A later version
  should make these "copy only": they cannot be shared live, only sent or copied, and a copy
  runs on the recipient's own access. Handoff note, not built in the prototype.

## 8. Prototype and application handover

See [HANDOVER.md](HANDOVER.md) for how to explain prototype changes and apply them to your current application.

- **Mira app (side nav, Apps group): empty placeholder.** `designs/mira-app.html` lists a "Mira"
  item (id `mira-app`, `sparkles` icon) between HR and Agriculture. It is clickable and
  highlights like Pricing and HR, but the content panel is intentionally blank: there is no
  render branch for it yet. Not to be confused with the Ask Mira chat (id `mira`).
