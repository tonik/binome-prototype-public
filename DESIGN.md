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
- **Insight cards drill down to where the data lives, not to a side panel.** Every card
  that reads from an app has an icon-only button in its header (external-link arrow, next to the
  ···) whose label shows as a tooltip on hover (Open in Tasks, HR, Harvest, Gmail, Salesforce). Only Tasks is a real destination in the prototype; the
  others do nothing on click (simulated). In the app they should open the source app
  scrolled to the matching section. Not added: "Mira noticed" (a summary across sources)
  and cards the viewer has no access to (they show "Request access" instead).
- **Personal sources (mail, calendar) are not handled separately in v1.** Blocks that read
  from a personal source (Gmail, calendar) behave like any other block: the insight runs on
  its owner's access and everyone the dashboard is shared with sees its data. A later version
  should make these "copy only": they cannot be shared live, only sent or copied, and a copy
  runs on the recipient's own access. Handoff note, not built in the prototype.

## 8. Admin Panel: People (2026-10-30)

Built from the People wireframes (`Inputs/Wireframes/Main.dc.html`, `Member.dc.html`), then reworked. Only their content is
used; the wireframes' side nav and tabs are the app's own. In the prototype this is the one Admin Panel tab that shows content
(`HIDE_MODE_CONTENT` still blanks the other Admin Panel tabs and Settings). The old floating "Panel | Architecture" switch and its
sitemap were removed as out of date.

**Model (MVP, flat).** There is no "Department". A **Team** is the only grouping: name, optional head, members, tool pool.
- A person can belong to several teams (no single "home" team). Admins sit above all teams.
- No reporting lines. Each team has an optional head. A team routine is approved by the head of the team the routine belongs to
  (not by the creator's head); a team without a head sends approvals to the admins.
- The data keeps `parentId` on every team (always null) so reporting lines and escalations can be added later.
- **The role is derived and shown everywhere as Admin / Head / Member** (table, Role filter): Admin if the person's organization role is
  Admin; else Head if they lead at least one team; else Member. The only role set by hand is the person's **Role in organization**, whose
  options are Admin or Member (never Head; the dropdown always shows the stored role). Head is a per-team matter: set in the team drawer
  (Head picker) or per team in the person drawer. One head per team; picking a new head replaces the old one. An admin who leads a team
  stays Admin, and that team shows them as Head.

**Screen**
- **Two tabs** under the title, the same component as Tasks | Projects: *People* and *Teams*. The toolbar below follows the Tasks
  toolbar: dropdown filters, a divider, toggle pills on the left; collapsible search and the primary button on the right.
- **People table.** Person, Teams (names, "All teams" for an admin in none, "No team"), Role, Status, row menu. All filters are
  multi-select: Team and Role use the Routines "Show:" multi dropdown (menu stays open, "Team: 2 selected", Clear row); the status
  pills (Active, Invited, Removed) toggle independently; search matches name and email. A person matches the Team filter when they are in
  any ticked team. Toggle pills (`.wl-pill`) have black text and count everywhere (People, Tasks, Routines).
- **Row menu** (text only): Change role, Edit teams (both open the drawer), Resend invitation (invited people), Remove. Removed people
  get only Restore access. Reset 2FA lives in the drawer.
- **Banners (hidden for now, `ADM_SHOW_GAPS = false`).** One `.dc-banner--warn` (the banner in the warning tint) per open gap: a team with no head (Assign head opens that team's drawer, where
  the head is chosen; approvals go to the admins meanwhile) and invitations unanswered for 7+ days (Resend all). Derived from the data, so
  fixing a gap removes its banner.
- **Remove, not Archive.** One way out: "Remove from Organization". The person loses access and cannot sign in; history stays (run log,
  routines and tasks keep their author). The dialog asks who takes over their routines and tasks (pre-selected: the head of one of their
  teams, else an admin) and is skipped when they own nothing. Removing a head leaves their teams without one. Removed people stay in the
  list with a Removed status and can be restored (row menu or drawer). No hard delete in the UI (deletion requests go through support).
- **Person drawer** (the existing right side panel, 400px), built from the wireframe `Inputs/Wireframes/People:user.png`. Header: name with
  the status tag next to it, email, since-date. Sections: **Role in organization** (full-width dropdown, options Admin or Member), **Teams** (one
  row per team with the person's role in it, a Head / Member dropdown, and a remove x; "+ Add to team" is a link at the right of the label and
  is not shown for an admin, who sits above all teams), **Security** (Reset two-factor authentication with its Reset 2FA button), and a single
  full-width filled red button "Remove from Organization" under a hairline (no section heading). Not in the drawer on purpose: Company tools
  (they come from the teams, edited in Connections), Mira (the trust level is the person's own setting), "Sign out everywhere" and the
  last-sign-in line. A removed person shows only Role in organization and an Access card with Restore access (the wireframe does not cover that state).
- **Team drawer.** A Teams row opens it (a team without a head shows a dash in the table): Head (picker), Members with the count next to the label, "Members (3)" (each row is the sidebar profile element: avatar, name, the derived role underneath, and the status tag next to the name as in the People table; a remove x leaves this team only; "Add people" opens a modal built on the Share modal: a chip field to search by name or email, and the list
  of everyone below it, always visible; people already in the team are greyed out and marked "In team" (listed last); pick several, then "Add". Adding moves nobody; admins are not offered) and Delete. Delete is allowed only when the team has no people; otherwise the button is disabled with
  the reason ("Remove its 4 people from the team first"). An empty team only asks for a simple confirmation.
- **Open button on rows.** A table row that opens something (a person, a team, an Insights row with a detail) shows a small "Open" button next to the first column's content while it is hovered or focused (`.in-open`, hidden otherwise). It is only an affordance: the click bubbles to the row, which opens it.
- **Drawer header spacing.** Drawer headers have equal space above and below their content (16px each). This applies to every header with a two-line title ("From library" panels, the person drawer) and to the People drawers (`.adm-head`), which also share one content height so the person and team headers are the same size. The Mira chat header is left as it was: its 6px top offset lines the title up with the workspace name in the sidebar. In the People drawers the first section starts 20px under the header.

Simulated or unfinished:

- All data is placeholder and lives in memory (a reload resets it). The company name in "Remove from Organization" is a constant (`ADM_COMPANY`).
- Invite people, Add team and the "→ Rules" link do nothing. Change role and Edit teams in the row menu only open the drawer.
- Reset 2FA and Resend invitation only confirm with a toast.
- The roles info popover (three roles, one line each) has no trigger on screen; its code is left orphaned.
- The team `tools` data is kept but no longer shown anywhere in People.
- Approval routing ("head of the routine's team, else admins") is described here and in the banner copy but is not simulated.
- The side panel is hidden below 900px across the whole prototype, so the drawers cannot be opened on narrow screens.
- Monitoring's Team filter now lists these teams; Tools & access still uses its own team names ("Customer success") and still lists Slack as a tool.

## 9. Prototype and application handover

See [HANDOVER.md](HANDOVER.md) for how to explain prototype changes and apply them to your current application.
