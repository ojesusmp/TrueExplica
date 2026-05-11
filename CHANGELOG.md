# Changelog

All notable changes to the `explica` skill are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.4] - 2026-05-10

### Added
- Two themed template variants: `templates/base-light.html` (forced light) and `templates/base-dark.html` (forced dark). Each works regardless of the reader's OS color-scheme preference.
- `--theme light|dark` CLI flag in `argument-hint`.
- Skill now asks the user for a theme at invocation when no `--theme` flag is provided. If running non-interactively, falls back to `templates/base.html` which still auto-switches based on the reader's OS.

### Changed
- `SKILL.md` Generation step 1 now resolves the theme choice before reading any template file.
- `SKILL.md` Customization section now lists all three template paths.

### Notes
- `templates/base.html` is kept as the auto-switching fallback. No template files were removed.
- No change to schema, trigger phrases, section IDs, CSS class contract, or SVG symbol IDs.

## [1.0.3] - 2026-05-10

### Changed
- Redesigned `templates/base.html` again with the "Midnight Slate Editorial" palette — dark-first deep slate canvas, white ink, indigo accents. Replaces the prior cream/saffron treatment.
- Default theme is now **dark mode** (slate `#020617` / `#0f172a` / `#1e293b`); light mode is the fallback via `@media (prefers-color-scheme: light)`.
- Status pills: saturated solid fills with white text — done (emerald), in-progress (indigo), waiting (violet), blocked (red). The `.pill--inferred` amber badge keeps dashed border + italic to stay visually distinct.
- Progress bars: indigo-to-violet gradient with subtle glow + shimmer highlight; percentage value in accent color.
- Hero header now sits in a rounded gradient card (navy → midnight) with eyebrow rule and `<dl>` metadata strip.
- TOC: sticky pill nav with backdrop blur over the dark page background.
- Section headers: numbered (`01·`), thick gradient accent rule under each.
- Cards: rounded 16px corners, dark slate fill, hover lift with accent border highlight.
- Matrix table: deep slate header, alternating row backgrounds, accent left border on first column, hover row highlight.
- Timeline: indigo gradient rail with pulsing in-progress dot.
- Glossary: deep slate term column with saffron right rule, lighter cards on right.
- Print mode: full color override → black-on-white for paper output.

### Notes
- No change to schema, trigger phrases, section IDs, CSS class contract, or SVG symbol IDs.
- v1.0.2 (cream "Ink & Ochre" palette) remains on git tag and changelog history; v1.0.3 supersedes the palette choice based on user feedback.

## [1.0.2] - 2026-05-10

### Changed
- Redesigned `templates/base.html` with the "Ink & Ochre Editorial" palette — warm cream surface, deep navy ink, saffron accent. Replaces the prior gray-on-white look with WCAG-AA contrast in both light and dark modes.
- Section headers now carry an auto-numbered `01·` prefix and a thin accent rule.
- Status pills use real saturated semantic fills (green / blue / violet / red) with white text. The `.pill--inferred` amber badge keeps a dashed border + italic for unmistakable "unconfirmed" signal.
- Progress bars: gradient saffron fill with shimmer highlight; percentage value rendered in accent color.
- Timeline: 3px gradient saffron rail with color-coded status dots; in-progress dot pulses.
- Decisions matrix: dark navy header row, zebra rows, accent left border on first column, hover row highlight.
- Glossary: tinted left-column with saffron right rule for the term column.
- Print mode: full color → black-on-white override so paper output stays readable.

### Notes
- No change to schema, trigger phrases, section IDs, CSS class contract, or SVG symbol IDs. Existing skill outputs continue to render correctly.
- File size went from ~36 KB to ~45 KB — still inside the 8–500 KB validation window.

## [1.0.1] - 2026-05-10

### Changed
- Rewrote `README.md` for clarity, depth, and platform-neutral install instructions; removed every local filesystem reference.
- Updated `LICENSE` and `package.json` author attribution to **Orlando Molina** under **TruePointAgents**.
- Added repository-standard documentation: `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`, and GitHub issue + pull-request templates under `.github/`.

### Notes
- No functional change to the skill itself, the schema, the template, the trigger phrases, or the generated HTML.
- Existing installs of `1.0.0` continue to work without changes.

## [1.0.0] - 2026-05-10

### Added
- Initial release of `explica` Claude Code skill.
- Self-contained HTML+CSS guide generator with 10 sections: Overview, Where We Are, What's Done, In Progress, Waiting On, Decisions Made, Decisions Pending, Timeline, Next Steps, Glossary.
- Two-tier schema separating `confirmed_*` items (cite a source) from `inferred_*` items (rendered with an amber unconfirmed pill).
- Single canonical template at `templates/base.html` with inline CSS, inline SVG icon sprite, `prefers-color-scheme` dark mode, and `@media print` paper-friendly rules.
- Hybrid input model: auto-extracts state from conversation plus optional state files (`.omc/state/`, `.journal/`, `MEMORY.md`, `CLAUDE.md`, `README.md`); asks one consolidated question only for missing required fields.
- Five auto-fire trigger phrases: `explica`, `/explica`, `visual project status`, `document where we are`, `explain where we are`.
- Two soft-suggestion phrases that prompt the user instead of auto-firing: `explain this project`, `graphical guide`.
- Validation checklist embedded in `SKILL.md` (file size, section anchors, zero external references, required `<style>` and `<svg>` and media queries, filename format).
- MIT licensed.
- npm install via `npm install -g @ojesusmp/explica` with postinstall copy to the user's Claude Code skills directory.

### Known limitations
- No JavaScript interactivity in generated HTML (intentional for offline portability).
- No charting library beyond plain CSS bars and inline SVG donuts.
- No embedded raster images (intentional — keeps file size small and portable).

### Planned for v1.1
- Optional Node render script at `bin/render.mjs` (~150 LOC, zero deps) for deterministic HTML generation outside an assistant turn.

[1.0.1]: https://github.com/ojesusmp/TrueExplica/releases/tag/v1.0.1
[1.0.0]: https://github.com/ojesusmp/TrueExplica/releases/tag/v1.0.0
