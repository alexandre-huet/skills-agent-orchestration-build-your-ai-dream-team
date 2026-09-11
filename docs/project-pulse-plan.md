# Project Pulse Implementation Plan

## 1. Summary

Project Pulse is a small, static, client-only dashboard for Mona's team. It helps
contributors quickly see:

- which projects are active
- who owns each project
- each project's current status
- recent activity
- priority/risk level
- a short, contributor-friendly summary

The dashboard is a static app with three files under `app/`:

- `app/index.html` — page structure, dashboard shell, and project card markup
  rendered from data
- `app/styles.css` — visual design: `.dashboard` layout, `.project-card`
  components, status badges, responsive/readable spacing
- `app/project-data.json` — a top-level `projects` array; each project object
  includes `name`, `owner`, `status`, `recentActivity`, and `priority`

A `.vscode/launch.json` file provides a **Run Project Pulse Dashboard** launch
configuration that serves `app/` (e.g. `python3 -m http.server 5500` with `cwd`
set to `${workspaceFolder}/app`) and opens `index.html` directly — never a bare
directory listing.

This repository has no existing `app/` implementation files yet (the `app/`
directory is currently empty), so this is a from-scratch build. Work is split
between the **Designer** (Gemini 3.1 Pro — UI/UX, accessibility, visual design)
and the **Coder** (GPT-5.5 — implementation logic, data wiring, and
`.vscode/launch.json`), coordinated by the **Orchestrator**, following the
patterns already defined in `.github/agents/` and `docs/agent-team.md`.

## 2. Ordered Implementation Steps

### Step 1 — Define the data contract (`app/project-data.json`)
Establish the data shape first so both HTML structure and CSS class hooks can
be designed against real, representative sample data (multiple projects,
varied `status` and `priority` values, e.g. `"active"`, `"at risk"`,
`"blocked"`, and `"high"`, `"medium"`, `"low"`).

- **Owner:** Coder
- **File:** `app/project-data.json`
- Must use a top-level `projects` array/key.
- Each project object must include: `name`, `owner`, `status`,
  `recentActivity`, `priority`.
- Include at least 4–6 sample projects so the Designer/Coder can validate
  layout with realistic variety (short and long names, long `recentActivity`
  text, all `status`/`priority` values represented).

### Step 2 — Design the information architecture and visual system
In parallel with Step 1, the Designer defines the dashboard's structure and
visual language: card layout, status badge treatment, priority indicators,
spacing, typography, color system, and accessibility approach (contrast,
semantic HTML, ARIA where needed). This is design direction, not final files
yet — it informs Steps 3 and 4.

- **Owner:** Designer
- **Output:** Design direction consumed directly while authoring
  `app/index.html` and `app/styles.css` (no separate design-spec file is
  required by the brief; the Designer may co-author these two files directly).

### Step 3 — Build the HTML structure (`app/index.html`)
Create the dashboard shell and markup that will render project cards from
`app/project-data.json`.

- **Owner:** Designer (structure/semantics/accessibility) with Coder
  (data-loading logic) — see file-ownership note below.
- **File:** `app/index.html`
- Requirements:
  - Exact page title text "Project Pulse".
  - `<link>` reference to `styles.css`.
  - Script/reference that loads `project-data.json` and renders cards.
  - A `.dashboard` container element.
  - Repeated card elements using the class `project-card`.
  - Each rendered card must visibly show `status`, `recentActivity`, and
    `priority` (plus `name`/`owner`).
  - Semantic HTML (headings, landmarks) and accessible markup (alt text if
    icons are used, sufficient labeling for status/priority badges).

### Step 4 — Build the visual design (`app/styles.css`)
Style the dashboard shell and cards to be polished, not a bare page.

- **Owner:** Designer
- **File:** `app/styles.css`
- Requirements:
  - `.dashboard` selector (layout/grid, spacing).
  - `.project-card` selector with `border-radius` and `box-shadow`.
  - Status badge styling with clear visual distinction per status/priority.
  - Responsive layout (cards reflow on narrow viewports; no horizontal
    scrolling at common breakpoints, e.g. 375px, 768px, 1280px).
  - Readable typography and color contrast meeting WCAG AA where practical.

