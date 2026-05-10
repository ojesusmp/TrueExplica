# explica — instant visual project status, no setup required

**explica** is a Claude Code skill that reads your current project state and produces a self-contained HTML status page you can open in any browser. Drop it in a conversation after a coding session and get a shareable snapshot of what is done, what is in progress, who is waiting on what, and what comes next — all formatted with readable visual elements like progress bars, status pills, timelines, and decision matrices.

---

## Install

### Option 1: npm (recommended)

```bash
npm install -g @ojesusmp/explica
```

The postinstall script copies `SKILL.md`, `templates/`, `README.md`, `LICENSE`, and `CHANGELOG.md` into `~/.claude/skills/explica/` (or `%USERPROFILE%\.claude\skills\explica\` on Windows). To update, run the same command again.

To skip the auto-copy (CI builds, sandboxes), set `EXPLICA_SKIP_POSTINSTALL=1` before installing.

### Option 2: Manual copy from this repo

**Windows**

Copy the `Explica` folder into your Claude skills directory:

```
%USERPROFILE%\.claude\skills\explica\
```

So the final path looks like:

```
C:\Users\YourName\.claude\skills\explica\SKILL.md
```

**macOS / Linux**

```bash
cp -r Explica ~/.claude/skills/explica/
```

After copying, either restart Claude Code or run the skill refresh command:

```
/oh-my-claudecode:skill
```

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

---

## Usage examples

**Bare trigger after a coding session**

```
explica
```

Claude reads the current project state on its own — open files, recent changes, task context — and generates the status page without any extra input from you.

**Explicit topic**

```
explica "Q2 launch plan"
```

Focuses the status page on a named topic. Useful when you want to share a specific initiative rather than a general snapshot.

**Custom output directory**

```
explica "API redesign" --out ./docs/status
```

Writes the generated HTML file to `./docs/status/` instead of the default output location. Good for keeping status pages alongside project documentation.

---

## Output anatomy

The generated HTML page has ten sections, each using a distinct visual element to make scanning fast.

**Overview** — An intro paragraph that names the project, the current phase, and the main goal. Plain text.

**Where We Are** — A short status paragraph describing the current moment in the project. Plain text.

**What's Done** — A list of completed items shown as status pills with checkmark icons. Each pill is green and labeled with the item name.

**In Progress** — Active work items shown as horizontal progress bars. Each bar shows an estimated completion percentage.

**Waiting On** — Blocked items shown as waiting pills with clock icons. These are things the team cannot move on until something else happens.

**Decisions Made** — Cards that show a decision that was finalized, plus a short rationale explaining why that choice was made.

**Decisions Pending** — A decision matrix table. Each row is an option being considered, and the columns show the pros and cons side by side so you can compare at a glance.

**Timeline** — A vertical timeline with status dots. Each dot is colored to show whether a milestone is complete, in progress, or upcoming.

**Next Steps** — An ordered list of action items. Each item includes who owns it and when it should happen.

**Glossary** — Term and definition pairs for any project-specific words or abbreviations used in the document.

Items that Claude inferred rather than confirmed from explicit sources are marked with an amber `.pill--inferred` badge so you know which details may need verification.

---

## Customization

Open `templates/base.html` in any text editor to change how the output looks or add new sections.

**Change colors**

The template uses CSS custom properties at the top of the `<style>` block. Edit these variables to restyle the whole page:

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

**Add a new section**

1. Add a `<section id="my-section">` block in `templates/base.html` where you want it to appear.
2. Add a matching field to the schema so Claude knows to populate it.
3. Trigger explica — the new section will appear in all future outputs.

---

## Limitations

These are intentional design choices made to keep the output portable and self-contained:

- No JavaScript interactivity — the page is static HTML and CSS only, so it works offline and can be emailed or archived without breaking.
- No charts beyond progress bars and donut-style indicators — complex chart libraries require network calls or large dependencies.
- No embedded raster images — the output stays small and text-searchable.
- No network calls at render time — nothing is loaded from a CDN or external server when you open the page.

---

## License

MIT
