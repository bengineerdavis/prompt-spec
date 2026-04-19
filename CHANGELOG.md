# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

<!-- scriv-insert-here -->

## [Unreleased]

## [0.1.0] - 2026-04-18

### Added

- Initial draft of the Prompt Rules Standard. (`d641404`)

### Added

- `changelog.d/` directory with Scriv fragment template for per-change
  entries to be aggregated at release time. (`bd38228`)
- `ROADMAP.md` with high-interest tentative goals and alpha project notice,
  including finalized `P<FAMILY><AA><BB>` code model and references to
  `docs/rules/RULES.md` and `docs/rules/rule-file.template.md`. (`cd33a14`)
- `CONTRIBUTING.md` with contributor workflow, rule proposal process,
  updated rule identifier format `P<FAMILY><AA><BB>`, link to
  `docs/rules/rule-file.template.md`, and link to `SECURITY.md`. (`c954c7b`)
- `docs/schema/extensions.md` with guidance on how schema extensions should
  work using `x-*` keys without breaking the core model. (`eaa117e`)
- `schema/` directory with initial schema artifacts for validating core
  project documents and machine-readable outputs. (`6634824`)
- `.pre-commit-config.yaml` and `pyproject.toml` for repository-level
  automation, linting, formatting, and validation tooling. (`4c0e34b`)
- `docs/rules/rule-file.template.md` as the standard template for authoring
  individual rule files, covering summary, rationale, detection guidance,
  repair guidance, examples, limitations, and rule-specific references.
  (`e42be6f`)
- `SECURITY.md` root-level security policy documenting how to report
  security issues affecting the repository, tooling, and draft standard,
  with links to `docs/rules/RULES.md` and `docs/schema/security-intake.md`.
  (`15cd0f3`)
- Initial `PX` safety and interpolation rule drafts: `PX001` undelimited
  untrusted input slot, `PX002` mixed instructions and untrusted content,
  `PX003` slot allows instruction-like content, `PX010` weak tool-output
  boundary. (`0df27a4`)
- Initial `PS` structure rule drafts: `PS001` missing explicit task or
  objective, `PS002` missing role or persona section, `PS003` missing output
  contract, `PS010` critical instructions buried, `PS020` overloaded
  all-in-one section. (`58d3d0b`)
- Rule family directory scaffolding under `docs/rules/` with `structure/`,
  `safety/`, and `vulnerabilities/` subcategory paths. (`044c830`)
- `docs/motivation.md` motivation essay linked from `README.md`. (`760ae53`)
- `docs/schema/context-inclusion.md` specifying `.promptignore`,
  `context.include`, default inclusion behavior, and precedence rules for
  machine context. (`c0cf9b6`)
- `docs/schema/security-intake.md` specifying how OSV, CVE, and advisory
  sources map into the `PV` family and require human review before changes.
  (`c0cf9b6`)
- Initial project documentation structure including `README.md`, motivation
  essay, and specification drafts. (`63f6729`)

### Changed

- `docs/rules/RULES.md` refocused as the canonical rule catalog with
  code-model guidance, family sections, PX/PV distinction, subcategory
  tables, and source notes merged into the catalog. (`ad2d6ec`)
- `README.md` trimmed to project entry point with navigation links to
  `docs/rules/RULES.md`, `docs/schema/`, and `docs/motivation.md`. (`ad2d6ec`)
- Structure family rule names refactored for readability and alignment with
  catalog wording. (`043bdd5`)
- Standard scope expanded from prompt-only to prompts and all governed
  documents under a Document-Driven Design (DDD+) model. (`02026ca`)
