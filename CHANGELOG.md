# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

<!-- scriv-insert-here -->

## [Unreleased]

## [0.4.0] - 2026-05-28

### Breaking Changes

- **Renamed `compliance_level` to `compliance`** — All files with frontmatter must update this top-level key
- **Dropped `compliance.level` field** — The `simple` vs `full` distinction is deferred until there's evidence it's needed
- **Renamed status values**:
  - `compliant` → `pass`
  - `partial` → `pass` with `exceptions` block
  - `exempt` → `pass` with `exceptions.exempt`
  - `unstable` → \`

## [0.3.0] - 2026-04-19

### Added

- Rule catalog and code model. `docs/rules/RULES.md` is now the canonical
  reference for all rule families, subcategory guidance, and the current
  draft inventory.
- Rule file template at `docs/rules/rule-file.template.md` to standardize
  how individual rules are authored.
- Initial draft rules across eight rule families:
  - **Structure (`PS`)** — missing task, missing role, missing output
    contract, buried instructions, overloaded sections.
  - **Clarity (`PC`)** — vague objectives, open-ended filler language,
    hedged imperatives, mixed tone instructions.
  - **Reasoning / consistency (`PR`)** — conflicting constraints, conflicting
    length guidance, redundant priority ordering, inconsistent output
    examples.
  - **Safety / interpolation (`PX`)** — undelimited input slots, mixed
    trusted and untrusted content, risky slot types, weak tool-output
    boundaries.
  - **Security / vulnerability intelligence (`PV`)** — family reserved;
    no concrete rules in this draft yet.
  - **Metadata (`PM`)** — missing metadata declaration, missing tags or
    domain notes, ambiguous model notes.
  - **Formatting / hygiene (`PF`)** — trailing whitespace, mixed line
    endings, inconsistent fences, inconsistent example indentation.
  - **Prose / style (`PP`)** — generic filler phrasing, redundant phrasing,
    weak imperative style, overlong preambles.
- Security policy (`SECURITY.md`) with reporting guidance.
- `CONTRIBUTING.md` with contributor workflow, rule proposal process, and
  documentation style guidance.
- `ROADMAP.md` with tentative high-interest goals and alpha project notice.
- Repository automation: pre-commit hooks, `pyproject.toml`, and
  `changelog.d/` scaffolding for release-time fragment aggregation.

### Changed

- `README.md` is now a concise project entry point. Detailed catalog
  content has moved to `docs/rules/RULES.md`.
- Structure rule names updated for consistency with catalog wording.

## [0.2.0] - 2026-04-18

### Added

- Schema artifacts and frontmatter extension guidance under `docs/schema/`
  and `schema/`.
- `docs/schema/context-inclusion.md` specifying context inclusion behavior,
  `.promptignore`, and precedence rules for machine-ingestible context.
- `docs/schema/security-intake.md` specifying how advisories flow into the
  `PV` family with human review requirements.
- Motivation essay at `docs/motivation.md`.

### Changed

- Standard scope expanded from prompt-only to all governed documents
  under a Document-Driven Design (DDD+) model.

## [0.1.0] - 2026-04-18

> **Note:** `v0.1.0` and `v0.2.0` were tagged retroactively against existing
> commits. No release artifacts were published for these versions. `v0.3.0` is
> the first formally tagged release.

### Added

- Initial draft of the Prompt Rules Standard.

### Added

- Initial draft of the Prompt Rules Standard.
