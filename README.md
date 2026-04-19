---
id: readme
title: Prompt Rules Standard (Draft)
description: A vendor-neutral, portable standard for linting and validating prompts and governed documents.
version: 0.1.0
type: documentation
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Prompt Rules Standard (Draft)

A vendor-neutral, portable standard for linting and validating AI prompts and governed documents. It provides a shared rulebook and catalog so tool builders can automate prompt quality checks, safety checks, and metadata validation without reinventing the same logic in isolation.

## Why

We are not building the linter; we are building the rulebook so others can build the tools.

Today, prompt knowledge is fragmented across vendor docs, papers, blog posts, internal playbooks, and scattered repositories. Teams repeatedly rebuild their own ad hoc checks for missing structure, conflicting instructions, unsafe interpolation, or inconsistent metadata. The Prompt Rules Standard exists to give that work a portable vocabulary and a stable place to live.

For the longer motivation and background, see [docs/motivation.md](./docs/motivation.md).

## What this is

- A portable catalog of prompt-side diagnostics that any CLI, editor, CI pipeline, or prompt management system can adopt.
- A Markdown-first standard whose human-readable rule documents can compile to machine-readable artifacts.
- A framework that separates deterministic fixes from template-guided patches and higher-risk semantic rewrites.

## What this is not

- Not an evaluation framework or a replacement for runtime testing.
- Not a guarantee that a rule-clean prompt is behaviorally correct.
- Not an attempt to enforce one universal writing style for all prompts.

## How it is organized

The project is split into a few core layers:

- **Root documentation** introduces the project and its goals.
- **`docs/rules/RULES.md`** is the canonical rule catalog, including rule families, code-model details, and the current inventory of draft rules.
- **`docs/schema/`** contains schema-adjacent documents such as frontmatter rules, extension guidance, and security intake process documentation.
- **Individual rule files** live under `docs/rules/` in family and subcategory paths.

## Rule IDs

Rule identifiers use short family codes such as `PS`, `PC`, and `PX`. The catalog is moving toward a hierarchical model that separates rule family, subcategory, and rule index while preserving compact draft identifiers during the transition.

For the code model, family layout, and catalog details, see [docs/rules/RULES.md](./docs/rules/RULES.md).

## Read next

- [docs/rules/RULES.md](./docs/rules/RULES.md) — canonical rule catalog
- [docs/schema/frontmatter.md](./docs/schema/frontmatter.md) — frontmatter and schema guidance
- [docs/schema/security-intake.md](./docs/schema/security-intake.md) — advisory intake and `PV` mapping workflow
- [docs/motivation.md](./docs/motivation.md) — longer project rationale and research context
