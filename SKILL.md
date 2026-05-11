---
name: explica
description: "Generate a self-contained graphical HTML+CSS guide explaining the current state of any prompt, project, program, or decision-point at an 18-year-old reading level. Output is one HTML file with inline CSS and SVG, no external dependencies, no JavaScript libraries. Sections: Overview, Where We Are, What's Done, In Progress, Waiting On, Decisions Made, Decisions Pending, Timeline, Next Steps, Glossary. Trigger phrases - 'explica', '/explica', 'visual project status', 'document where we are', 'explain where we are'."
license: MIT
argument-hint: "[topic] [--out <dir>] [--audience <level>] [--theme light|dark]"
---

# explica

Generate a self-contained, offline-readable HTML+CSS visual guide that explains the current state of any project, prompt, program, or decision-point — no external dependencies, no JavaScript libraries, no CDNs.

---

## When to invoke

Auto-fire on any of these exact phrases (case-insensitive):

- `explica`
- `/explica`
- `visual project status`
- `document where we are`
- `explain where we are`

**Does NOT fire on:** `explain this function`, `explain this code`, `explain this regex`, `explain this method`, or any phrase where "explain" is followed by a code artifact rather than a project/state concept.

---

## Claude may also suggest invoking when user says...

These phrases are soft-suggestion triggers only. Claude does NOT auto-fire; instead it offers:
> "Want me to run `explica` to generate a visual guide?"

- `"explain this project"`
- `"graphical guide"`

Wait for user confirmation before proceeding.

---

## Output contract

- **Filename pattern:** `guide_<topic-slug>_<YYYYMMDD-HHMMSS>.html`
  - `topic-slug`: lowercase, non-alphanumeric replaced with `-`, consecutive hyphens collapsed, leading/trailing hyphens trimmed.
  - Timestamp: local time at moment of generation.
  - Example: `guide_q2-launch-plan_20260510-143200.html`
- **Single file:** all CSS inline in `<style>`, all graphics as inline SVG, zero `<link>` tags, zero `<script src>` tags, zero CDN references.
- **Theme:** light default via CSS custom properties; `@media (prefers-color-scheme: dark)` overrides; `@media print` resets for paper output.
- **Size:** 8 KB – 500 KB (validated before reporting success).

---

## Input model (hybrid)

Claude auto-extracts context in this order, then asks only for gaps:

1. **Scan conversation turns** for explicit state language: "done", "in progress", "waiting", "decided", "blocked", "next step", "we agreed", "outstanding".
2. **Read state files** if present (skip silently if missing):
   - `.omc/state/mission-state.json`
   - `.omc/state/sessions/*/session-started.json`
   - `.journal/STATE.md`
   - `.journal/timeline.jsonl` (last 50 entries)
   - `MEMORY.md`
   - `CLAUDE.md`
   - `README.md` (CWD)
3. **Populate the schema** (see below) from extracted context.
4. **If any required field is still empty**, ask **one consolidated question** listing all missing fields with sensible defaults pre-filled. Required fields: `topic`, `overview`, `where_we_are`. (`audience_level` defaults to `"18yo basic knowledge"` if not specified.)

---

## Schema

Fields the HTML guide must populate. Two tiers for fabrication-prone arrays:

**Required fields (strings):**
```
topic            (string, required)
audience_level   (string, default "18yo basic knowledge")
overview         (string, 2–4 sentences, required)
where_we_are     (string, current-state paragraph, required)
```

**Two-tier arrays** — `confirmed_*` requires a citable `source`; `inferred_*` renders with `.pill--inferred` (amber "unconfirmed" badge):

```
confirmed_done[]              {title, detail, source}
inferred_done[]               {title, detail}           → renders .pill--inferred

confirmed_in_progress[]       {title, percent, detail, source}
inferred_in_progress[]        {title, percent, detail}  → renders .pill--inferred

confirmed_waiting_on[]        {title, blocker, owner, source}
inferred_waiting_on[]         {title, blocker, owner}   → renders .pill--inferred

confirmed_decisions_made[]    {decision, rationale, date, source}
inferred_decisions_made[]     {decision, rationale, date} → renders .pill--inferred

confirmed_decisions_pending[] {question, options:[{name, pros, cons}], deadline, source}
inferred_decisions_pending[]  {question, options:[{name, pros, cons}], deadline} → renders .pill--inferred
```

**Single-tier arrays** (low fabrication risk — no source required):
```
timeline[]      {date, label, status}
next_steps[]    {step, owner, when}
glossary[]      {term, plain_definition}
```

**Validation invariants:**
- Every `confirmed_*` entry MUST have a non-empty `source` (e.g. `"2026-05-10T14:32"` or `"src/auth.ts:42"`).
- Every `inferred_*` entry rendered in HTML MUST carry the CSS class `.pill--inferred` visible in markup.
- Skill fails validation and does not report success if either invariant is violated.

---

## Generation steps

