# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

<!-- scriv-insert-here -->

## [Unreleased]

### Added
- Initial frontmatter schema documentation and JSON Schema.

- `docs/schema/context-inclusion.md` specifying `.promptignore`, `context.include`,
  default inclusion behavior, and precedence rules for machine context.
- `docs/schema/security-intake.md` specifying how OSV, CVE, and Dependabot
  advisories map into the `PV` family and require human review before changes.

- Initial changelog, Scriv integration, and pre-commit automation.
- Frontmatter extension rules using `x-*` keys.
- Minimal repository docs with standardized frontmatter.
- `PV` (Security / vulnerability intelligence) rule family with starter rules
  `PV001`–`PV004` covering known vulnerable patterns, unsafe dependencies,
  missing trust-boundary declarations, and advisory conflicts.
- `PD` (Documentation) rule family with starter rules `PD001`, `PD002`,
  `PD010`, and `PD020` covering missing metadata, missing context inclusion
  signal, missing H1, and dead links.
- `context.include` frontmatter field allowing files to opt out of machine
  context explicitly.
- `## Type vocabulary` section to `docs/schema/frontmatter.md` as the single
  source of truth for the closed `type` enum.
- Document-Driven Design (DDD+) framing in `docs/motivation.md` establishing
  that prompts are one governed document type among many.
- Markdown compatibility section to README clarifying that existing `MDxxx`
  rule semantics are reused by reference unless a rule requires modification.

### Changed
- Reduced the schema to essential required and optional fields.
- Adopted a minimal frontmatter schema with required `license` and optional
  `context.include`.
- Standard scope expanded from prompt-only to prompts and governed documents.
- `scripts/build_frontmatter_schema.py` now derives required fields, `type`
  enum, and property shapes from `docs/schema/frontmatter.md` at build time
  rather than hardcoding them in the script.
- README opening description updated to reflect expanded scope.

## [0.1.0] - 2026-04-18

### Added
- Initial draft of the Prompt Rules Standard.
