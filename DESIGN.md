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

### Buttons (`.home-btn`)
One component, one modifier to switch the style: `--primary` (`--c-ink` fill, white text), `--outline` (white fill, grey border; also the default with no modifier) and `--ghost` (no fill, no border, muted text that turns `--ink` on hover, like the nav's "+ New dashboard"). `--danger` is a red fill. All three keep a 1px border, so they are the same size. `--lg` makes any of them 40px high.

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
- **Disable, not Archive** (renamed from Remove 2026-10-30; the status is Disabled). One way out: "Disable in Tonik". The person loses access and cannot sign in; history stays (run log,
  routines and tasks keep their author). The dialog is only the title, one line ("They’ll lose access to [Company]. You can restore it later: they’ll get a new invitation, and their history, teams and role are kept.") and Cancel / Disable. There is no picker: in the prototype their routines and tasks are handed to the default person (head of one of their teams, else an admin) without asking. No toast is shown after removing: the drawer switching to Removed is the confirmation. Removing a head leaves their teams without one. Removed people stay in the
  list with a Disabled status and can be restored (drawer). No hard delete in the UI (deletion requests go through support).
- **Person drawer** (the existing right side panel, 400px), built from the People user wireframe. Header: name with **Department** (2026-11-06, on request): a section right after Teams, same section style, read-only: the department name (tooltip "To change it, update it in Microsoft Entra ID.") and under it, in secondary text, "Synced from Microsoft Entra ID". Departments come from the company directory (Microsoft Entra ID or Google Workspace, synced via SCIM), not from the app. It is shown only when the person has one (no department: no section, no empty state) and never for a disabled person. No table column, no filter, nothing in the Teams tab. Data: `dept: { name, source }` on the person in `ADM_MEMBERS`, one per person for now, shaped so it can later become a list of synced groups that can be added to teams. SIMULATED: nothing is synced; only Maya Klein (Sales) and Lena Berg (Finance) have one, from Microsoft Entra ID.
  the status tag next to it, email (no "Active since" date). Sections: **Role in organization** (full-width dropdown, options Admin or Member), **Teams** (one
  row per team with the person's role in it, a Head / Member dropdown, and a minus-in-circle button to leave the team; "+ Add to team" is a link at the right of the label and
  is not shown for an admin, who sits above all teams), **Security** (Reset two-factor authentication with its Reset 2FA button), and a single
  full-width filled red button "Disable in Tonik" under a hairline (no section heading). Not in the drawer on purpose: Company tools
  (they come from the teams, edited in Connections), Mira (the trust level is the person's own setting), "Sign out everywhere" and the
  last-sign-in line. A disabled person shows only the text "Disabled in [Company]. Can’t sign in. History is kept. Restoring access sends them a new invitation." and a full-width Restore access button below it: no frame, no section title, no Role in organization (the wireframe does not cover that state).
- **Team drawer.** A Teams row opens it (a team without a head shows a dash in the table): Head (picker), Members with the count next to the label, "Members (3)" (each row is the sidebar profile element: avatar, name, the derived role underneath, and the status tag next to the name as in the People table; a remove x leaves this team only; "Add people" opens a modal built on the Share modal: a chip field to search by name or email, and the list
  of everyone below it, always visible; people already in the team are greyed out and marked "In team" (listed last); pick several, then "Add". Adding moves nobody; admins are not offered) and, under the members list, only a full-width red "Delete team" button like the one in the person drawer (no section title, no card, no text). It is allowed only when the team has no people; otherwise it is disabled and its tooltip says "You can’t delete this team because it has people in it." An empty team only asks for a simple confirmation.
- **Open button on rows.** A table row that opens something (a person, a team, an Insights row with a detail) shows a small "Open" button next to the first column's content while it is hovered or focused (`.in-open`, hidden otherwise). It is only an affordance: the click bubbles to the row, which opens it.
- **Drawer header spacing.** Drawer headers have equal space above and below their content (16px each). This applies to every header with a two-line title ("From library" panels, the person drawer) and to the People drawers (`.adm-head`), which also share one content height so the person and team headers are the same size. The Mira chat header is left as it was: its 6px top offset lines the title up with the workspace name in the sidebar. In the People drawers the first section starts 20px under the header.

Simulated or unfinished:

- All data is placeholder and lives in memory (a reload resets it). The company name in "Disable in Tonik" is a constant (`ADM_COMPANY`).
- Add team does nothing here (the Add team modal is on the Teams tab).
- **Invite people and Restore access** (2026-10-30) open the Share modal in invite mode: no tabs, the field takes emails (Enter, comma, space or leaving the field turns them into chips), the pill offers Admin or Member instead of Can edit / Can run, the button says Invite ("Send invitation" for Restore access), and nothing sits under the field. Restore access opens it with the person already in the field and their role preselected; inviting sets them to Invited (they are not Active again until they accept, which does not exist in the prototype). A new email is added as an Invited person with no team. SIMULATED: no email is sent; the toast is "Invitation sent to …". The generic text field (`.field` with a `.field__action`, e.g. the Share and Invite modals) keeps its dropdown top aligned, in the first line, when chips wrap onto more lines. 
- Reset 2FA and Resend invitation only confirm with a toast.
- The roles info popover (three roles, one line each) has no trigger on screen; its code is left orphaned.
- The team `tools` data is kept but no longer shown anywhere in People.
- Approval routing ("head of the routine's team, else admins") is described here and in the banner copy but is not simulated.
- The side panel is hidden below 900px across the whole prototype, so the drawers cannot be opened on narrow screens.
- Governance's Team filter (AI usage) lists these teams (Connections & channels uses them too, see section 9).

## 9. Admin Panel: Connections & channels (2026-10-30)

Built from the Connections & Channels wireframes (Tools view). The wireframes' side nav and top tab bar are not used: the sections are the Admin Panel's own side nav, which is unchanged apart from the rename (the tab used to be called Tools & access).

**Naming (2026-10-30):** "Tools" is now "Connections" in all visible text of this area: first tab, table column, search placeholder, "Add connection" button, Add connections modal, empty states and the Member role description. Code names (`ADM_TOOLS`, `adm-tool*`, `adv-tools`) are unchanged. The Routines filter said "Tool:" until 2026-11-06 and now says "Connections:" (same options, with the logos); sample data and code names (`rtTool`, `data-dd="tool"`) keep the old word.

- **Header.** Title, then a subtitle styled exactly like the one under the Tasks title (`.wl__subtitle`: 14px, wraps to a second line when long): the tools people may connect and the most Mira can do in each; each person still decides within these limits.
- **Two tabs** under it (the same underline tabs as People | Teams): Connections and Channels. The wireframes' **By team** tab is hidden for now (not built; no code path shows it).
- **Channels tab** (built 2026-10-30 from the Channels wireframe): four cards (no intro line; the In-app chat card became the **Mira app** card, 2026-11-06, on request). **Mira app** is first: the Mira avatar in the logo tile, "Where everyone works with Mira: chat, tasks, routines and insights.", and a grey "Always on" tag (`.home-badge--neutral`) where the others have a switch or button (not clickable; later the multiplayer rules per channel will live on this card). Slack, the company channel (Installed badge, a Workspace line with a solid red Uninstall button aligned to its right (all destructive buttons are solid red, `.home-btn--danger`; the outline variant was removed 2026-11-06, on request) (it asks first); once uninstalled the card shows an Install Slack button instead). WhatsApp and Telegram are two separate cards (as in the onboarding), each with its own Active / Disabled switch (the same as the Mira tab's apps and presets; replaced the "Allowed" checkbox 2026-11-06) (`ADM_CHANNELS.whatsapp` and `.telegram`; unticking asks first, "Turn off WhatsApp?" or "Turn off Telegram?"). SIMULATED: nothing is installed, uninstalled or disconnected for real; the state lives in memory. The Telegram logo is the app's; WhatsApp has no asset yet, so a simple stand-in logo is drawn (`ADM_WHATSAPP_LOGO`) to be replaced. "Go to Channels" in the Connections empty state opens this tab.
- **Tools tab.** Two parts side by side. Left, straight on the page like the People table (no card around it): a toolbar (a multi-select Autonomy level dropdown on the left; the collapsible search and the primary "Add tool" button on the right, just above the table, as in People; the specification asked for it in the page header, but it was kept in the toolbar on request) and the table (Tool with logo and name only, no kind line under it; Autonomy level as a tag; Available to). A row opens the tool drawer and shows the same hover "Open" button as the People rows. Right: "The three levels" (Observer, Assistant, Operator, one line each) as a borderless panel in the canvas grey of the side nav (`--c-bg`). The wireframe's shield line under the table was dropped on request.
- **Autonomy levels** (the wireframes call this column Access). Observer: Mira reads and tells you what she notices. Assistant: Mira drafts, people approve before anything goes out. Operator: Mira acts internally, external messages still need an OK. Custom: individual permissions set by the admin that no single level describes.
- **Filters.** The Team filter keeps a tool when it is open to all teams or lists a ticked team. The Autonomy level dropdown keeps tools at any ticked level (none ticked = all). Search matches tool name and kind (the kind is still in the drawer header). When nothing matches, the table shows the Routines empty state ("No tools match “query”", or "...the selected filters") with an Add action (see Add tool modal below). There is no Team filter on this tab. Team names in Available to are the ones from People.

Simulated or unfinished:

- The "Defaults for new tools" setting does not exist in the prototype: the modal uses a constant (`ADM_TOOL_DEFAULTS`: Assistant, all teams). The catalogue is the onboarding's list of names and categories without Slack (14 tools; Slack, WhatsApp and Telegram are channels, not tools, so they are not in the catalogue, and searching for one of them shows "X is a channel, not a tool" with a Go to Channels button); most have no logo asset and show the plug icon. The permission rows in step 2 are Gmail's for every tool. Nothing is saved anywhere but in memory.
- Placeholder data in memory (a reload resets it). Levels are mixed (Observer, Assistant, Operator, and Gmail and HubSpot as Custom); the wireframe has every tool as Observer.
- There is no row menu.
- **Add connections modal.** One step only (there is no second step and no "Available to"). Opened by the Add connection button above the table and by the Connections empty states (both open the same modal). Built on the Add people modal shell.
  - *Catalogue:* ticked tiles move to a "Selected" section above the search (the results below never repeat them) and only those tiles have a purple "Customize" link, which opens the onboarding-style "What Mira can do in X" modal in place of the catalogue (Cancel / Save; saving also ticks the tile). Selected tiles have a 1px purple border. Below the search: a "Suggested" grid (connections not added yet, the popular ones first, nine at most) or, while searching, a result count and the matching tiles (connections already in the company stay visible with an "Added" tag and cannot be picked). Each tile has the app's purple checkbox at the right. No category pills.
  - *Adding:* the footer button "Add to Tonik" (disabled until something is ticked) adds every ticked connection with the defaults (`ADM_TOOL_DEFAULTS`: Assistant, all teams) or what was customized.
  - External sending rows (outside the company, forwarding externally) can never be Allowed, in the modal and in the drawer: Allowed is disabled on them and they are only Blocked or Ask.
  - *After adding:* the modal closes, the level filter and the search are cleared so the new rows are visible, the new rows flash once, and a toast says "Gmail added. People can now connect their accounts." There is no reminder and no connection status: connecting an account is the person's own decision.
- **Tool drawer** (the existing right side panel), built from the Gmail drawer wireframe: header as in the onboarding (logo, title "What Mira can do in X", and "Applies to everyone at [Company]." or "Available to 2 teams."; no kind or "Email ·"), then a section title "Max level for everyone" (2026-11-06, on request; the existing drawer section label) over the three level cards; the cards, group labels (2026-11-16, on request: the same type as the table column headers, 12px medium muted grey in the page font, over a hairline; and the drawer section titles such as "Levels allowed for Sales" use the same type, so no mono capitals in the drawers) and the Blocked / Ask / Allowed radio buttons (purple when selected) copy the onboarding's permission modal instead of the app's segmented switches (`.adm-preset`, `.adm-pgl`, `.adm-pseg`; also used in the Add connection modal). Custom shows no selected card (the note "Custom settings. Pick a level to reset." was removed from the drawers 2026-11-06, on request; the Add connection modal still shows it) (selected one outlined in purple) and every permission as Blocked / Ask / Allowed in three groups (Reading, Creating, Sending and approving), "Available to" checkboxes (All teams plus each team; ticking every team becomes All teams, unticking All teams leaves none; with only some teams ticked, All teams shows a dash, and clicking it ticks every team) and a red full-width Remove. Remove asks first (2026-11-05, on request): the danger confirmation "Remove [Connection] from [Company]?" ("Mira won’t be able to use [Connection] anywhere in [Company], and routines that use it will stop working. You can add it again from the catalog.") with Cancel / Remove; Cancel keeps the connection and the drawer open, Remove removes it and closes the drawer. Each team that was using it gets a bell notification of the same kind as the level changes ("Admins removed [Connection].", "[Team] routines that use it have stopped."), information only, no link. SIMULATED: the heads cannot be someone else in the prototype, so they land in this bell, labelled with the team. Choosing a level card sets all permissions to that level's preset. Changing a single permission recalculates the level: it stays on a level only if the permissions match that level's preset exactly, otherwise the tool becomes Custom (so Custom is never picked by hand). A change applies at once (no Save or Cancel; an earlier version edited a draft behind Save and was taken out on request) and tells the teams, one notification per change. The drawer shows no line about who gets told (an "Affects ..." line and then a "Changes here notify ..." line were both taken out on request). Who is told, per team that uses the connection (not switched off in Team settings; the `t.teams` list is no longer used for this): a team that ends up with less than before (it followed the admin down, or its own choice is clamped) is told it was lowered; if nothing went down but a ceiling went up, every team is told it was raised (a team that follows gets more autonomy without doing anything; a team with its own choice is told it can go higher, and its level does not change); a team already within the new ceiling with no change is not told. SIMULATED: the notifications are not sent to the heads. Each one lands in the bell of the prototype user, who cannot be someone else, as an information item (no Accept or Decline) with the team's name in the tag row, e.g. "Admins lowered Asana to Assistant." with "Sales routines that send in Asana will need approval or stop working." under it. Opening the bell marks them as seen.
- Each permission row stays in two columns (label left, control right); a long label wraps to two lines. The permission rows are Gmail's for every tool (placeholder), and the presets are invented from the wireframe (Observer reads only; Assistant also drafts and labels, sending needs approval; Operator may also send inside the company). Remove only drops the tool from the in-memory list, with a toast. The drawer closes when you leave the tab or switch to By team / Channels.
- The old tool list plus "What Mira can do" editor was removed; it is recoverable from git history.
- Xero, GitHub, Zendesk and Outlook have a logo (the Simple Icons glyph in the brand colour, added on request). Granola and FreeAgent have no logo asset and show the generic plug icon.
- The autonomy level tag uses the grey tag style; the wireframe's outlined pill has no existing component.

## 10. Admin Panel: other sections (2026-10-30)

**Empty states (2026-10-30).** People with no match: "No one matches", "Try another search or invite them to the app." and an Invite people button (the same modal as the toolbar button); there is no Clear filters button any more. Connections with no match: the title says what is missing, with an "Add connection" button that opens the Add connections modal.

**Search boxes.** Every search box has a small x inside it while it holds text; clicking it empties the box and keeps the focus. The collapsible toolbar search already had one; the sidebar chat search, the Add connections search and the Add people search now get it from one shared helper (`addSearchClear`). The chip fields of the Share and Invite modals are not search boxes and have none.

**Add team modal.** The Add team button of the Teams tab opens a modal: Team name (the button stays disabled while it is empty or a team with that name exists, with a red line saying so), Head (full-width dropdown of the active people, default No head; choosing a head also ticks them in the list, and unticking the head resets it to No head), and People (a search box with a clear x over a list of rows with the app's checkboxes; admins are not offered, they sit above all teams; disabled people are not listed). Cancel / Add team. Creating adds the team with no connections, adds the ticked people at once (no acceptance) and a toast says "Marketing added with 2 people". Not built: opening the new team's drawer afterwards. SIMULATED: memory only.

**Teams need no acceptance.** Adding someone to a team (Add people in the team drawer, or Add to team in the person drawer) takes effect at once: the person does not accept or confirm it, and nothing shows "Pending". The only thing a person accepts is the invitation to the app (the Invited status).

**Available to removed (2026-10-30).** The Connections table has no "Available to" column and the tool drawer has no "Available to" section (the team lead will decide who gets a connection later). The drawer header always says "Applies to everyone at [Company].". The data (`teams`) is still in the prototype; it has no effect on any screen.

**Checkboxes.** Every checkbox in the app is drawn like the onboarding's (one global rule on `input[type=checkbox]`): 20px, 6px corners, grey border; ticked = filled brand purple with a white tick; the partial "All teams" state = filled with a white dash.

**Page headers.** Every Admin Panel page has a title and a subtitle in the Tasks style (`ADM_SUBTITLES`, rendered by `admHead`). People: "Who is in [Company], their role and their teams." and Connections & channels ("What Mira connects to across [Company] and where people talk to it.", for both tabs, 2026-11-06) use the agreed text; Mira's is agreed too (section 10b); Governance's is agreed (section 10c); Company knowledge's is "What Mira knows about [Company]." (agreed 2026-11-16).

The Admin Panel side nav is now People, Connections & channels, Mira, Governance, Company knowledge (Rules, Monitoring and Run log were removed, 2026-10-30; see section 10c). Company knowledge was disabled for a while; since 2026-11-16 it is clickable again and built (section 10d). Overview from the wireframes' tab bar is not added.

## 10b. Admin Panel: Mira (2026-10-30)

Built from the "Admin Panel → Mira" brief. Org-level, admins only, the same in every workspace. It covers what Mira can do inside the company: Binome apps, built-in tools, skills, ready-made agents (presets) and company-wide instructions. External tools stay in Connections & channels. Everything reuses existing components: the page header, the underline tabs, the Connections list rows and letter tiles, the Routines toggle (`.rt-toggle`), grey and purple tags, cards, the right drawer with the Connections permission editor (Blocked / Ask / Allowed), the `rtConfirm` modal, the share chips and the "+ Add to team" menu. The sidebar is unchanged. Mira is exempt from `HIDE_MODE_CONTENT`.

- **Header.** Title Mira. The subtitle is the agreed text without its last sentence ("External tools are in Connections & channels." was removed on request, so there is no link to that page any more; the link handler in `wireAdmMira` is left unused). Tabs: Apps, Skills, Agents, Instructions (the Presets tab was renamed Agents on request; the code names and the rest of this section still say preset).
- **Apps.** HR, Pricing, Org structure: letter tile, name, one line (the "N capabilities" tag was removed on request), toggle with the status word "Active" or "Disabled" before it, like "Active" / "Paused" on a routine card (the "On for everyone in [Company]" / "Off for everyone" line was removed on request). All are on by default. Turning one off opens "Turn off HR?" (Cancel / Turn off); turning on has no modal. The modal text for Pricing and Org structure follows the HR wording ("Mira will stop ... for everyone in [Company]. Routines that use X will stop working until you turn it back on."); only HR's was given, the other two are derived. If Org structure is off while HR is on, the HR row shows a warning-tint line ("HR approvals need Org structure. Turn it on or approvals will go to admins.", copy to confirm with the client). A row glows on hover like a Routines card. A row opens the drawer: header with the name and description, then straight the permission editor, capabilities grouped Reading / Creating / Sending, no raw tool names, no level cards. The drawer has no on/off switch (it is on the card) and no "What Mira can do" title or helper line (all removed on request); the HR dependency warning still shows at its top. A disabled app shows the grey "Disabled" tag next to its name in the drawer header, the same tag as a disabled person in People. While the app is off, every Blocked / Ask / Allowed button in the drawer is disabled (the values are kept); turning it back on from the card re-enables them. (`admPermEditorHTML` now takes optional groups and locked rows; its defaults keep the Connections drawer as it was.)
- **App drawer hidden** (2026-11-06, on request). The app rows no longer open their drawer: they are static like the skill rows (no hover glow, no hand cursor, not focusable; only the switch works). The drawer is still in the code behind the constant `ADM_APP_DRAWER` (set it to `true` to bring it back); read the app descriptions above as the hidden drawer's design. The agent cards still open their drawer.
- **Built-in tools** section (bottom of Apps) was removed on request. Its data stays only as a group in the preset drawer's "+ Add" menu; the collapse code is left orphaned.
- **Skills.** A list of rows, nothing above it. Each row: name, description, and a toggle with "Active" or "Disabled" before it (as on the app cards). Skills are on or off for the whole company: there is no "Who can use it" link or picker (removed on request), and no tag, heading or counter. Data: ERP clarify and Case story on, Margin guard off. No "create skill". The empty state ("No skills yet. Skills Binome builds for [Company] will appear here.") is coded but cannot be reached with the sample data.
- **Presets.** Grid of cards (each glows on hover like a Routines card): name, "Model · tools". There is no Edit button (removed on request): the whole card opens the drawer, with a hand cursor on hover; the switch and the copy icon do not open it. Top right of a card: the Active / Disabled switch (as on apps and skills; it replaces the old Off tag and Turn off / Turn on button, no modal) and a copy icon that adds "[name] (copy)" next to it, switched on (SIMULATED, memory only). The "Used by" chips and "Not used yet" were removed from the cards on request; Used by is edited in the drawer. A card whose preset uses an app that is off shows "[App] is off — this preset can't use it." No "New preset" (to confirm with the client). The drawer: name and description read-only (made by Binome), Model dropdown (a constant list, `ADM_MODELS`, stands in for the models allowed in Governance), Uses (chips with remove and "+ Add" from the company pool), Used by (teams and people, same chips and "+ Add"), Save and Cancel. The drawer edits a copy; Save writes it back, Cancel or the X drops it.
- **Instructions.** On the left: textarea (no visible label and no placeholder, both removed on request; the box is still named "Instructions for everyone" for screen readers), helper, "Last edited by Agata Tonik · just now" (shown only after the first save) and Save, enabled only while the text differs from what is saved (saving empty is allowed: it means Mira's defaults). Leaving with unsaved text, by tab, side nav or Back to app, asks "Discard changes?". On the right, the grey panel of Connections' "The three levels" (`.adm-col--side`) holds a static "Instructions example" (American English, answer first, amounts in USD).

Simulated or unfinished:

- All data is placeholder and lives in memory (a reload resets it). App capabilities, preset descriptions, and the model list are invented. The agents' teams and connections are real ones (2026-11-06, on request): "Used by" offers only People's teams and people, and the connections a preset can use are the Connections list (`ADM_TOOLS`), next to the apps and built-in tools.
- Nothing is enforced: turning an app or skill off, preset assignments and permission limits do not change Mira or any routine. Routines are not checked for app use, so the turn-off warning only describes the effect.
- Preset drawer description is cut to one line in the header. Governance is built (section 10c); the Routines brief settings and Company knowledge pages named in the brief's "Not on this page" list are not. The preset drawer's model list (`ADM_MODELS`) is still a constant: it does not follow the models allowed in Governance.
- The side panel is hidden below 900px, so the drawers cannot be opened on narrow screens (as elsewhere).

## 10d. Company knowledge, "Company Brain" (2026-11-16)

Built from the Company Brain spec. It replaces the first, static version of the page (the old `ADM_KNOW_*` data and its Up to date / Archived statuses are gone). Two levels: **Company** entries (managed by admins) and **Team** entries (managed by that team's head). The personal level already exists (Settings, Mira) and is untouched. Everything reuses existing components; the only new CSS is the two diff rules (`.kn-del`, `.kn-add`).

**Roles (as in People).** Admin or Member at org level; Head is a role inside a team. A head who is not an admin has no Admin Panel and manages their team in Team settings, Team knowledge. **Managed by** is derived from Applies to and never edited: `Admins` for Everyone at Tonik, `Head of <Team>` (a role only, no name) read from `ADM_TEAMS` for a team (a team without a head shows `Admins`). Mira's suggestions are not used until someone approves them; entries a person writes go live at once, and an uploaded file becomes a suggestion for review.

- **Admin Panel, Company knowledge** (`adv-knowledge`, `renderAdmKnowledge`). Header and subtitle as before. Two underline tabs under the header, the same component as People | Teams, without icons: **All** and **Needs review** (entries with a Mira suggestion; this replaces the Status filter, and there is no banner). Toolbar: the **Applies to** multi dropdown (options "Everyone at Tonik" and "<Team> team") and, next to it, a **Type** multi dropdown (Policy, Guideline, Process, Reference; `admMultiDdHTML`, and Clear filters covers both), collapsible search, and the primary **Add entry** button. There is no Topic anywhere (removed on request, 2026-11-16). Table columns Entry · Applies to · Type · Updated · Status (no Managed by column; Type added 2026-11-17). The list is flat, sorted Everyone at Tonik first, then the teams in `ADM_TEAMS` order, then by title; there are no group headers (so the earlier per-group **Approve all** is gone; entries are approved one by one on their page). A live entry shows no badge; an entry waiting for review shows the amber "To review" badge. A row opens the entry page. Empty state (only when there are no entries at all): "Mira hasn't found anything yet" with **Connect a source** (goes to Connections & channels) and Add entry. A filter with no match shows the usual "No entries match"; an empty Needs review tab says "Nothing to review".
- **Team settings, Team knowledge** (the tab and the page title say "Team knowledge") (`ts-knowledge`, third tab of `MODES.team`, icon `book`). The same list renderer (`knListHTML`) filtered to the active team, with the same two tabs: only the entries that apply to this team (there are no company-wide rows and no "Company entries you manage" line), no toolbar filter, and the columns Entry · Type · Updated · Status (no Type filter), and nothing under the table (the line "Company-wide entries are managed by admins. Mira uses both." was removed on request). Only Sales has Team settings in the prototype, so this tab can only be seen for Sales.
- **Add entry** opens a small menu. *Write an entry* creates an empty live entry ("New entry", title selected) in the page's team, else in the single ticked Applies to filter, else Company, and opens it. *Upload a file* opens the browser's own file picker (the file comes from the user's computer; there is no picking from Content or Google Drive, because Mira browses the connected sources herself). After choosing, Mira types (`MIRA_TYPING_MS`) and ONE prefilled entry opens **in review** (`kind:'new'`, `from:` the file name), in the page's team, else in the single ticked Applies to filter, else Company. SIMULATED: the file is never read or sent anywhere, only its name is used; a name with "travel" in it (Travel_policy.pdf) gets a travel draft, any other file gets a short placeholder draft that names it. Its Source reads "Travel_policy.pdf · Uploaded by Agata Tonik". There is no draft state.
- **Entry page** (full content area, sidebar stays; it eases in when opened, with the Routines view animation `.rt-view--enter`, an 8px rise and fade of about 0.4s that is off under reduced motion, and it does not replay when the page redraws after Approve or a dropdown change). Breadcrumb by where it was opened from, not by role: from the Admin Panel `Company knowledge / Discount rules` (no scope in the crumb), from Team settings `Team knowledge / Discount rules`. To the right of the crumb a muted edit stamp: seed entries show their date ("Last edited by Maya Klein · 30 Oct"). There is no Save and no Undo: every change (title, body, Applies to) autosaves and the stamp, after a short pause (debounced), reads "Last edited by Agata · just now". Editable title; then **Applies to** (a single-choice dropdown pill in plain words: "Everyone at Tonik" or "Sales team", stored as scope Company or the team; only admins change it, and Managed by follows it), **Type** (a row under Applies to, same pill, see the Type bullet below), **Managed by** (read-only text, never editable, derived from Applies to: "Admins" for Everyone at Tonik, "Head of Sales" for a team, "Admins" for a team without a head; there is no `managedBy` field; this replaces the earlier Managed by dropdown, 2026-11-16, on request), and Source (never "None": a file shows its name and place as a chip in the Home source badge look; an entry written by hand says "Written by <person>"; an entry Mira wrote from a conversation says "Chat in <team>", plain text with no link because a chat may be private; the same in the Admin Panel, in Team settings and in the chat panel; there is no Origin row, removed on request), as label and value rows in two columns (label left, value beside it, no dividers between the rows; the Source file chip is in normal case, "Leave_policy_2026.pdf · Google Drive", not uppercase monospace) in the drawer section look, labels and values at 13px (the source badge keeps its own size); then the editable body (contenteditable) and a red "Delete entry" link behind the confirmation modal (`rtConfirm`). Without the right to manage the scope, the page would have no editing (coded, but the prototype user is an Admin everywhere, so it cannot be reached).
  - **Review state** (`status:'review'`, `suggestion.kind` `change` or `new`): a banner "Mira suggested this change from a chat in Sales. It isn't used until approved." with Approve and Reject (the banner's own white and outlined buttons, not `home-btn`, because they sit on the purple banner). In the body the `removed` text is struck through and muted, the `added` text has the accent background. Only the highlighted new text is editable (editing it edits the suggestion). - **An entry Mira drafted in full** (`kind:'new'`, e.g. Kick-off checklist; an entry made by uploading a file is the same) differs from a change in six ways: (1) the stamp reads "Drafted by Mira · 2 h ago" (an uploaded file: "just now"); (2) the banner reads "Mira drafted this from a project page in Notion. It isn't used until approved." (an uploaded file: "from Travel_policy.pdf") with Approve and Reject; (3) there is no highlight: in a change it shows what is new, on a whole entry it would say nothing and only make reading harder, so the banner alone says it is a draft (this replaces the spec's "whole body highlighted"); (4) the whole title and body are editable before approving; (5) there is no Delete, because Reject deletes the draft; (6) Mira does not use the entry until someone approves it (by the model, not simulated). After the first edit the stamp becomes "Last edited by Agata · just now". Approve makes the entry live and the stamp reads "Approved by Agata · just now". Reject drops a change and keeps the old text; a rejected new entry is deleted.
- **Type (2026-11-17).** One property per entry saying what it is for, not its subject, never a team name. A single choice from a fixed list that is the same for every company (set by Binome, not editable per company): **Policy** (rules people must follow), **Guideline** (the recommended way, not binding), **Process** (steps across people or teams), **Reference** (facts to look up). `KN_TYPES`, `entry.type`. Mira sets it on every entry she drafts; an entry written by hand starts as Policy. Admins change it on any entry, heads on entries they manage (`knCanManage`; read-only text for anyone else, which the prototype cannot reach). On the entry page it is a row under Applies to, the same compact `ddPillHTML` pill; in the review state of a new entry it shows Mira's choice and can be changed before Approve. Changing it autosaves and updates the edit stamp like the other properties. Members never see it: the source chip side panel has no Type row. SIMULATED: for an uploaded file Mira's choice is guessed from the file name (travel is Policy; checklist, process, onboarding or handover is Process; anything else Reference), not read from the file. Seeds: Policy for Leave and time off, Expenses and client entertainment, Security and passwords, Discount rules, Working hours and remote work; Guideline for Tone of voice with clients; Process for Handing a deal over to Customer projects, Qualifying an inbound lead, Month-end close checklist, Supplier onboarding, Kick-off checklist; Reference for Client entertainment budget. Search also matches Type. Not built: Mira does not use Type anywhere (nothing reaches Mira in the prototype).
- **Home, Needs you (changed 2026-11-17).** The Company knowledge review items ("Review Mira's change to ...", "Review a new entry: ...") are no longer in Needs you; they moved to the bell (next bullet). Every other Needs you item is unchanged.
- **Company knowledge in the bell (2026-11-17).** Two new notification kinds, drawn with the existing bell item (avatar, title with bold names, grey badge, time, buttons) and no description line. SIMULATED, in memory. Both go to whoever manages the entry (`knManagedBy`: Admins, or the team's head).
  - **Mira's suggestions** (`knsugg`, grouped). Avatar: Mira's orb (a `.share-ava` with one new rule, `.share-ava img`). Title: "Mira made 3 suggestions for Company knowledge" for any group of more than one (changes and new entries mixed), "Mira suggested a change to Discount rules" or "Mira drafted a new entry: Kick-off checklist" for one. Badge Knowledge. One primary button, **Review** (no Accept or Decline: a change has to be read first). One suggestion goes to that entry's page; once a notification has grouped more than one, Review always goes to Company knowledge on the Needs review tab (filters and search cleared). Grouping: while the notification is unread, a new suggestion for the same recipient joins it (the count updates, the time resets to "Just now", it moves to the top); once read (the bell's usual behaviour: opening the panel), the next one starts a new notification. The count never shrinks; when every entry in it has been approved or rejected the item shows the done state ("Reviewed", the green check of Accepted) and loses its button.
  - **Reports from people** (`knreport`, one per report). Avatar: the reporter's initials. Title: "Jonas Okafor reported Expenses and client entertainment as outdated". One primary button, **Open**, to the entry page. The entry page now has a **Reported banner** (the existing `.dc-banner`, under the review banner when both apply) with the latest report's words, "+N more reports" when there are several, and **Resolve** and **Dismiss**. Either closes every report on the entry and marks all of their notifications done ("Resolved" or "Dismissed"). Nothing in the prototype lets a person make a report.
  - **Seeds.** Admin: one grouped notification of 3 suggestions (Expenses and client entertainment, Leave and time off, Working hours and remote work, each a change). There is no seeded report notification (removed on request, 2026-11-17); the `knreport` kind is still built, and Expenses and client entertainment still carries one report, so its page shows the Reported banner, but no bell item leads to it. Head of Sales: a single change to Discount rules. Head of Customer projects: a single new entry, Kick-off checklist. Members: nothing. Expenses and client entertainment now has a change suggestion (the approval limit, $500 to $1,000) and so shows To review; the $80 that Mira quotes is still its live text. Counts as "new" in the bell while unread or not yet done.
  - **Unfinished or simulated.** The prototype user is an Admin and cannot switch role, so the two head notifications exist in the data but are never shown (`notifMine`), and the head paths of Review and Open (`knOpenTeam`: Team settings, Team knowledge) cannot be reached. Nothing running in the prototype creates a suggestion or a report; `knNotifySuggestion` is how one would arrive and is only used for the seeds. Nobody is told when a report is resolved or dismissed. The tab the spec calls "To review" is the existing **Needs review** tab (and the entry badge is "To review").
- **Member side: sources in Mira's answers.** Two places, one scripted answer (SIMULATED, written out, not computed from the entries): a message matching `/expens|dinner/i` (demo: "Can I expense a dinner with a client from Acme?") gets a text answer (no insight card, no Pin) with one source chip per knowledge entry used (Home source badge look; "· <team>" after the title for team entries, nothing for Company entries; chips are only for knowledge entries). It works in every workspace, Personal included, because Mira uses the Company entries plus those of every team the person is in. (1) **Side-panel chat** (floating Ask Mira button): a chip SWITCHES the panel from the chat to the entry, no modal and no second panel. The header is a back arrow with "Back to chat" on the left and close on the right; Back returns to the conversation with its scroll position kept; the title is the first line of the body. Source chips stay when suggestion chips go away. (2) **Full-screen chat** (Ask Mira tab, `cvAsk`): a chip opens the same entry in the right side panel, titled in the panel header, and shows as selected (purple border, the `.adm-row.is-active` look). The entry is a read-only PREVIEW with no actions at all (no buttons, no composer): the same property rows as the entry page with plain text instead of dropdowns (Applies to: "Everyone at Tonik" or "Sales team"; Managed by: "Admins" or "Head of Sales"; Source: file chips), then the body. It eases in when it opens (`.rt-view--enter`, the Routines animation: an 8px rise and fade of about 0.4s, off under reduced motion). Seed data: "Expenses and client entertainment" is live (no review suggestion, $80 per person) and applies to Everyone at Tonik, so it is managed by Admins, and the answer matches it; the To review count is two entries. The old canned conversation "Client dinner expenses" was removed. Not in this flow: Slack, Telegram and WhatsApp, a browsable list for members.

**Data.** `KN_ENTRIES` (id, title, type, scope (shown as Applies to), body paragraphs, sources, author or chat, editedBy, editedAt, status `live` / `review`, suggestion `null` or `{ kind:'new'|'change', from, file, removed, added }`). 12 placeholder entries: Company 5, Sales 4, Finance 1, Operations 1, Customer projects 1. Five are waiting for review (since 2026-11-17): Leave and time off, Expenses and client entertainment and Working hours and remote work (changes, Company), Discount rules (change, Sales) and Kick-off checklist (new, Customer projects, drafted from a project page in Notion). An entry may also carry `reports` (`[{ by, text, when }]`); Expenses and client entertainment has one. The `removed` text of a change is a piece of text that really sits in the body, which always holds the live text; approving swaps `removed` for `added`.

**Simulated or unfinished.**
- All entries are placeholder data in memory (a reload resets everything). Nothing reaches Mira: the chat answer is written out, not computed from the entries, and it uses the live text even while a change is in review. Uploaded files are not read or sent (only the name is used, and the draft is canned), and nobody is notified when something is approved or reported (a toast only).
- The prototype user is an Admin everywhere, so there is no head or member sign-in: the head view is Team settings, Team knowledge in Sales, and the member view is the chat panel. The read-only entry page is in the code but unreachable.
- The Status column only shows the "To review" badge, because live entries have no badge.
- Not built, by decision: version history and undo, conflict detection between levels, review-by-date, "gaps" Mira did not find, a browsable list for members, editing the personal layer, real uploads, group chats.

## Clear filters button (2026-11-16)

Every page with working filters has a ghost button "x Clear filters" right after the filter pills (`clearFiltersHTML`, the existing `home-btn--ghost` with a 12px x). It appears only while at least one filter is set, and it resets the filters only: the search text and the Insights sort stay. Pages: People (People tab; "set" means different from the default Status of Active + Invited), Company knowledge, Routines (Show, Status, Connections), the routine library (Category) and Insights (Collection only; Sort is an order, not a filter). Not covered: Tasks, whose pills are visual only in the prototype, and Connections, which has no filters. Insights now redraws the whole page when the Collection changes (it only redrew the results before), so the button can appear.

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

- **Entry.** A "Team settings" row at the top of the workspace switcher menu (under the team name, with a divider), only while a team workspace is active and only for that team's head and admins. Members of the team and the Personal workspace never get it. It opens a mode like the Admin Panel (`MODES.team`): the sidebar is replaced by Back to app, the title "Sales settings" and three tabs, Connections, Members and Team knowledge (2026-11-16, see section 10d); Back to app returns to the page you were on.
- **Header.** The sidebar label says "Sales settings". The page title and subtitle follow the tab: Connections ("The company’s connections, and which of them Sales uses.") and Members ("Who’s in Sales. Contact Admin to manage people."). The wording is placeholder. The two tabs are the sidebar items, so the page has no tab row of its own.
- **Connections tab.** Table Connection, Mira’s autonomy level, Used by Sales, with "The three levels" legend in a grey box beside it (the same aside as the Admin Panel's Connections, added on request; the table column is a fixed 820px, the same on Admin Panel, Team settings and personal Connections (`.adm-col--conn`), and only shrinks on a narrower page; it drops under the table on a narrow page). The list is every connection in the company pool (`ADM_TOOLS`), so a connection an admin adds is ON for every team by default. The level is read-only, a grey tag showing the team's own level (what the team chose, or the admin's level while it follows; Custom if the admin's level is Custom and the team changed nothing). There is no second line in the cell; the tag's tooltip says "Max Operator" (admin's level), or "Max: custom settings set by admins" for Custom. When the connection is off for the team, the level cell shows an en dash instead of the tag. There is no "Ask for more" link any more (removed on request): asking for more permissions goes through the banner below the table. The modal for it is still in the code, unused. Turning a connection OFF asks first ("Stop using HubSpot in Sales?", Cancel / Turn off, the red confirmation used for plugins); turning it ON has no modal.
- **Banner.** A purple banner (the existing `.dc-banner`) under the table: "Missing a connection or need more permissions?" and "If a connection is missing, or you want to extend Mira’s permissions, you can request it from an admin.", with a white "Request with Mira" button that opens the Mira side panel looking at the page. SIMULATED: Mira's reply is the generic canned one and nothing is sent to an admin. The wording is placeholder.
- **Connection drawer.** A row shows the hover "Open" button like People and opens the right side panel (the switch and "Ask for more" do not open it). It is the Admin Panel's connection drawer cut down to what the team may change, with the company's setting as the ceiling. Max box (2026-11-16, option B in the wireframes, only its top part): above the level cards a grey box (same grey and radius as "The three levels") and no title (the "Max for Sales" label was removed on request), only the sentence "[Level] is set by admins. Greyed options are above it." plus an "Ask for more" link (the existing `home-link`, set to the size of the sentence before it; it opens the Mira side panel like "Request with Mira", SIMULATED, nothing is sent). At Operator the text is "Operator is set by admins. This is the highest level." with no link. Level cards: all three are always shown, the ones above the ceiling disabled and dimmed (Operator max: none disabled; Assistant: Operator; Observer: Assistant and Operator); with a Custom max there are still no cards, only the box and the permissions. Each permission offers its ceiling value and the ones below it (Allowed, then Ask, then Blocked), so a permission admins keep on Ask still shows Allowed, disabled, with the hover note "Admins set the max to Ask." (2026-11-16, on request: all three values are always shown). The drawer has no subtitle any more (it read "For Sales. Max Operator."); the section title "Levels allowed for Sales" (2026-11-06) sits above the level cards instead, and is left out when there are no cards (a Custom max: no level fits). Only when the team has lowered the level (it has its own stored choice), a "Reset to max level" link (the existing `home-link`) sits under the level cards, or above the permissions when admins' Custom level offers no cards; it puts the team back to following the admin. Inheritance (agreed 5 Nov): a team starts at the admin's level and FOLLOWS it, up or down, until it changes something; opening the drawer changes nothing. Once the team lowers a level it keeps its own choice. If the admin goes lower than that choice, the team is pulled down and the lower value is STORED as its choice: if the admin raises the level again the team does not change (it stays on its own choice, and does not go back to following). A choice the team itself sets equal to the admin's level is dropped and the team follows again. A connection admins set to Custom offers no level cards (no standard level is guaranteed to fit under it), only the permissions. No Remove button. SIMULATED: the team's choice is kept in memory per team and connection and changes nothing else on the page.
- **Members tab.** Read-only list, Person and Role in team (Head first, then Member), from the same data as People. Nothing sits under the list: the "Contact Admin to manage people." line is in the subtitle (the link into People for admins and the head's "ask an admin" line were removed on request; the link code is left unused). No connection status per person.
- **Not on this page:** team structure, head and adding people (People), permission levels (Connections & channels), apps and skills (Mira), routine approvals (Routines).

Differences from the brief, to confirm:

- **Level names.** The brief names the levels Draft, Read only and Act inside the company. The Admin Panel uses Observer, Assistant and Operator (section 9), so the page shows those, read live from the same data (`ADM_TOOLS.access`), and the rows are the Admin Panel's nine connections, not the six of the brief. They map one to one (Observer = Read only, Assistant = Draft, Operator = Act inside the company). If the plain names are meant to replace the Admin Panel ones, both screens should change together.
- **No kind under the name.** The brief writes "Gmail · Email"; the table shows the name and logo only, as the Admin Panel Connections table does.
- **Data.** The Sales team now has Maya Klein as its head (it had none), so the Members tab matches the brief; Priya Shah stays in Operations, so she is not in Sales' list.
- **Turn off is red.** The confirmation uses the existing red confirm button (`danger`), as when turning an app off. The brief's "to confirm with client" is still open.

Simulated or unfinished:

- Everything lives in memory (a reload resets it). Turning a connection off stops nothing, and the request is not sent to anyone (in the application it reaches admins as a notification and an email, not in the Admin Panel, neither of which exists here); no toast is shown, the link changing is the confirmation.
- **Permission requests in the bell** (2026-11-06, on request). The bell holds a sample request, "Maya asks for more in Gmail" (badge Sales, a line "Let Mira send emails from Gmail for Sales.", a Review button). Clicking the item or Review closes the panel and opens the existing confirmation dialog (`rtConfirm`, now with an optional left-button label): title "Maya asks for more in Gmail", text "Let Mira send emails from Gmail for Sales. This changes it for all of [Company].", Decline and Approve (Escape or a click outside only dismisses, the request stays). Approve sets that permission (Gmail, "Sending emails inside the company") to Allowed for the whole company, in the connection's own permissions, so its level is recalculated (Gmail stays Custom); Decline changes nothing. Either answer removes the request from the bell and shows a toast ("Approved. Maya was told" or "Declined. Maya was told"). SIMULATED: Maya is not a user in the prototype, so "she is told" is only the toast; Approve does not send the team notifications that a level change in the Admin Panel does. The old Ask for more modal (`tsAskModal`) and its helpers were deleted; sending a request from Team settings is still not built.
- The prototype user is an Admin in every workspace, so the head-only variant of the entry (workspace role 'Head') is in the code but cannot be reached.
- Not built: the Operations workspace (named "Ops team" until 2026-11-06; its member count, like Sales', is read from People, `admTeamMembers`) is a disabled mock, so Team settings can only be seen for Sales.

## 10e. Settings (personal) (2026-10-30)

Built from the "Settings (personal)" brief. Each person's own settings: everyone has them, admins included, the same in every workspace. It opens like the Admin Panel (`MODES.settings`): the sidebar is replaced by Back to app, five tabs (Account, Connections, Channels, Mira, Notifications) and, pinned to the bottom, a "Contact Binome" link. It replaces the old three-tab sketch (Mira, My connections, Account); the old `renderMiraSettings` (model choice, system prompt, memory files) is left in the code, unused, because model and system prompt now belong to the Admin Panel. Everything is reused from the Admin Panel (page header, tables, toggle, dropdown pill, tags, confirmation modal, channel cards, the instructions box). The only new CSS is the small `.set-*` layout glue and a disabled state for dropdown items.

- **Connections.** (named Accounts in the brief, renamed on request; the code ids still say `accounts`) Subtitle "The connections your teams use. Connect your own account to work with Mira in each." Table Connection, Status, Action. The "Used in" and "Mira on your account" columns and the helper line under the table were removed on request. The list is every connection switched on for any of the person's teams (Team settings' off switches count), read live from `ADM_TOOLS`, so it is the Admin Panel's connections, not the five of the brief. The prototype person is in Sales (as head) and Operations (as a member), the same teams as the workspace switcher (`SET_TEAMS`, changed from Customer projects 2026-11-06). Status is Connected (green), Stopped syncing (red, action Reconnect) or Not connected (grey, action Connect). Disconnect asks first (Cancel, Disconnect, red). The empty state ("Your teams don’t use any connections yet. Ask your team head or an admin.") shows when the person is in no team that uses a connection.
- **Which connections are listed, level column, max (2026-11-05, on request).** A connection is listed when at least one of the person's teams uses it (the "Used by" switch in Team settings is on); the old `teams` field on connections is not used here any more. With no team, every company connection is listed and the max is the admin's level. Beside the table sits the same "The three levels" legend as in the Admin Panel and Team settings. The table has a "Mira’s autonomy level" column (same grey badge as Team settings) showing the person's actual level, with the tooltip "Max Operator" ("Max: custom settings" for Custom); a connection that is not connected shows an en dash (Stopped syncing still shows the level). The max is the highest of the teams' levels, permission by permission, never above the admin's; a team that turned the connection off does not count. The drawer has the section title "Levels allowed for you" above the level cards (left out when there are no cards, a Custom max) and no subtitle (2026-11-06; the old line naming the max and its source, e.g. "Max Operator, set by admins.", was removed on request, `setMaxSource` is orphaned in the code). "Reset to max level" (the existing `home-link`) sits under the level cards, or above the permissions when there are no cards, only once the person has lowered the level; it puts them back to following the max.
- **Rule: when someone is in two teams, which level wins (2026-11-06, a product rule from the brief; the prototype's screens already behave this way, so nothing in the design changed).** The highest level wins for personal work only: chat with Mira and the person's own routines, because connections are personal accounts. That is the "max" above. Team routines run at their own team's level: a Finance routine never goes above Finance's level, even when someone with Operator from Sales runs it. The prototype only shows the personal side (the max and the level column in Settings → Connections); it does not run routines, so the team side is not simulated. The application has to apply it when a routine runs: use the routine's team level, not the runner's max.
- **Connection drawer.** Same pattern as Team settings (section 10d): hovering a row shows the "Open" button, and the row opens the right side panel "What Mira can do in [Connection]", with "On your account, up to what admins allow." underneath. It is the Team settings drawer, one step lower: the ceiling is the company's level and permissions, lowered further by whatever the person's teams chose in Team settings (with several teams the highest permissions win, permission by permission; a team that follows the admin counts as the admin's level), and the person can only pick the same or less, never more. The person follows that max until they change something, with the same rules as a team (opening the drawer changes nothing; when the max goes below the person's choice the lower value is stored and stays if the max goes up again; a choice the person sets equal to the max is dropped). The same max box sits on top ("[Level] is set by admins", or "is set by your team" when a team of the person lowered it) and the level cards above the max are shown disabled. Each permission shows all three values, the ones above the ceiling disabled with a hover note ("Admins set the max to Ask.", or "Your team set the max to Ask." when a team of the person lowered it) (2026-11-16, on request). A Custom company level offers no level cards, only the permissions. A connection that is not connected has no drawer (no Open button, the row is not clickable); Stopped syncing still opens it, and disconnecting closes the drawer. The Connect, Reconnect and Disconnect buttons do not open the drawer. SIMULATED: the choice is kept in memory (`SET.perms`) and changes nothing else.
- **Channels.** Mira app first ("Always on", no switch or button, same card as the admin's), then Slack (installed once by admins, so only "Open in Slack"; "Not set up for Tonik. Ask an admin." when admins uninstalled it), WhatsApp and Telegram (Connect or Disconnect). The "When something needs you, Mira messages you on" setting from the brief was removed on request.
- **Mira.** The memory card (rows with a source line and Forget, an add field; the title "What Mira remembers about you" and the count were removed on request) and the instructions card (placeholder, helper "Added to every conversation with you, after Tonik’s instructions.", Save enabled only when the text changed). No model choice, no brief settings.
- **Account and Notifications.** Account (Profile and Security merged, on request) has a Profile block (name and email as read-only text, each with a Change button that only shows a toast) and a Security block: change password, two-factor (with "Lost your device? Ask an admin to reset it.") and sign out everywhere. Appearance and profile variables were removed. Notifications has three switches. The brief says "move as is", but these pages do not exist in the prototype, so their contents are PLACEHOLDER.

Differences from the brief, to confirm:

- **Level names.** The brief writes Draft, Read only and Act inside the company. The drawer shows the Admin Panel's Observer, Assistant and Operator, as Team settings does (section 10d).
- **Rows.** Nine connections instead of five (see above).
- **WhatsApp and Telegram.** If admins switch either off in Connections & channels (each is allowed on its own), the cards say "Not allowed in Tonik. Ask an admin." and offer no button. The brief does not cover this.
- **No Disconnect confirmation for WhatsApp and Telegram**, and unsaved instruction changes are not guarded when leaving the page.

Simulated or unfinished:

- Everything lives in memory (a reload resets it). Connect and Reconnect do not open the OAuth flow, they only flip the status and show a toast. Disconnect stops no routine. Open in Slack, Change password, Sign out and Contact Binome only show a toast or a confirmation; WhatsApp and Telegram connect without a sign-in.
- The memory rows and their sources are sample data, a new one gets "From Settings · today", and nothing reaches Mira.
- Removed from Settings on purpose, as in the brief: My department (replaced by Connections), Support access (Governance) and the Mira model and system prompt (Admin Panel).

## 11. Prototype and application handover

See [HANDOVER.md](HANDOVER.md) for how to explain prototype changes and apply them to your current application.
