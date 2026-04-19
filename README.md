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

A vendor-neutral, portable standard for linting and validating prompts and governed documents. It provides a shared rule catalog—maintained in Markdown but compiling to machine-readable schemas—so the community can automate prompt quality checks without reinventing the wheel.

## Table of Contents

- [Why?](#why)
- [Goals & Non-Goals](#goals--non-goals)
- [Code format & Versioning](#code-format--versioning)
- [Rule families](#rule-families)
- [Fix kinds](#fix-kinds)
- [Minimal frontmatter](#minimal-frontmatter)
- [Context inclusion](#context-inclusion)
- [Markdown compatibility](#markdown-compatibility)
- [Rule index](#rule-index)

## Why?

We are not building the linter; we are building the rulebook so *you* can build the tools.

Today, practitioners are doing incredible work reverse-engineering model behaviors, but that knowledge is trapped in silos. Every team writes their own ad-hoc validation rules to catch under-specified instructions, conflicting constraints, unsafe template injections, or brittle document structures.

Inspired by foundational protocols like LSP, ESLint, and the speed of `uv`, the goal of this project is to pool the community's hard-won knowledge into a shared, automatable standard. By establishing a common vocabulary for prompt and document defects, we can build an ecosystem where linters, formatters, and CI/CD pipelines plug seamlessly into one another.

*(See [docs/motivation.md](./docs/motivation.md) for the deeper background, examples, and ecosystem context).*

## Goals & Non-Goals

**What this is:**

- **A portable rule catalogue:** A shared set of prompt-side and document-side diagnostics (structure, clarity, safety, metadata, etc.) that any CLI, editor, CI pipeline, or prompt management system can adopt.
- **Markdown-first:** Human-readable rule definitions that compile down to JSON/YAML for tools to consume.
- **Honest about repairs:** A system that strictly separates safe, deterministic formatting fixes from risky, LLM-assisted semantic rewrites.
- **Document-driven:** A step toward document-driven design, where governed human-language artifacts can act as design context for both humans and machines.

**What this isn't:**

- **Not an evaluation framework:** Linting does not replace runtime testing, benchmark scoring, or behavioral evals.
- **Not a guarantee of quality:** A rule-clean prompt or document is not automatically a *good* one. Quality remains contextual and system-dependent.
- **Not an enforcement of "one true style":** It is a framework to catch known defects, not to enforce a rigid, universal writing style.

## Code format & Versioning

Rule identifiers use a compact, neutral namespace:

`P<FAMILY><NNN>`

Where:

- `P` indicates a prompt-rule standard code.
- `<FAMILY>` is a 1-3 letter family code.
- `<NNN>` is a 3-digit rule number.

### File Versioning (YAML Frontmatter)

Governed files use YAML frontmatter with Semantic Versioning (`MAJOR.MINOR.PATCH`) and minimal metadata. This ensures tools can safely parse, validate, and track changes to rule definitions and related documents.

```yaml
---
id: PS001
title: Missing explicit task or objective
description: The prompt lacks a clearly stated task.
version: 0.1.0
type: rule
license: MIT
---

```

**Versioning logic:**

- **MAJOR:** The core definition or detection logic changed (breaking).
- **MINOR:** A new valid pattern or fix method was added (backwards-compatible).
- **PATCH:** Documentation typos fixed or rationale clarified (no logic changes).

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
| `PD` | Documentation |

## Fix kinds

| Code | Meaning |
|---|---|
| `AFX` | Deterministic mechanical repair |
| `TPL` | Template-guided structured patch |
| `LLM` | Semantic repair requiring LLM assistance and verification |
| `NONE` | Diagnostic only |

## Minimal frontmatter

Governed files in this repository use a minimal YAML frontmatter block.

Required fields:

- `id`
- `title`
- `description`
- `version`
- `type`
- `license`

Optional standard fields:

- `context.include`

The `type` field uses a closed standard vocabulary:

- `documentation`
- `rule`
- `schema`
- `index`
- `roadmap`

Non-standard metadata is allowed only through top-level `x-*` keys, which must appear after all standard fields.

See `docs/schema/frontmatter.md` and `schema/frontmatter.schema.json`.

## Context inclusion

Files are included in machine context by default unless excluded by repository-level ignore rules or explicitly disabled in frontmatter.

A file may opt out explicitly with:

```yaml
context:
  include: false
```

This supports a broader document-driven design model where some documents are intended as governed machine-readable context and others are not.

## Markdown compatibility

Where existing Markdown rules already solve the problem, this standard should incorporate them by reference rather than reinvent them. If a rule is materially equivalent to an existing `MDxxx` rule, tools may mirror the integer suffix for easier mapping.

This standard should prefer existing rule code and semantics unless a rule truly needs to be modified for prompt-specific or documentation-specific behavior.

## Rule index

This draft uses a catalog layout: short family tables in the main index, with one file per rule containing rationale, detection guidance, repair guidance, limitations, and empirical evidence.

### Structure (`PS`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PS001` | Missing explicit task or objective | The prompt provides context or role framing but does not clearly state the requested task. | `TPL` | Draft |
| `PS002` | Missing role or persona section | The prompt lacks explicit role framing where the project or house style expects it. | `TPL` | Draft |
| `PS003` | Missing output contract | The prompt does not clearly specify the required output shape or format when one is expected. | `TPL` | Draft |
| `PS010` | Critical instructions buried | Important instructions are buried in the middle of a long prompt instead of being made prominent. | `LLM` | Draft |
| `PS020` | Overloaded all-in-one section | Task, constraints, examples, and formatting cues are packed into one undifferentiated block. | `LLM` | Draft |

### Clarity (`PC`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PC001` | Vague objective | The task is underspecified or phrased too vaguely to reliably guide model behavior. | `LLM` | Draft |
| `PC002` | Open-ended filler language | Phrases like `etc.` or `and so on` weaken boundaries and leave the task under-specified. | `TPL` | Draft |
| `PC003` | Hedged imperative | Weak or hesitant phrasing reduces instruction clarity. | `LLM` | Draft |
| `PC010` | Mixed tone instructions | Tone or style directions conflict, such as asking for both highly formal and very casual writing. | `LLM` | Draft |

### Reasoning / consistency (`PR`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PR001` | Conflicting constraints | The prompt contains constraints or goals that appear to be in tension or contradiction. | `LLM` | Draft |
| `PR002` | Conflicting length guidance | The prompt gives incompatible brevity or verbosity instructions. | `LLM` | Draft |
| `PR003` | Redundant priority ordering | Multiple sections compete to define top priority, creating ambiguity. | `TPL` | Draft |
| `PR010` | Inconsistent output examples | One or more examples do not match the described output contract. | `LLM` | Draft |

### Safety / interpolation (`PX`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PX001` | Undelimited untrusted input slot | User-controlled content is interpolated without a strong delimiter boundary. | `TPL` | Draft |
| `PX002` | Mixed instructions and untrusted content | Trusted instructions and untrusted content appear in the same undifferentiated block. | `TPL` | Draft |
| `PX003` | Slot allows instruction-like content | A slot may allow imperative phrases that can blur the intended control boundary. | `LLM` | Draft |
| `PX010` | Weak tool-output boundary | Tool, database, or retrieved content is inserted without clear separation from instructions. | `LLM` | Draft |

### Security / vulnerability intelligence (`PV`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PV001` | Known vulnerable pattern reference | The file references or preserves a known unsafe pattern documented by policy or advisory. | `NONE` | Draft |
| `PV002` | Unsafe dependency in prompt pipeline | A dependency used in prompt or document processing matches a known advisory. | `NONE` | Draft |
| `PV003` | Missing trust-boundary declaration | The file consumes external or untrusted context but does not declare its trust model. | `TPL` | Draft |
| `PV004` | Advisory supersedes local rule | A newer external advisory conflicts with an existing local rule definition and should trigger review. | `NONE` | Draft |

### Metadata (`PM`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PM001` | Missing prompt metadata declaration | Required metadata is absent. | `TPL` | Draft |
| `PM002` | Missing tags or domain notes | The prompt lacks expected tags, domain markers, or classification notes. | `TPL` | Draft |
| `PM010` | Ambiguous model notes | Model-specific guidance is vague, incomplete, or not actionable. | `LLM` | Draft |

### Formatting / hygiene (`PF`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PF001` | Trailing whitespace | One or more lines end with trailing spaces or tabs. | `AFX` | Draft |
| `PF002` | Mixed line endings | The prompt file mixes newline styles. | `AFX` | Draft |
| `PF003` | Inconsistent delimiters or fences | Equivalent structural delimiters are used inconsistently. | `AFX` | Draft |
| `PF010` | Inconsistent indentation in examples | Example blocks do not follow the expected indentation convention. | `TPL` | Draft |

### Prose / style (`PP`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PP001` | Generic filler phrasing | Boilerplate or generic “AI-sounding” phrasing reduces specificity. | `LLM` | Draft |
| `PP002` | Redundant phrasing | Instructions repeat the same idea without adding meaning. | `LLM` | Draft |
| `PP003` | Weak imperative style | The prompt uses indirect phrasing where a direct instruction would be clearer. | `LLM` | Draft |
| `PP010` | Overlong preamble | Persona or setup text dominates before the actual task is introduced. | `LLM` | Draft |

### Documentation (`PD`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PD001` | Missing document metadata declaration | Required document metadata is absent. | `TPL` | Draft |
| `PD002` | Missing context inclusion signal | The file does not make machine-context intent clear where the project expects it. | `TPL` | Draft |
| `PD010` | Missing H1 | The document lacks a top-level heading. | `TPL` | Draft |
| `PD020` | Dead link or unresolved reference | The document contains a broken link or unresolved cross-reference. | `NONE` | Draft |
