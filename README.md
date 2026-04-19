---
id: readme
title: Prompt Rules Standard (Draft)
description: A vendor-neutral, portable standard for linting and validating prompts and governed documents.
version: 0.1.0
type: documentation
license: MIT
***

<!-- Above: YAML frontmatter used by tools. -->

# Prompt Rules Standard (Draft)

A vendor-neutral, portable standard for linting and validating AI prompts. It provides a shared rule catalog—maintained in Markdown but compiling to machine-readable schemas—so the community can automate prompt quality checks without reinventing the wheel.

## Table of Contents

- [Why?](#why)
- [Goals & Non-Goals](#goals--non-goals)
- [Code format & Versioning](#code-format--versioning)
- [Rule families](#rule-families)
- [Fix kinds](#fix-kinds)
- [Rule catalogue](#rule-catalogue)

## Why?

We are not building the linter; we are building the rulebook so *you* can build the tools.

Today, practitioners are doing incredible work reverse-engineering model behaviors, but that knowledge is trapped in silos. Every team writes their own ad-hoc validation rules to catch under-specified instructions, conflicting constraints, or unsafe template injections.

Inspired by foundational protocols like LSP, ESLint, and the speed of `uv`, the goal of this project is to pool the community's hard-won knowledge into a shared, automatable standard. By establishing a common vocabulary for prompt defects, we can build an ecosystem where linters, formatters, and CI/CD pipelines plug seamlessly into one another.

*(See [docs/motivation.md](./docs/motivation.md) for the deeper background, examples, and ecosystem context.)*

## Goals & Non-Goals

**What this is:**

- **A portable rule catalogue:** A shared set of prompt-side diagnostics (structure, clarity, safety, etc.) that any CLI, editor, CI pipeline, or prompt management system can adopt.
- **Markdown-first:** Human-readable rule definitions that compile down to JSON/YAML for tools to consume.
- **Honest about repairs:** A system that strictly separates safe, deterministic formatting fixes from risky, LLM-assisted semantic rewrites.

**What this isn't:**

- **Not an evaluation framework:** Linting does not replace runtime testing, benchmark scoring, or behavioral evals.
- **Not a guarantee of quality:** A rule-clean prompt is not automatically a *good* prompt. Prompt quality remains highly contextual and model-dependent.
- **Not an enforcement of "one true style":** It is a framework to catch known defects, not to enforce a rigid, universal writing style.

## Code format & Versioning

Rule identifiers use a compact, neutral namespace:

`P<FAMILY><NNN>`

Where:

- `P` indicates a prompt rule.
- `<FAMILY>` is a 1-3 letter family code.
- `<NNN>` is a 3-digit rule number.

### File Versioning (YAML Frontmatter)

Every rule document MUST begin with YAML frontmatter that conforms to the Prompt Rules Standard frontmatter schema. At minimum, rule documents must include `id`, `title`, `description`, `version`, `type`, and `license`.

```yaml
---
id: PS001
title: Missing explicit task or objective
description: Detects prompts that provide context or role framing but never state a clear task or requested outcome.
version: 0.1.0
type: rule
license: MIT
---
```

Rule-specific details such as family membership, fix kind, status, and other catalog metadata SHOULD be expressed in the rule body or under `x-*` extension keys until standardized in the schema.

### Rule numbering within families

Within each family, the last three digits are grouped into conceptual bands rather than treated as a simple counter.

For example, in the `PS` family:

- `PS001`–`PS009` — missing structural elements
- `PS010`–`PS019` — ordering and placement defects
- `PS020`–`PS029` — overloaded or entangled structure

This leaves room to add new related rules near existing ones without renumbering the catalog.

**Versioning logic:**

- **MAJOR:** The core definition or detection logic changed (Breaking).
- **MINOR:** A new valid pattern or fix method was added (Backwards-compatible).
- **PATCH:** Documentation typos fixed or rationale clarified (No logic changes).

## Rule families

| Family | Meaning |
|---|---|
| `PS` | Structure |
| `PC` | Clarity |
| `PR` | Reasoning / consistency |
| `PX` | Safety / interpolation |
| `PV` | Security / vulnerability intelligence |
| `PM` | Metadata |
| `PF` | Formatting / hygiene |
| `PP` | Prose / style |

## Fix kinds

| Code | Meaning |
|---|---|
| `AFX` | Deterministic mechanical repair |
| `TPL` | Template-guided structured patch |
| `LLM` | Semantic repair requiring LLM assistance and verification |
| `NONE` | Diagnostic only |

## Rule catalogue

The full rule catalogue lives in [docs/rules/RULES.md](./docs/rules/RULES.md).

That document lists all rule families and draft rules, and links to the individual rule documents as they are added.
