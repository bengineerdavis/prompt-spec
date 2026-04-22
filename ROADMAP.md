---
id: roadmap
title: Roadmap
description: Planned structural additions and ecosystem features for the Prompt Rules Standard.
version: 0.1.0
type: roadmap
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Roadmap

> **Alpha notice:** This is an early-stage, community-driven project. Nothing
> in this roadmap is a firm commitment. All items are tentative high-interest
> goals — directions we think are worth exploring, not a release schedule or
> guaranteed feature list. Priorities may change as the project and its
> community evolve.

This document outlines proposed structural additions to the Prompt Rules
Standard and near-term ecosystem work we think is worth pursuing.

## 1. Prompt Anatomy & Strictness Levels

The current catalog lists individual defects, but it does not yet define a
shared view of what a well-structured prompt looks like.

We are interested in defining a **Prompt Anatomy Guide** that outlines the
structural sections of a high-quality prompt (for example: Role, Objective,
Constraints, Output Schema, Context).

To support different project needs, this guide would classify components and
rules using standard [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119)
terminology:

- **REQUIRED (Strict):** Elements that must be present for a prompt to be
  considered valid under this standard.
- **RECOMMENDED (Standard):** Elements that should be used in most cases, but
  can be safely ignored if the developer understands the trade-offs.
- **OPTIONAL (Loose):** Elements that are useful in specific edge cases (for
  example, few-shot examples) but are not strictly necessary.

This would allow individual projects to set a linter strictness level (for
example, `strict`, `standard`, `loose`) based on their team's maturity and
risk tolerance.

*This item is high-interest but has no committed timeline.*

## 2. Project-Specific & Experimental Rules

No universal standard can capture every domain-specific need. Teams building
healthcare chatbots will need different prompt rules than teams building
coding agents.

We plan to define an **Extension Standard** for custom rules:

- **Experimental / custom namespaces:** Reserve a specific family or prefix
  for project-local or proprietary rules so they never collide with official
  standard updates. The precise family code is still under discussion, but the
  goal is clear: make project-local rules visibly distinct from standardized
  ones.
- **Custom rule schemas:** Define how a tool-maker can write configuration
  (for example, a `.promptlintrc` file) that imports community-developed rules
  or project-specific checks alongside the official catalog.
- **Plugin architecture guidelines:** Provide recommendations for tool
  builders on how to expose a plugin interface for custom rules, similar to
  how ESLint or markdownlint handle community plugins.

*This item is high-interest and will likely evolve alongside early adopters
building custom rules and plugins.*

## 3. Document-Driven Design

The standard is evolving from a prompt-only framing toward a broader
**Document-Driven Design (DDD+)** model.

Under this model, prompts remain important, but they are treated as one kind of
governed document among many. Specifications, rule docs, READMEs, architecture
notes, runbooks, policies, and other project documents can all contribute
design context when teams choose to make them machine-usable.

Not all documents should be included in machine context. The standard therefore
needs both file-level inclusion metadata and repository-level ignore behavior
so teams can govern what is and is not considered machine-ingestible context.

Planned work includes:

- Clarifying how prompt rules apply to non-prompt documents.
- Defining recommended sections and patterns for governed documentation
  (for example, architecture notes, runbooks, security policies).
- Providing guidance on when documents should be included in machine context
  versus when they should remain human-only.

*This item is high-interest and foundational to how the standard scales beyond
prompt-centric use cases.*

## 4. Frontmatter, Documentation & Ecosystem

The standard is intentionally Markdown-first and file-based. To make
governed files portable and machine-readable, we plan to:

- **Stabilize the minimal frontmatter schema.**

  - Keep the core author-owned fields small (`id`, `title`, `description`,
    `version`, `license`).
  - Treat fields such as `type` and `context.include` as optional, allowing
    tools and repository conventions to infer or enforce them when appropriate.
  - Use `x-*` top-level keys for extensions instead of a generic metadata bag.

- **Publish and maintain JSON Schema.**

  - Use a human-readable Markdown document as the frontmatter specification.
  - Generate and validate `schema/frontmatter.schema.json` from that spec.

- **Define documentation and security families.**

  - `PD` for documentation-specific rules (structure, links, cross-references,
    machine-ingestible Markdown).
  - `PV` for security and vulnerability intelligence (advisories, known unsafe
    patterns, trust-boundary declarations).
  - Treat security as a separate classification axis from how a file is fixed.

- **Clarify Markdown rule mappings.**

  - Map existing Markdown rules (for example, from markdownlint) into the
    Prompt Rules Standard where they are equivalent, instead of reinventing
    them.
  - Document when to use strict rules versus more permissive styles in
    documentation vs. prompt contexts.
  - Prefer existing code and rule semantics unless a rule truly needs to be
    modified for this standard.

