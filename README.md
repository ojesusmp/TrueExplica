# explica

> Instant visual project status — one HTML file, no setup, works offline.

[![npm version](https://img.shields.io/npm/v/@ojesusmp/explica.svg)](https://www.npmjs.com/package/@ojesusmp/explica)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)

**explica** is a Claude Code skill that reads your current project state and produces a self-contained, graphical HTML status page you can open in any browser, email, archive, or hand to a non-technical stakeholder. Drop it in a conversation after a coding session and get a shareable snapshot of what is done, what is in progress, who is waiting on what, and what comes next — all formatted with readable visual elements like progress bars, status pills, timelines, and decision matrices.

The output is intentionally simple and portable: one HTML file, all CSS inline, all icons inline as SVG, no JavaScript, no external resources, no network calls. It works on a plane, on a USB stick, or printed on paper.

---

## Table of contents

- [Why explica](#why-explica)
- [Install](#install)
- [Trigger words](#trigger-words)
- [Usage examples](#usage-examples)
- [Output anatomy](#output-anatomy)
- [How explica avoids fabricating state](#how-explica-avoids-fabricating-state)
- [Customization](#customization)
- [Limitations](#limitations)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [Security](#security)
- [Credits](#credits)
- [License](#license)

---

## Why explica

Status updates take time. Slides take longer. By the time you've made one, the project has moved.

explica generates the status page from the conversation Claude already has with you about the project — so the cost of an updated status report is one keystroke. Because the output is a single HTML file with no dependencies, you can:

- Email it as an attachment without worrying about broken links.
- Open it on any phone, tablet, or laptop without installing anything.
- Print it for a stakeholder who prefers paper.
- Archive it in a folder and re-open it in 2030 without anything breaking.
- Drop it into a wiki, a Notion page, or a static site without modification.

The page is readable at an 18-year-old reading level with a built-in glossary, so non-technical readers do not need to be fluent in your project's vocabulary.

---

## Install

Pick one. All three install the skill into your Claude Code skills directory automatically — no file copying, no path tweaking.

### 1. npm (recommended)

```bash
npm install -g @ojesusmp/explica
```

Update later with the same command.

### 2. Git (latest unreleased code)

```bash
npm install -g github:ojesusmp/TrueExplica
```

Same postinstall, but pulls straight from the repo's `main` branch.

### 3. Claude Code plugin marketplace

Inside Claude Code:

```
/plugin marketplace add ojesusmp/TrueExplica
/plugin install explica@trueexplica
```

### Verify

In a fresh Claude Code conversation, type `explica`. The skill should generate an HTML status file in the current directory or ask one short question about anything missing.

---

## Trigger words

These phrases tell Claude to run explica automatically:

- `explica`
- `/explica`
- `visual project status`
- `document where we are`
- `explain where we are`

These phrases make Claude offer to run explica (it will ask first, not run automatically):

- `explain this project`
- `graphical guide`

The skill explicitly does **not** auto-fire on:

- `explain this function`
- `explain this code`
- `explain this regex`

This precision is deliberate — explica is for project-level state, not code-level explanation.

---

## Usage examples

### Bare trigger after a coding session

```
explica
```

Claude reads the current project state on its own — open files, recent changes, conversation context, and optional state files like `MEMORY.md`, `CLAUDE.md`, or `README.md` at the project root — then generates the status page without extra input from you. If anything critical is missing (no clear topic, no overview), Claude asks one consolidated question before generating.

### Explicit topic

```
explica "Q2 launch plan"
```

Focuses the status page on a named topic. Useful when you want to share a specific initiative rather than a general snapshot.

### Custom output directory

```
explica "API redesign" --out ./docs/status
```

Writes the generated HTML file to `./docs/status/` instead of the default output location. Good for keeping status pages alongside project documentation.

### Custom audience level

```
explica "infra migration" --audience "engineering managers"
```

Adjusts the reading level and glossary detail for the named audience. Defaults to "18-year-old with basic technical knowledge" if you do not specify.

---

## Output anatomy

The generated HTML page has ten sections, each using a distinct visual element to make scanning fast.

| # | Section | Visual element |
|---|---------|----------------|
| 1 | Overview | Intro paragraph naming the project, phase, and main goal. |
| 2 | Where We Are | Short status paragraph describing the current moment. |
| 3 | What's Done | List of completed items as green pills with checkmark icons. |
| 4 | In Progress | Horizontal progress bars with estimated completion percentages. |
| 5 | Waiting On | Yellow pills with clock icons for blocked items. |
| 6 | Decisions Made | Cards showing finalized decisions and short rationale. |
| 7 | Decisions Pending | Matrix table with options as rows, pros and cons as columns. |
| 8 | Timeline | Vertical timeline with colored status dots per milestone. |
| 9 | Next Steps | Ordered list of action items with owner and target date. |
| 10 | Glossary | Term and definition pairs for project-specific words. |

Items that Claude inferred rather than confirmed from explicit sources are marked with an amber `.pill--inferred` badge so readers know which details may need verification.

The output filename follows this pattern:

```
guide_<topic-slug>_<YYYYMMDD-HHMMSS>.html
```

For example: `guide_q2-launch-plan_20260510-143215.html`.

---

## How explica avoids fabricating state

A risk with auto-generated status documents is that they look authoritative even when the underlying details were guessed. explica uses a two-tier schema to keep readers honest about what was confirmed versus inferred.

**Confirmed items** require a `source` field — either a conversation reference (timestamp), a file reference (`src/auth.ts:42`), or an explicit user statement. These render with normal styling.

**Inferred items** are populated from context but lack a verifiable source. These render with a visible amber `.pill--inferred` badge labeled "unconfirmed" so readers know the item is a best guess and should be verified before relying on it.

If Claude cannot find or infer a value for a required field, it asks you a single consolidated question instead of silently inventing one.

---

## Customization

Open `templates/base.html` in any text editor to change how the output looks or add new sections.

### Change colors

The template uses CSS custom properties at the top of the `<style>` block. Edit these variables to restyle the whole page in one place:

```css
:root {
  --bg: #ffffff;
  --fg: #1a1a1a;
  --accent: #4f46e5;
  --success: #16a34a;
  --warning: #d97706;
  --danger: #dc2626;
}
```

Dark mode rules live under a `@media (prefers-color-scheme: dark)` block — edit those to retheme the dark variant.

### Change fonts

The template ships with a system font stack so no fonts are loaded over the network:

```css
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
```

You can swap to any locally installed font by editing that one line.

### Add a new section

1. Add a `<section id="my-section">` block in `templates/base.html` where you want it to appear.
2. Add a matching field to the schema documented in `SKILL.md` so Claude knows to populate it.
3. Trigger explica — the new section will appear in all future outputs.

### Tweak the print layout

Print rules live under a `@media print` block. By default, navigation is hidden and the page forces a paper-friendly contrast.

---

## Limitations

These are intentional design choices made to keep the output portable and self-contained:

- No JavaScript interactivity — the page is static HTML and CSS only, so it works offline and can be emailed or archived without breaking.
- No charts beyond progress bars and donut-style indicators — complex chart libraries require network calls or large dependencies.
- No embedded raster images — the output stays small and text-searchable.
- No network calls at render time — nothing is loaded from a CDN or external server when you open the page.
- Single canonical template per release — variants like "condensed" or "decision-only" are planned for a future version.

---

## Troubleshooting

### `explica` does not fire after I type it

- Make sure the skill is in your Claude Code skills directory: `~/.claude/skills/explica/SKILL.md` should exist.
- Restart Claude Code, or refresh the skill registry using whatever skill manager you use.
- Check that no other installed skill claims the same trigger phrase. If there is a collision, the more specific phrase usually wins.

### The output HTML is missing sections

- explica hides empty sections by default to keep the page readable.
- If a section you expected is missing, the underlying schema field is probably empty — either supply the data explicitly in the conversation or run with an explicit topic.

### The output looks wrong in dark mode

- Open the file in any modern browser and toggle your OS theme. The page should switch styling automatically via `@media (prefers-color-scheme: dark)`.
- If a browser does not honor the OS preference, the page falls back to the light theme.

### `npm install -g @ojesusmp/explica` fails

- Make sure your Node version is `>=18`.
- In a CI environment, set `EXPLICA_SKIP_POSTINSTALL=1` to bypass the postinstall copy step.

---

## Contributing

Pull requests and issues are welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md) for the contribution workflow, coding conventions, and how to propose a new template variant.

---

## Security

If you discover a security issue (a way to make the generated HTML execute arbitrary code, leak data, or reach an external network), please follow the disclosure process in [SECURITY.md](./SECURITY.md). Do not open a public issue for security reports.

---

## Credits

Built and maintained by **Orlando Molina** under **TruePointAgents**.

Designed for the Claude Code skill system. Inspired by the principle that good status reporting should cost nothing in time and travel anywhere without breaking.

---

## License

Released under the [MIT License](./LICENSE).