### Step 5 — Wire data loading logic
Implement the JavaScript (inline in `index.html` or a small script block) that
fetches/reads `app/project-data.json`'s `projects` array and renders one
`.project-card` per project into the `.dashboard` container.

- **Owner:** Coder
- **File:** `app/index.html` (script portion)
- Must handle: empty array, missing optional fields, and JSON parse errors
  gracefully (see Edge Cases).

### Step 6 — Create the run configuration (`.vscode/launch.json`)
Add a VS Code launch configuration so the dashboard can be previewed with one
click.

- **Owner:** Coder
- **File:** `.vscode/launch.json`
- Requirements:
  - Strict JSON, no comments.
  - A configuration named exactly **Run Project Pulse Dashboard**.
  - `cwd` set to `${workspaceFolder}/app`.
  - Command: `python3 -m http.server 5500` (or equivalent) serving from
    `app/`.
  - A `serverReadyAction` (or equivalent) that opens
    `http://localhost:%s/index.html` — never a directory listing.

### Step 7 — Integration pass
Orchestrator (or Coder, if delegated) confirms `index.html`, `styles.css`, and
`project-data.json` work together end to end, and that `launch.json` opens the
dashboard correctly.

- **Owner:** Coder (integration/validation), Designer (visual QA)
- **Files reviewed:** all four target files.

### Step 8 — Validation and handoff
Run through the Validation Expectations checklist below and confirm every
requirement is met before reporting completion. (This step feeds the later
`docs/final-handoff.md` deliverable, which is out of scope for this plan.)

- **Owner:** Orchestrator (coordinates), Coder + Designer (fix any gaps).

## 3. File Assignments

| File | Primary Owner | Notes |
|---|---|---|
| `app/index.html` | Designer (structure/markup/accessibility) + Coder (data-loading script) | Split by concern, not by file copy — see Dependencies/Parallel section for sequencing to avoid edit conflicts. |
| `app/styles.css` | Designer | Full ownership; Coder should not need to touch this file. |
| `app/project-data.json` | Coder | Full ownership; Designer consumes this as sample data reference only, does not edit it. |
| `.vscode/launch.json` | Coder | Full ownership; support/tooling configuration, matches `coder.agent.md` guidance to create launch configs when assigned. |

## 4. Designer Responsibilities

- Own **all visual design** of `app/styles.css`: layout system (`.dashboard`),
  card component (`.project-card`), status badges, priority treatment,
  spacing, typography, color, `border-radius`, `box-shadow`.
- Own the **semantic/accessible structure** of `app/index.html`: page title
  ("Project Pulse"), headings, landmark elements, card markup skeleton, ARIA
  labeling for status/priority indicators, alt text for any icons.
- Ensure **responsive behavior** (cards reflow correctly on mobile/tablet/
  desktop widths) and **accessibility** (color contrast, keyboard
  navigability, screen-reader-friendly status/priority labeling).
- Make the first view **clearly read as a dashboard**, not a bare HTML page —
  per `designer.agent.md` guidance.
- Explicitly avoid editing `app/project-data.json` (data) or
  `.vscode/launch.json` (tooling) to prevent scope overlap with Coder.
- Report design decisions and any tradeoffs (e.g., color choices for status
  vs. priority badges) back to the Orchestrator.

## 5. Coder Responsibilities

- Own `app/project-data.json`: define and populate the `projects` array with
  valid, representative sample data (`name`, `owner`, `status`,
  `recentActivity`, `priority`).
- Implement the **data-loading and rendering logic** inside `app/index.html`
  (fetch/parse JSON, map each project to a `.project-card` element, insert
  into the `.dashboard` container the Designer built).
- Own `.vscode/launch.json` in full: strict JSON, no comments, configuration
  named **Run Project Pulse Dashboard**, serving from `app/` with `cwd` set to
  `${workspaceFolder}/app`, opening `index.html` (not a directory listing).
