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

Built from the People wireframes (the list and the person drawer), then reworked. Only their content is
used; the wireframes' side nav and tabs are the app's own. In the prototype this is the one Admin Panel tab that shows content
(`HIDE_MODE_CONTENT` is still on, but every Admin Panel tab, Team settings and Settings are now exempt from it). The old floating "Panel | Architecture" switch and its
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
- **People table.** Person, Teams (names, "All teams" for an admin in none, "No team"), Role, Status. There is no "…" row menu: everything happens in the drawer. All filters are
  multi-select: Team, Role and Status are three Routines "Show:" style multi dropdowns side by side (menu stays open, "Team: 2 selected", Clear row; no divider, no counts). Status has Active and Invited ticked by default, so removed people are hidden until Disabled is ticked (Clear filters returns to that); search matches name and email. A person matches the Team filter when they are in
  any ticked team. Toggle pills (`.wl-pill`) are still used on Tasks and Routines.
- **No row menu** (removed 2026-10-30). Disable, Restore access, Reset 2FA and Resend invitation (invited people, in the Security section) are buttons in the person drawer. The code of the menu is kept as orphaned.
- **Banners (hidden for now, `ADM_SHOW_GAPS = false`).** One `.dc-banner--warn` (the banner in the warning tint) per open gap: a team with no head (Assign head opens that team's drawer, where
  the head is chosen; approvals go to the admins meanwhile) and invitations unanswered for 7+ days (Resend all). Derived from the data, so
  fixing a gap removes its banner.
- **Disable, not Archive** (renamed from Remove 2026-10-30; the status is Disabled). One way out: "Disable in Organization". The person loses access and cannot sign in; history stays (run log,
  routines and tasks keep their author). The dialog is only the title, one line ("They'll lose access to [Company]. To bring them back, you'll need to invite them again.") and Cancel / Disable. There is no picker: in the prototype their routines and tasks are handed to the default person (head of one of their teams, else an admin) without asking. No toast is shown after removing: the drawer switching to Removed is the confirmation. Removing a head leaves their teams without one. Removed people stay in the
  list with a Disabled status and can be restored (drawer). No hard delete in the UI (deletion requests go through support).