- **Add repo automation for consistency.**

  - Keep a human-readable `CHANGELOG.md` using fragment-based aggregation.
  - Use pre-commit hooks to enforce frontmatter ordering, `x-*` placement, and
    schema freshness.
  - Use a linter/formatter for helper scripts and supporting code.

*This item is actively evolving as frontmatter usage and tooling stabilize in
early adopters’ repositories.*

## 5. Prompt Format Compatibility (Dotprompt & friends)

To improve interoperability with existing prompt tooling, the standard will
add compatibility guidance and conversion support for widely used prompt file
formats and prompt-management systems.

The first compatibility target is **Dotprompt**, since it is already used as
a frontmatter-based prompt format and aligns well with this standard’s
Markdown-first approach.

Planned work includes:

- **Dotprompt compatibility**

  - Define how Dotprompt frontmatter maps into Prompt Rules frontmatter and
    `prompt_spec`.
  - Define which fields are author-owned versus tool-owned during import and
    export.
  - Support conversion between Prompt Rules Markdown files and
    Dotprompt-compatible files where practical.
  - Document any fields that cannot be mapped cleanly without loss.
  - Treat Dotprompt support as an early interoperability target rather than
    deferred ecosystem work.

- **Prompt tool compatibility inventory**

  - Survey major prompt tools and formats to identify the most useful
    interoperability targets.
  - Prioritize tools that already treat prompts as versioned files or
    structured prompt artifacts.

Initial candidates include:

- Dotprompt / Genkit
- Langfuse prompt management
- Promptfoo
- LiteLLM prompt management

The goal is not to make this standard dependent on any one runtime or vendor.
The goal is to let governed Markdown prompts participate in the existing prompt
tooling ecosystem with minimal friction.

*Dotprompt compatibility is a near-term priority, since it directly supports
file-based workflows already emerging in the community.*

## 6. Security & Advisory Intake

Security needs its own explicit treatment in both the rule catalog and the
surrounding tooling.

The roadmap includes a path toward integrating CVEs, OSV data, dependency
alerts, and similar advisory sources into the evolution of the standard. The
goal is not to let advisories silently rewrite the standard, but to generate
review inputs for security-related rule updates and documentation changes.

This requires:

- a machine-readable advisory intake model,
- mappings from advisory signals into the security family,
- and clear review boundaries so external intelligence informs the standard
  without bypassing human editorial control.

*This item is medium-interest and will likely follow once the PV family is
more fully defined.*

## 7. Tooling architecture and pluggable checkers

The Prompt Rules Standard is intended to be tool-agnostic and ecosystem-
friendly. No single checker, formatter, or editor is authoritative. Instead,
the standard specifies rule semantics and data contracts that multiple tools
can implement.

We plan to define:

- A language-neutral **diagnostic schema** for checkers.
- A language-neutral **rewrite contract** for formatters and fixers.
- Clear separation between:
  - the **standard** (rule definitions, rule IDs, semantics),
  - **reference tools** (one possible implementation),
  - and **consumers** (IDEs, CLIs, services, CI, agents).

Any tool that can read governed Markdown and emit diagnostics in the agreed
schema should be considered a first-class participant in the ecosystem.

### Minimal JSON diagnostic schema (illustrative)

The following schema is an example of how a checker can report results in a
tool-agnostic way. It is not final, but illustrates the intended shape:

```json
{
  "tool": "prompt-rules-check",
  "version": "0.1.0",
  "diagnostics": [
    {
      "rule_id": "PF005",
      "severity": "warning",
      "message": "Chat citation artifact detected.",
      "path": "docs/ROADMAP.md",
      "line": 42,
      "column": 17,
      "end_line": 42,
      "end_column": 28,
      "fixable": true
    }
  ]
}
```

Guiding principles:

- The standard defines allowed `rule_id` values and their meaning.
- Checkers are free to be implemented in any language or runtime as long as
  they can emit diagnostics in this shape (or in a formally published variant).
- Consumers (for example editors, CI, or higher-level tools) should treat any
  conforming checker as a drop-in source of diagnostics.

Future work may introduce optional fields for rule metadata, suggested fixes,
or integration with other diagnostic formats, but the minimal contract aims
to stay small and stable so that multiple implementations can coexist.

## 8. Deferred Work

The following ideas are useful, but not yet part of the core standard or
active tooling:

- richer metadata beyond the minimal frontmatter fields,
- ingestion-mode flags beyond `context.include`,
- a formal advisory-ingestion pipeline for external data sources,
- promotion of certain extension metadata into the core schema once it proves
  broadly useful,
- automated semantic versioning and release tagging,
- deeper automation for doc-by-doc updates and changelog synthesis.

*These items are intentionally deferred until core concepts and early
tooling have stabilized.*