1. **Resolve theme.** The output ships in one of two visual themes:
   - `light` — warm light surface with blue accents (`templates/base-light.html`)
   - `dark` — deep slate canvas with indigo accents (`templates/base-dark.html`)

   Decision order:
   1. If `--theme light` or `--theme dark` was passed, use it without asking.
   2. Otherwise ask the user a single consolidated question: *"Light or dark theme for this guide?"* with both options shown. Wait for an answer before generating.
   3. If neither theme is available in the conversation and the run is non-interactive, fall back to `templates/base.html` (auto-switches based on the reader's OS preference).

   Read the resolved template file.
2. For each schema field, render the corresponding section block into the template placeholder. Hide any section whose array is empty by setting `style="display:none"` on its `<section>` element.
   - **When rendering `inferred_*` arrays, every item element MUST carry `class="pill--inferred"` (or contain a `span.pill--inferred` sibling) so the unconfirmed-state styling activates.**
3. Slug the topic: lowercase → replace non-alphanumeric with `-` → collapse consecutive hyphens → trim leading/trailing hyphens.
4. Build filename: `guide_<slug>_<YYYYMMDD-HHMMSS>.html` using current local time.
5. Resolve output directory: default is CWD; `--out <dir>` argument overrides. Create directory if it does not exist.
6. Write the completed HTML via the Write tool to the resolved path.
7. Run the validation checklist (see below). If any check fails, attempt one auto-fix (e.g., strip any stray external `<link>` tag) and re-validate. On second failure, report the failing check and ask the user how to proceed.
8. Report to the user: absolute file path, file size in KB, count of non-hidden sections emitted, and the list of validation checks passed.

---

## Validation checklist

Run all checks before claiming success. Check each item explicitly:

| # | Check | Pass condition |
|---|-------|---------------|
| 1 | File exists | Path returned by Write tool resolves to a readable file |
| 2 | File size | Between 8 KB and 500 KB |
| 3 | Section anchors | Contains all 10: `id="overview"`, `id="where-we-are"`, `id="done"`, `id="in-progress"`, `id="waiting-on"`, `id="decisions-made"`, `id="decisions-pending"`, `id="timeline"`, `id="next-steps"`, `id="glossary"` |
| 4 | No external HTTP refs | Zero matches for: `http://`, `https://`, `src="http`, `href="http`, `@import url(http`, `cdn.`, `<script src`, `<link` |
| 5 | Inline style present | Contains `<style>` |
| 6 | Inline SVG present | Contains `<svg` |
| 7 | Dark mode media query | Contains `@media (prefers-color-scheme: dark)` |
| 8 | Print media query | Contains `@media print` |
| 9 | Filename format | Matches regex `^guide_[a-z0-9-]+_\d{8}-\d{6}\.html$` |
| 10 | Inferred pill classes | Every rendered `inferred_*` item contains `pill--inferred` in its HTML |
| 11 | Confirmed sources | Every rendered `confirmed_*` item has a non-empty `source` attribute or data element |

---

## Examples

**Example 1 — plain trigger after a coding session**

User: `explica`

Claude extracts from conversation: auth refactor in progress, tests passing, PR waiting on review.
Claude writes `guide_auth-refactor_20260510-150000.html` (no questions asked — context was sufficient).
Reports: `guide_auth-refactor_20260510-150000.html` | 34 KB | 7 sections | 11/11 checks passed.

---

**Example 2 — explicit topic with output directory**

User: `explica "Q2 launch plan" --out ./docs/status`

Claude reads MEMORY.md and conversation; populates all schema fields.
Writes to `./docs/status/guide_q2-launch-plan_20260510-152233.html`.
Reports path, size, sections.

---

**Example 3 — thin context triggers consolidated question**

User: `explain where we are`

Claude finds no state files and minimal conversation context.
Claude asks one question: "To generate the guide I need a few details — what is the topic? (e.g., 'API redesign') And in 2–4 sentences, what is the current overview? (audience defaults to 18yo reading level; output goes to CWD unless you specify --out)"
User answers → Claude writes the file.

---

## Customization

To change colors, open the relevant template file and edit the CSS custom properties in the `:root` block:
- `templates/base-light.html` — light theme defaults
- `templates/base-dark.html` — dark theme defaults
- `templates/base.html` — auto-switching fallback (uses both palettes)

```css
:root {
  --bg: #ffffff;
  --fg: #111827;
  --accent: #2563eb;
  --pill-inferred-bg: #f59e0b;  /* amber — do not make this too subtle */
  --pill-inferred-fg: #1f1300;
}
```

To add a new section:
1. Add a `<section id="my-section">` block in `templates/base.html` with a `{{MY_SECTION_BLOCK}}` placeholder.
2. Add the corresponding schema field(s) to the `## Schema` section of this file.
3. Add a render step in `## Generation steps` step 2.
4. Add the anchor `id="my-section"` to validation checklist item 3.