- **Person drawer** (the existing right side panel, 400px), built from the People user wireframe. Header: name with
  the status tag next to it, email (no "Active since" date). Sections: **Role in organization** (full-width dropdown, options Admin or Member), **Teams** (one
  row per team with the person's role in it, a Head / Member dropdown, and a minus-in-circle button to leave the team; "+ Add to team" is a link at the right of the label and
  is not shown for an admin, who sits above all teams), **Security** (Reset two-factor authentication with its Reset 2FA button), and a single
  full-width filled red button "Disable in Organization" under a hairline (no section heading). Not in the drawer on purpose: Company tools
  (they come from the teams, edited in Connections), Mira (the trust level is the person's own setting), "Sign out everywhere" and the
  last-sign-in line. A disabled person shows only the text "Disabled in [Company]. Can’t sign in. History is kept. Restoring brings the access back." and a full-width Restore access button below it: no frame, no section title, no Role in organization (the wireframe does not cover that state).
- **Team drawer.** A Teams row opens it (a team without a head shows a dash in the table): Head (picker), Members with the count next to the label, "Members (3)" (each row is the sidebar profile element: avatar, name, the derived role underneath, and the status tag next to the name as in the People table; a remove x leaves this team only; "Add people" opens a modal built on the Share modal: a chip field to search by name or email, and the list
  of everyone below it, always visible; people already in the team are greyed out and marked "In team" (listed last); pick several, then "Add". Adding moves nobody; admins are not offered) and, under the members list, only a full-width red "Delete team" button like the one in the person drawer (no section title, no card, no text). It is allowed only when the team has no people; otherwise it is disabled and its tooltip says "You can’t delete this team because it has people in it." An empty team only asks for a simple confirmation.
- **Open button on rows.** A table row that opens something (a person, a team, an Insights row with a detail) shows a small "Open" button next to the first column's content while it is hovered or focused (`.in-open`, hidden otherwise). It is only an affordance: the click bubbles to the row, which opens it.
- **Drawer header spacing.** Drawer headers have equal space above and below their content (16px each). This applies to every header with a two-line title ("From library" panels, the person drawer) and to the People drawers (`.adm-head`), which also share one content height so the person and team headers are the same size. The Mira chat header is left as it was: its 6px top offset lines the title up with the workspace name in the sidebar. In the People drawers the first section starts 20px under the header.

Simulated or unfinished:

- All data is placeholder and lives in memory (a reload resets it). The company name in "Disable in Organization" is a constant (`ADM_COMPANY`).
- Add team does nothing here (the Add team modal is on the Teams tab).
- **Invite people and Restore access** (2026-10-30) open the Share modal in invite mode: no tabs, the field takes emails (Enter, comma, space or leaving the field turns them into chips), the pill offers Admin or Member instead of Can edit / Can run, the button says Invite, and nothing sits under the field. Restore access opens it with the person already in the field and their role preselected; inviting sets them to Invited (they are not Active again until they accept, which does not exist in the prototype). A new email is added as an Invited person with no team. SIMULATED: no email is sent; the toast is "Invitation sent to …". The generic text field (`.field` with a `.field__action`, e.g. the Share and Invite modals) keeps its dropdown top aligned, in the first line, when chips wrap onto more lines. 
- Reset 2FA and Resend invitation only confirm with a toast.
- The roles info popover (three roles, one line each) has no trigger on screen; its code is left orphaned.
- The team `tools` data is kept but no longer shown anywhere in People.
- Approval routing ("head of the routine's team, else admins") is described here and in the banner copy but is not simulated.
- The side panel is hidden below 900px across the whole prototype, so the drawers cannot be opened on narrow screens.
- Governance's Team filter (AI usage) lists these teams (Connections & channels uses them too, see section 9).

## 9. Admin Panel: Connections & channels (2026-10-30)

Built from the Connections & Channels wireframes (Tools view). The wireframes' side nav and top tab bar are not used: the sections are the Admin Panel's own side nav, which is unchanged apart from the rename (the tab used to be called Tools & access).

**Naming (2026-10-30):** "Tools" is now "Connections" in all visible text of this area: first tab, table column, search placeholder, "Add connection" button, Add connections modal, empty states and the Member role description. Code names (`ADM_TOOLS`, `adm-tool*`, `adv-tools`) are unchanged. Routines keep "Tool:" in their filter and sample data, because there it means the app a routine uses.

- **Header.** Title, then a subtitle styled exactly like the one under the Tasks title (`.wl__subtitle`: 14px, wraps to a second line when long): the tools people may connect and the most Mira can do in each; each person still decides within these limits.
- **Two tabs** under it (the same underline tabs as People | Teams): Connections and Channels. The wireframes' **By team** tab is hidden for now (not built; no code path shows it).
- **Channels tab** (built 2026-10-30 from the Channels wireframe): two cards (no intro line, and no In-app chat card). Slack, the company channel (Installed badge, a Workspace line with a red outline Uninstall button aligned to its right (it asks first); once uninstalled the card shows an Install Slack button instead). WhatsApp & Telegram (their two logos side by side and one purple "Allowed" checkbox; unticking asks first). SIMULATED: nothing is installed, uninstalled or disconnected for real; the state lives in memory. The Telegram logo is the app's; WhatsApp has no asset yet, so a simple stand-in logo is drawn (`ADM_WHATSAPP_LOGO`) to be replaced. "Go to Channels" in the Connections empty state opens this tab.
- **Tools tab.** Two parts side by side. Left, straight on the page like the People table (no card around it): a toolbar (a multi-select Autonomy level dropdown on the left; the collapsible search and the primary "Add tool" button on the right, just above the table, as in People; the specification asked for it in the page header, but it was kept in the toolbar on request) and the table (Tool with logo and name only, no kind line under it; Autonomy level as a tag; Available to). A row opens the tool drawer and shows the same hover "Open" button as the People rows. Right: "The three levels" (Observer, Assistant, Operator, one line each) as a borderless panel in the canvas grey of the side nav (`--c-bg`). The wireframe's shield line under the table was dropped on request.
- **Autonomy levels** (the wireframes call this column Access). Observer: Mira reads and tells you what she notices. Assistant: Mira drafts, people approve before anything goes out. Operator: Mira acts internally, external messages still need an OK. Custom: individual permissions set by the admin that no single level describes.
- **Filters.** The Team filter keeps a tool when it is open to all teams or lists a ticked team. The Autonomy level dropdown keeps tools at any ticked level (none ticked = all). Search matches tool name and kind (the kind is still in the drawer header). When nothing matches, the table shows the Routines empty state ("No tools match “query”", or "...the selected filters") with an Add action (see Add tool modal below). There is no Team filter on this tab. Team names in Available to are the ones from People.

Simulated or unfinished:

- The "Defaults for new tools" setting does not exist in the prototype: the modal uses a constant (`ADM_TOOL_DEFAULTS`: Assistant, all teams). The catalogue is the onboarding's list of names and categories without Slack (14 tools; Slack, WhatsApp and Telegram are channels, not tools, so they are not in the catalogue, and searching for one of them shows "X is a channel, not a tool" with a Go to Channels button); most have no logo asset and show the plug icon. The permission rows in step 2 are Gmail's for every tool. Nothing is saved anywhere but in memory.
- Placeholder data in memory (a reload resets it). Levels are mixed (Observer, Assistant, Operator, and Gmail and HubSpot as Custom); the wireframe has every tool as Observer.
- There is no row menu.
- **Add connections modal.** One step only (there is no second step and no "Available to"). Opened by the Add connection button above the table and by the Connections empty states (both open the same modal). Built on the Add people modal shell.
  - *Catalogue:* ticked tiles move to a "Selected" section above the search (the results below never repeat them) and only those tiles have a purple "Customize" link, which opens the onboarding-style "What Mira can do in X" modal in place of the catalogue (Cancel / Save; saving also ticks the tile). Selected tiles have a 1px purple border. Below the search: a "Suggested" grid (connections not added yet, the popular ones first, nine at most) or, while searching, a result count and the matching tiles (connections already in the company stay visible with an "Added" tag and cannot be picked). Each tile has the app's purple checkbox at the right. No category pills.
  - *Adding:* the footer button "Add to Organization" (disabled until something is ticked) adds every ticked connection with the defaults (`ADM_TOOL_DEFAULTS`: Assistant, all teams) or what was customized.
  - External sending rows (outside the company, forwarding externally) can never be Allowed, in the modal and in the drawer: Allowed is disabled on them and they are only Blocked or Ask.
  - *After adding:* the modal closes, the level filter and the search are cleared so the new rows are visible, the new rows flash once, and a toast says "Gmail added. People can now connect their accounts." There is no reminder and no connection status: connecting an account is the person's own decision.
- **Tool drawer** (the existing right side panel), built from the Gmail drawer wireframe: header as in the onboarding (logo, title "What Mira can do in X", and "Applies to everyone at [Company]." or "Available to 2 teams."; no kind or "Email ·"), then straight the three level cards (no section title, the header says it); the cards, group labels (small mono capitals over a hairline) and the Blocked / Ask / Allowed radio buttons (purple when selected) copy the onboarding's permission modal instead of the app's segmented switches (`.adm-preset`, `.adm-pgl`, `.adm-pseg`; also used in the Add connection modal). Custom shows no selected card and the note "Custom settings. Pick a level to reset." (selected one outlined in purple) and every permission as Blocked / Ask / Allowed in three groups (Reading, Creating, Sending and approving), "Available to" checkboxes (All teams plus each team; ticking every team becomes All teams, unticking All teams leaves none; with only some teams ticked, All teams shows a dash, and clicking it ticks every team) and a red full-width Remove. Choosing a level card sets all permissions to that level's preset. Changing a single permission recalculates the level: it stays on a level only if the permissions match that level's preset exactly, otherwise the tool becomes Custom (so Custom is never picked by hand). The table and the drawer header follow at once.
- Each permission row stays in two columns (label left, control right); a long label wraps to two lines. The permission rows are Gmail's for every tool (placeholder), and the presets are invented from the wireframe (Observer reads only; Assistant also drafts and labels, sending needs approval; Operator may also send inside the company). Remove only drops the tool from the in-memory list, with a toast. The drawer closes when you leave the tab or switch to By team / Channels.
- The old tool list plus "What Mira can do" editor was removed; it is recoverable from git history.
- Xero and Granola have no logo asset and show the generic plug icon.
- The autonomy level tag uses the grey tag style; the wireframe's outlined pill has no existing component.

## 10. Admin Panel: other sections (2026-10-30)

**Empty states (2026-10-30).** People with no match: "No one matches", "Try another search or invite them to the app." and an Invite people button (the same modal as the toolbar button); there is no Clear filters button any more. Connections with no match: the title says what is missing, with an "Add connection" button that opens the Add connections modal.

**Search boxes.** Every search box has a small x inside it while it holds text; clicking it empties the box and keeps the focus. The collapsible toolbar search already had one; the sidebar chat search, the Add connections search and the Add people search now get it from one shared helper (`addSearchClear`). The chip fields of the Share and Invite modals are not search boxes and have none.

**Add team modal.** The Add team button of the Teams tab opens a modal: Team name (the button stays disabled while it is empty or a team with that name exists, with a red line saying so), Head (full-width dropdown of the active people, default No head; choosing a head also ticks them in the list, and unticking the head resets it to No head), and People (a search box with a clear x over a list of rows with the app's checkboxes; admins are not offered, they sit above all teams; disabled people are not listed). Cancel / Add team. Creating adds the team with no connections, adds the ticked people at once (no acceptance) and a toast says "Marketing added with 2 people". Not built: opening the new team's drawer afterwards. SIMULATED: memory only.

**Teams need no acceptance.** Adding someone to a team (Add people in the team drawer, or Add to team in the person drawer) takes effect at once: the person does not accept or confirm it, and nothing shows "Pending". The only thing a person accepts is the invitation to the app (the Invited status).

**Available to removed (2026-10-30).** The Connections table has no "Available to" column and the tool drawer has no "Available to" section (the team lead will decide who gets a connection later). The drawer header always says "Applies to everyone at [Company].". The data (`teams`) is still in the prototype; it has no effect on any screen.

**Checkboxes.** Every checkbox in the app is drawn like the onboarding's (one global rule on `input[type=checkbox]`): 20px, 6px corners, grey border; ticked = filled brand purple with a white tick; the partial "All teams" state = filled with a white dash.

**Page headers.** Every Admin Panel page has a title and a subtitle in the Tasks style (`ADM_SUBTITLES`, rendered by `admHead`). People: "Who is in [Company], their role and their teams." and Connections & channels use the agreed text; Mira's is agreed too (section 10b); Governance's is agreed (section 10c); Company knowledge's is PLACEHOLDER wording to be replaced.

The Admin Panel side nav is now People, Connections & channels, Mira, Governance, Company knowledge (Rules, Monitoring and Run log were removed, 2026-10-30; see section 10c). Company knowledge is disabled for now (on request): it stays in the nav, not clickable (`mock:true`, like the Ops workspace row) and drawn at 60% opacity (`dim:true`, so it looks disabled; other disabled items such as Agriculture are unchanged); its title-only placeholder page is still in the code. Overview from the wireframes' tab bar is not added.

## 10b. Admin Panel: Mira (2026-10-30)

Built from the "Admin Panel → Mira" brief. Org-level, admins only, the same in every workspace. It covers what Mira can do inside the company: Binome plugins, built-in tools, skills, ready-made agents (presets) and company-wide instructions. External tools stay in Connections & channels. Everything reuses existing components: the page header, the underline tabs, the Connections list rows and letter tiles, the Routines toggle (`.rt-toggle`), grey and purple tags, cards, the right drawer with the Connections permission editor (Blocked / Ask / Allowed), the `rtConfirm` modal, the share chips and the "+ Add to team" menu. The sidebar is unchanged. Mira is exempt from `HIDE_MODE_CONTENT`.

- **Header.** Title Mira. The subtitle is the agreed text without its last sentence ("External tools are in Connections & channels." was removed on request, so there is no link to that page any more; the link handler in `wireAdmMira` is left unused). Tabs: Plugins, Skills, Agents, Instructions (the Presets tab was renamed Agents on request; the code names and the rest of this section still say preset).
- **Plugins.** HR, Pricing, Org structure: letter tile, name, one line (the "N capabilities" tag was removed on request), toggle with the status word "Active" or "Disabled" before it, like "Active" / "Paused" on a routine card (the "On for everyone in [Company]" / "Off for everyone" line was removed on request). All are on by default. Turning one off opens "Turn off HR?" (Cancel / Turn off); turning on has no modal. The modal text for Pricing and Org structure follows the HR wording ("Mira will stop ... for everyone in [Company]. Routines that use X will stop working until you turn it back on."); only HR's was given, the other two are derived. If Org structure is off while HR is on, the HR row shows a warning-tint line ("HR approvals need Org structure. Turn it on or approvals will go to admins.", copy to confirm with the client). A row glows on hover like a Routines card. A row opens the drawer: header with the name and description, then straight the permission editor, capabilities grouped Reading / Creating / Sending, no raw tool names, no level cards. The drawer has no on/off switch (it is on the card) and no "What Mira can do" title or helper line (all removed on request); the HR dependency warning still shows at its top. A disabled plugin shows the grey "Disabled" tag next to its name in the drawer header, the same tag as a disabled person in People. While the plugin is off, every Blocked / Ask / Allowed button in the drawer is disabled (the values are kept); turning it back on from the card re-enables them. (`admPermEditorHTML` now takes optional groups and locked rows; its defaults keep the Connections drawer as it was.)
- **Built-in tools** section (bottom of Plugins) was removed on request. Its data stays only as a group in the preset drawer's "+ Add" menu; the collapse code is left orphaned.
- **Skills.** A list of rows, nothing above it. Each row: name, description, and a toggle with "Active" or "Disabled" before it (as on the plugin cards). Skills are on or off for the whole company: there is no "Who can use it" link or picker (removed on request), and no tag, heading or counter. Data: ERP clarify and Case story on, Margin guard off. No "create skill". The empty state ("No skills yet. Skills Binome builds for [Company] will appear here.") is coded but cannot be reached with the sample data.
- **Presets.** Grid of cards (each glows on hover like a Routines card): name, "Model · tools". There is no Edit button (removed on request): the whole card opens the drawer, with a hand cursor on hover; the switch and the copy icon do not open it. Top right of a card: the Active / Disabled switch (as on plugins and skills; it replaces the old Off tag and Turn off / Turn on button, no modal) and a copy icon that adds "[name] (copy)" next to it, switched on (SIMULATED, memory only). The "Used by" chips and "Not used yet" were removed from the cards on request; Used by is edited in the drawer. A card whose preset uses a plugin that is off shows "[Plugin] is off — this preset can't use it." No "New preset" (to confirm with the client). The drawer: name and description read-only (made by Binome), Model dropdown (a constant list, `ADM_MODELS`, stands in for the models allowed in Governance), Uses (chips with remove and "+ Add" from the company pool), Used by (teams and people, same chips and "+ Add"), Save and Cancel. The drawer edits a copy; Save writes it back, Cancel or the X drops it.
- **Instructions.** On the left: textarea (no visible label and no placeholder, both removed on request; the box is still named "Instructions for everyone" for screen readers), helper, "Last edited by Agata Tonik · just now" (shown only after the first save) and Save, enabled only while the text differs from what is saved (saving empty is allowed: it means Mira's defaults). Leaving with unsaved text, by tab, side nav or Back to app, asks "Discard changes?". On the right, the grey panel of Connections' "The three levels" (`.adm-col--side`) holds a static "Instructions example" (American English, answer first, amounts in USD).

Simulated or unfinished:

- All data is placeholder and lives in memory (a reload resets it). Plugin capabilities, preset descriptions, the pool a preset can use (connection names such as ERP, Sheets, Templates and Tasks are not in the Connections list), the "Leadership" team (not a People team, offered as an extra group in Used by) and the model list are invented.
- Nothing is enforced: turning a plugin or skill off, preset assignments and permission limits do not change Mira or any routine. Routines are not checked for plugin use, so the turn-off warning only describes the effect.
- Preset drawer description is cut to one line in the header. Governance is built (section 10c); the Routines brief settings and Company knowledge pages named in the brief's "Not on this page" list are not. The preset drawer's model list (`ADM_MODELS`) is still a constant: it does not follow the models allowed in Governance.
- The side panel is hidden below 900px, so the drawers cannot be opened on narrow screens (as elsewhere).

## 10c. Admin Panel: Governance (2026-10-30)

Built from the "Admin Panel → Governance" brief. Org-level, admins only, the same in every workspace. It replaces Rules, Monitoring and Run log: those items, pages and the code of Monitoring are gone, and the old ids (`adv-rules`, `adv-monitoring`, `adv-runlog`) are redirected to `adv-governance` (`ADM_OLD_ROUTES`, applied in `setActive`). The sidebar item uses Monitoring's icon. Locking routines lives in Routines, not here. Business dashboards are Insights. Governance is exempt from `HIDE_MODE_CONTENT`.

Reused, not re-invented: the page header, the underline tabs, the Insights table (`.in-table`), the stat cards, the segmented control (`.wl-view`), the dropdown pills, the Mira page's list rows and toggle (`admToggleHTML`), the right drawer with the Mira drawer header and footer, `.field` inputs, tags, the toast and the empty state.

- **Header.** Title Governance, subtitle "How Mira is running across [Company], what it costs, and who can access your setup." Tabs: Health, AI usage, Support & audit.
- **Health.** One container with three numbers in the Key numbers style of Insights (`.dc-kpis` inside an `.in-blk`: small label, big value, hairline dividers between the numbers; separate cards were dropped on request): "24 active routines" (Right now), "312 runs" (Last 7 days) and "2 failing" (Right now; the value is red like the Failing badge, and at 0 it reads "Nothing failing" in the normal text colour). The earlier "96% succeeded" card is gone. Under them a per-team table: Team, Active routines, Failing (the Action column and "View in Routines" are gone). Failing is the number of routines whose current status is Failing in Routines (`rtStatusOf`, one definition, set there), not a count over time: a red badge when above 0, a dash otherwise (not "0"). Rows sort failing first, then A to Z; only the first 20 show, then a "Show all N teams" text button (coded; with 4 teams it cannot appear).
- **Expandable rows (2026-10-30).** A row with failing routines has a chevron and expands on click (Enter or Space too); a row without any does not expand. The expanded part (light grey background) lists that team's failing routines, read-only, one item per routine, indented to line up with the team name (not the chevron) and not spread over the table's columns. Line 1: the routine name (medium) + " — " + why it fails in plain words (regular). Line 2, smaller and secondary: "Owner Lena Berg · failing since 27 Oct". On the right, vertically centred on the item, the link "Open routine" (opens that routine's panel in Routines, in the team's workspace). Several items stack 12px apart with no dividers between them; the table's row line closes the group. "Why" is the same text Routines shows (`rtFailWhy`, also used by the Routines card tooltip). If the cause is a company setting the admin controls (data field `setting`), line 1 is still "routine — reason" (e.g. "Sending in Gmail is blocked for [Company]") and the link on the right becomes the fix, "Change in Connections & channels"; the earlier "Company setting" tag was dropped. This variant is coded and checked with test data, but no sample routine uses it. Clicking the red "2 failing" number expands every failing row and scrolls to the table. The brief said it should also turn on "Failing only", but that chip was removed earlier, so it only expands. The Search teams box and the Failing only chip (and their empty states) stay removed.
- **One source.** The failing count and the Sales row are read from Routines (RT_ROWS: Weekly Sales Digest, "Notion isn't connected"), so connecting Notion in Routines clears it here too. Owner and failing-since are placeholder fields added to that row (`failOwner`, `failSince`). Finance has no routines in the prototype, so its one failing routine (Collections escalation, Lena Berg, 27 Oct, "Xero didn't respond") is a placeholder in `ADM_GOV_FAILURES`; its "Open routine" opens Routines filtered to Failing, since there is nothing to open. The brief's other example (Weekly pipeline report, Maya's HubSpot account) does not exist in Routines, so the real Sales routine is shown instead.
- **AI usage.** Period (This month, Last month, Last 3 months) and Team dropdowns. The first block uses the Connections layout (`.adm-split`, same proportions): on the left the table with the Period and Team dropdowns above it and the By team | By routine control at the right end of that row; on the right, in a column of the same width as Connections' side panel but without its grey box, the spend in the Key numbers tile of Insights (`.in-blk.in-kpi`, like Net revenue): the label ("Spend this month", "Spend last month", "Spend in the last 3 months"; with a team picked "Sales spend this month" and so on), the total ("$412") and "12% more than last month" in secondary text (the comparison wording follows the period). The earlier header above the table (spend left, control right) and the separate Spend card were removed. Table columns: first header "Team" or "Routine" (follows the control), Spend, Share, and a last header that follows the Period ("vs last month", "vs previous month", "vs previous 3 months"); sorted by spend. Both tables (By team and By routine) are paginated, 5 rows per page: under the table, right-aligned, "1-5 of 11" with Previous and Next (the app's small round buttons, disabled at the ends; there was no pagination component, so this one was chosen on request). It is hidden when everything fits on one page (By team has 4 rows, so only By routine shows it with the sample data). The page goes back to the first one when the period, team or breakdown changes. The change is shown with a triangle: ▲ when spend rose, in red (more cost is the bad direction), ▼ when it fell, in green (the `--chart-positive` green), in the table and in the tile's comparison line ("▲ 12% more than last month"); no change is plain text. On a narrow content area the tile drops under the table, as in Connections. When the Team filter is a specific team, the control is hidden and the table lists that team's routines (the team name sits under each routine name). Then Allowed models (Claude Opus, Sonnet, Haiku, each with a toggle and a status word before it, "Allowed" when on and "Disabled" when off (same style as Active / Disabled on the Mira page; an earlier version had no word at all); the last model on has its toggle disabled with the tooltip "At least one model must stay on") and Providers (Anthropic connected; Google Vertex AI and Amazon Bedrock not connected; Connect or Manage opens a drawer with placeholder fields and Save / Cancel). The empty state "No usage yet. Costs appear after Mira's first runs." (no header, no table) is coded for a new company, not reachable with the sample data.
- **Support & audit.** "Give Binome support access" toggle (turning it on starts 24 hours and shows when access ends; turning it off ends it at once and also turns the email permission off), "Include email contents" toggle (only shown while access is on, in the same container under the first switch; hidden when it is off), then the Audit log (the "Access history" list with its sample rows was removed on request; recoverable from git history), with a Date range dropdown (Last 7 / 30 / 90 days, default 30) and Export CSV.

Simulated or unfinished:

- All numbers (health, spend, shares, changes, history) are placeholder data in memory. The earlier periods are made up with fixed factors. Nothing is enforced: turning a model off, connecting a provider or giving support access changes no routine, cost or permission. Reloading resets everything.
- **Teams.** The brief's Health table names "Leadership"; the prototype uses the People page's teams instead (Finance, Sales, Operations, Customer projects), as the brief's own copy rule asks. Counts are unchanged.
- **To confirm.** Whether an admin can open the workspace of a team they are not a member of. In the prototype only Sales exists as a workspace, so "Open routine" on the Finance row switches no workspace and shows the current one's Routines filtered to Failing. Providers and Binome support access are marked "to confirm with the client" in the brief (the labels are not shown on screen).
- Export CSV really downloads a small CSV, but its five rows are made up and the Date range only changes the file name and the dates. The Date range is three presets, with no custom range.
- The provider drawer's Save marks the provider Connected without checking anything. Fields are placeholders (a connected provider shows masked dots).
- No confirmation modal is used on this page (the brief did not ask for one). The side panel is hidden below 900px, so the provider drawer cannot be opened on narrow screens (as elsewhere).

## 10d. Team settings (2026-10-30)

Built from the "Team settings" brief. The settings of one team: what the team uses. Structure (who is in the team, who leads it) stays in Admin Panel → People → Teams. It replaces today's Ops → Connections (tool selection per workspace).

- **Entry.** A "Team settings" row at the top of the workspace switcher menu (under the team name, with a divider), only while a team workspace is active and only for that team's head and admins. Members of the team and the Personal workspace never get it. It opens a mode like the Admin Panel (`MODES.team`): the sidebar is replaced by Back to app, the title "Sales settings" and two tabs, Connections and Members; Back to app returns to the page you were on.
- **Header.** The sidebar label says "Sales settings". The page title and subtitle follow the tab: Connections ("The company’s connections, and which of them Sales uses.") and Members ("Who’s in Sales. Contact Admin to manage people."). The wording is placeholder. The two tabs are the sidebar items, so the page has no tab row of its own.
- **Connections tab.** Table Connection, Mira’s autonomy level, Used by Sales. The list is every connection in the company pool (`ADM_TOOLS`), so a connection an admin adds is ON for every team by default. The level is read-only, a grey tag with the tooltip "Set by admins for all of [Company]." There is no "Ask for more" link any more (removed on request): asking for more permissions goes through the banner below the table. The modal for it is still in the code, unused. Turning a connection OFF asks first ("Stop using HubSpot in Sales?", Cancel / Turn off, the red confirmation used for plugins); turning it ON has no modal.
- **Banner.** A purple banner (the existing `.dc-banner`) under the table: "Missing a connection or need more permissions?" and "If a connection is missing, or you want to extend Mira’s permissions, you can request it from an admin.", with a white "Request with Mira" button that opens the Mira side panel looking at the page. SIMULATED: Mira's reply is the generic canned one and nothing is sent to an admin. The wording is placeholder.
- **Connection drawer.** A row shows the hover "Open" button like People and opens the right side panel (the switch and "Ask for more" do not open it). It is the Admin Panel's connection drawer cut down to what the team may change, with the company's setting as the ceiling. Level cards: only the levels that fit under the ceiling (Operator shows Operator, Assistant and Observer; Assistant shows Assistant and Observer; Observer shows only Observer). Each permission offers its ceiling value and the ones below it (Allowed, then Ask, then Blocked), so a permission admins keep on Ask shows only Blocked and Ask. The team's choice starts equal to the ceiling. A connection admins set to Custom offers no level cards (no standard level is guaranteed to fit under it), only the permissions. No Remove button. SIMULATED: the team's choice is kept in memory per team and connection and changes nothing else on the page.
- **Members tab.** Read-only list, Person and Role in team (Head first, then Member), from the same data as People. Nothing sits under the list: the "Contact Admin to manage people." line is in the subtitle (the link into People for admins and the head's "ask an admin" line were removed on request; the link code is left unused). No connection status per person.
- **Not on this page:** team structure, head and adding people (People), permission levels (Connections & channels), plugins and skills (Mira), routine approvals (Routines).

Differences from the brief, to confirm:

- **Level names.** The brief names the levels Draft, Read only and Act inside the company. The Admin Panel uses Observer, Assistant and Operator (section 9), so the page shows those, read live from the same data (`ADM_TOOLS.access`), and the rows are the Admin Panel's nine connections, not the six of the brief. They map one to one (Observer = Read only, Assistant = Draft, Operator = Act inside the company). If the plain names are meant to replace the Admin Panel ones, both screens should change together.
- **No kind under the name.** The brief writes "Gmail · Email"; the table shows the name and logo only, as the Admin Panel Connections table does.
- **Data.** The Sales team now has Maya Klein as its head (it had none), so the Members tab matches the brief; Priya Shah stays in Operations, so she is not in Sales' list.
- **Turn off is red.** The confirmation uses the existing red confirm button (`danger`), as when turning a plugin off. The brief's "to confirm with client" is still open.

Simulated or unfinished:

- Everything lives in memory (a reload resets it). Turning a connection off stops nothing, and the request is not sent to anyone (in the application it reaches admins as a notification and an email, not in the Admin Panel, neither of which exists here); no toast is shown, the link changing is the confirmation.
- The prototype user is an Admin in every workspace, so the head-only variant of the entry (workspace role 'Head') is in the code but cannot be reached.
- Not built: the Ops workspace is a disabled mock, so Team settings can only be seen for Sales.

## 10e. Settings (personal) (2026-10-30)

Built from the "Settings (personal)" brief. Each person's own settings: everyone has them, admins included, the same in every workspace. It opens like the Admin Panel (`MODES.settings`): the sidebar is replaced by Back to app, five tabs (Account, Connections, Channels, Mira, Notifications) and, pinned to the bottom, a "Contact Binome" link. It replaces the old three-tab sketch (Mira, My connections, Account); the old `renderMiraSettings` (model choice, system prompt, memory files) is left in the code, unused, because model and system prompt now belong to the Admin Panel. Everything is reused from the Admin Panel (page header, tables, toggle, dropdown pill, tags, confirmation modal, channel cards, the instructions box). The only new CSS is the small `.set-*` layout glue and a disabled state for dropdown items.

- **Connections.** (named Accounts in the brief, renamed on request; the code ids still say `accounts`) Table Connection, Status, Action. The "Used in" and "Mira on your account" columns and the helper line under the table were removed on request. The list is every connection switched on for any of the person's teams (Team settings' off switches count), read live from `ADM_TOOLS`, so it is the Admin Panel's connections, not the five of the brief. The prototype person is in Sales and Customer projects (`SET_TEAMS`). Status is Connected (green), Stopped syncing (red, action Reconnect) or Not connected (grey, action Connect). Disconnect asks first (Cancel, Disconnect, red). The empty state ("Your teams don't use any tools yet. Ask your team head or an admin.") shows when the person is in no team that uses a connection.
- **Connection drawer.** Same pattern as Team settings (section 10d): hovering a row shows the "Open" button, and the row opens the right side panel "What Mira can do in [Connection]", with "On your account, up to what admins allow." underneath. It is the Team settings drawer, one step lower: the ceiling is the company's level and permissions, lowered further by whatever the person's teams chose in Team settings, and the person can only pick the same or less, never more. Only the levels that fit under the ceiling are offered as cards, and each permission offers its ceiling value and the ones below it. A Custom company level offers no level cards, only the permissions. A connection that is not connected has no drawer (no Open button, the row is not clickable); Stopped syncing still opens it, and disconnecting closes the drawer. The Connect, Reconnect and Disconnect buttons do not open the drawer. SIMULATED: the choice is kept in memory (`SET.perms`) and changes nothing else.
- **Channels.** Slack (installed once by admins, so only "Open in Slack"; "Not set up for Organization. Ask an admin." when admins uninstalled it), WhatsApp and Telegram (Connect or Disconnect). The "When something needs you, Mira messages you on" setting from the brief was removed on request.
- **Mira.** The memory card (rows with a source line and Forget, an add field; the title "What Mira remembers about you" and the count were removed on request) and the instructions card (placeholder, helper "Added to every conversation with you, after Organization's instructions.", Save enabled only when the text changed). No model choice, no brief settings.
- **Account and Notifications.** Account (Profile and Security merged, on request) has a Profile block (name and email as read-only text, each with a Change button that only shows a toast) and a Security block: change password, two-factor (with "Lost your device? Ask an admin to reset it.") and sign out everywhere. Appearance and profile variables were removed. Notifications has three switches. The brief says "move as is", but these pages do not exist in the prototype, so their contents are PLACEHOLDER.

Differences from the brief, to confirm:

- **Level names.** The brief writes Draft, Read only and Act inside the company. The drawer shows the Admin Panel's Observer, Assistant and Operator, as Team settings does (section 10d).
- **Rows.** Nine connections instead of five (see above).
- **WhatsApp and Telegram.** If admins switch them off in Connections & channels, the cards say "Not allowed in Organization. Ask an admin." and offer no button. The brief does not cover this.
- **No Disconnect confirmation for WhatsApp and Telegram**, and unsaved instruction changes are not guarded when leaving the page.

Simulated or unfinished:

- Everything lives in memory (a reload resets it). Connect and Reconnect do not open the OAuth flow, they only flip the status and show a toast. Disconnect stops no routine. Open in Slack, Change password, Sign out and Contact Binome only show a toast or a confirmation; WhatsApp and Telegram connect without a sign-in.
- The memory rows and their sources are sample data, a new one gets "From Settings · today", and nothing reaches Mira.
- Removed from Settings on purpose, as in the brief: My department (replaced by Connections), Support access (Governance) and the Mira model and system prompt (Admin Panel).

## 11. Prototype and application handover

See [HANDOVER.md](HANDOVER.md) for how to explain prototype changes and apply them to your current application.
