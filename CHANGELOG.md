# Changelog

All notable changes to the `explica` skill are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