- Validate that the app runs correctly end-to-end before reporting
  completion (per `coder.agent.md`: "Validate the change before reporting
  completion").
- Do not modify `app/styles.css` or the Designer's structural/accessibility
  markup choices without explicit Orchestrator reassignment.

## 6. Dependencies Between Steps

1. **Step 1 (data shape) must exist, at least as a draft, before Step 3's
   rendering logic (Step 5) can be finalized** — the script needs to know the
   exact field names (`name`, `owner`, `status`, `recentActivity`,
   `priority`).
2. **Step 2 (design direction) should happen before or alongside Step 3/4** —
   HTML structure and CSS class names (`.dashboard`, `.project-card`) need to
   be agreed upon so Designer and Coder don't invent conflicting hooks.
3. **Step 3 (HTML structure/markup) must exist before Step 5 (data-loading
   script)** can be meaningfully wired in, since the script needs DOM
   targets (the `.dashboard` container) to insert cards into.
4. **Step 3 and Step 4 depend on the same class-name contract** (`.dashboard`,
   `.project-card`) — this must be agreed once, up front, and not changed
   independently by either agent afterward.
5. **Step 6 (`launch.json`) has no dependency on the data or CSS contents** —
   it only needs to know the app lives under `app/` and the entry file is
   `index.html`. It can be created any time after the directory layout is
   decided (i.e., as soon as Step 1 or 3 begins).
6. **Step 7 (integration) depends on Steps 3, 4, 5 all being complete** —
   HTML, CSS, and data-loading logic all need to exist for a meaningful
   end-to-end check.
7. **Step 8 (validation/handoff) depends on Step 7** passing.

## 7. Parallel Work Decisions

**Can run in parallel (non-overlapping file scope):**
- `app/project-data.json` (Coder) **in parallel with** early design direction
  work (Designer) for Steps 1 and 2 — no shared file, no data dependency
  beyond the agreed field names, which should be fixed early (see Dependency
  #1) so both sides move on a stable contract.
- `.vscode/launch.json` (Coder) **in parallel with** `app/styles.css`
  (Designer) — completely separate files with zero functional dependency on
  each other. This can happen any time after the `app/` directory convention
  is agreed.

**Must run sequentially (overlapping file/shared contract):**
- `app/index.html` structure (Designer) → data-loading script insertion
  (Coder) must be sequential *within the same file* to avoid merge conflicts:
  Designer produces the markup skeleton and the `.dashboard` container first;
  Coder then adds/edits the script block that renders cards into it. The
  Orchestrator should treat `app/index.html` as **one phase, two sub-tasks in
  sequence**, not two agents editing concurrently.
- Final class-name contract (`.dashboard`, `.project-card`) must be agreed
  before both `app/index.html` (Designer markup) and `app/styles.css`
  (Designer CSS) are finalized, since both derive from the same names — this
  is naturally sequential within Designer's own work, not a conflict risk.
- Step 7 (integration) must wait until Steps 3, 4, 5, and 6 are all complete.

## 8. Validation Expectations

- **File existence:** `app/index.html`, `app/styles.css`,
  `app/project-data.json`, and `.vscode/launch.json` all exist.
- **`app/project-data.json`:**
  - Parses as valid JSON (e.g., `python3 -m json.tool app/project-data.json`).
  - Has a top-level `projects` array.
  - Every project object includes `name`, `owner`, `status`,
    `recentActivity`, and `priority`.
- **`app/index.html`:**
  - Contains the exact title text "Project Pulse".
  - References `styles.css` via a `<link>` tag.
  - References/loads `project-data.json`.
  - Contains a `.dashboard` container and one or more `.project-card`
    elements once rendered.
  - Rendered cards visibly display `status`, `recentActivity`, and
    `priority` values (open in a browser or local server to confirm, not just
    static markup inspection, since cards may render via script).
  - Passes a basic accessibility check (semantic headings present, adequate
    color contrast, no keyboard traps) — spot-check with browser devtools
    accessibility panel or axe/Lighthouse.
  - Responsive check: resize viewport (or use devtools device toolbar) at
    ~375px, ~768px, ~1280px widths; confirm no horizontal scroll and cards
    reflow sensibly.
- **`app/styles.css`:**
  - Contains `.dashboard` selector.
  - Contains `.project-card` selector with `border-radius` and `box-shadow`.
- **`.vscode/launch.json`:**
  - Parses as strict JSON with no comments
    (`python3 -m json.tool .vscode/launch.json`).
  - Contains a configuration named exactly **Run Project Pulse Dashboard**.
  - Serves from the `app/` directory (`cwd` = `${workspaceFolder}/app`).
  - Opens `http://localhost:%s/index.html` — confirmed by launching via
    VS Code **Run and Debug** → **Run Project Pulse Dashboard** and observing
    the dashboard UI (not a directory listing) in the opened browser tab.
  - Stop the preview server after confirming, to avoid leaving a background
    process running.
- **End-to-end data loading check:** with the dashboard running, confirm the
  number of rendered `.project-card` elements matches the number of entries
  in `app/project-data.json`'s `projects` array, and that displayed
  status/priority/recentActivity text matches the source data.

## 9. Edge Cases to Handle

- **Empty `projects` array:** the dashboard should show a friendly empty
  state instead of a blank page or JS error.
- **Missing/undefined fields** on a project object (e.g., no `priority`):
  rendering logic should not throw; show a fallback label (e.g., "Unknown")
  rather than `undefined` in the UI.
- **Unexpected `status`/`priority` values** not in the anticipated set (e.g.,
  a typo or new status the CSS doesn't style): fall back to a default badge
  style rather than unstyled/invisible text.
- **Long text values** (very long `recentActivity` strings or project
  `name`s): CSS must truncate/wrap gracefully without breaking card layout.
- **JSON fetch failures** when opened via `file://` instead of a local server
  (browsers often block `fetch()` of local JSON under the `file://` protocol
  due to CORS): this is why `.vscode/launch.json` serving via
  `python3 -m http.server` is required — document this dependency clearly so
  the Coder doesn't assume `index.html` can simply be double-clicked and
  work.
- **Port conflicts** on 5500 if another process is already using it — the
  Coder should note this as a manual troubleshooting step, not silently fail.
- **Duplicate or missing `.dashboard`/`.project-card` class names** between
  Designer's CSS and Designer/Coder's HTML — must be locked down once, early,
  and treated as a shared contract (see Dependencies).
- **Comments in `launch.json`:** VS Code tolerates JSONC in some files, but
  the brief requires strict JSON with no comments here — Coder must not use
  `//` comments even though editors sometimes suggest them.

## 10. Open Questions

- Should status/priority be rendered as free-text values from the JSON, or
  should the UI map them to a fixed, closed set of styled badge variants
  (e.g., `active`/`at risk`/`blocked` and `high`/`medium`/`low`)? The brief
  doesn't fully constrain this — recommend a small fixed set with a graceful
  fallback style for anything else.
- Is any build tooling, framework, or bundler in scope, or must this remain a
  fully static, dependency-free HTML/CSS/JSON app runnable via
  `python3 -m http.server`? Current brief and `coder.agent.md` guidance
  strongly imply the latter (no framework) — recommend confirming with the
  Orchestrator before introducing any dependency.
- Should `app/index.html` fetch `project-data.json` via `fetch()` (which
  requires the local server from `launch.json`) or inline the data directly
  in a `<script>` tag to also work when opened via `file://`? Fetch is more
  aligned with the brief's emphasis on `.vscode/launch.json` serving the app,
  but it means the dashboard will *not* work if a learner double-clicks
  `index.html` directly instead of using the launch configuration — worth
  flagging explicitly in the handoff notes.
- No accessibility or performance budget (e.g., specific WCAG level, load
  time target) is specified in the brief — recommend defaulting to WCAG AA
  contrast and no external dependencies/network calls beyond the local JSON
  fetch.
