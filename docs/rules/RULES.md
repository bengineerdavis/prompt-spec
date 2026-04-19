---
id: rules-catalog
title: Prompt Rules Catalog
description: Canonical catalog of rule families, code-model guidance, and draft rules in the Prompt Rules Standard.
version: 0.1.0
type: index
license: MIT
---

# Prompt Rules Catalog

This document is the canonical rule catalog for the Prompt Rules Standard. It defines the rule code model, family layout, subcategory guidance, and the current draft inventory.

If you are new to the project, start with the [root README](../../README.md). For the
security advisory intake and `PV` review flow, see
[../schema/security-intake.md](../schema/security-intake.md).

## Table of Contents

- [Prompt Rules Catalog](#prompt-rules-catalog)
  - [Code model](#code-model)
    - [Draft compatibility](#draft-compatibility)
  - [Rule families](#rule-families)
  - [PX and PV](#px-and-pv)
  - [Structure (`PS`)](#structure-ps)
  - [Clarity (`PC`)](#clarity-pc)
  - [Reasoning / consistency (`PR`)](#reasoning--consistency-pr)
  - [Safety / interpolation (`PX`)](#safety--interpolation-px)
  - [Security / vulnerability intelligence (`PV`)](#security--vulnerability-intelligence-pv)
  - [Metadata (`PM`)](#metadata-pm)
  - [Formatting / hygiene (`PF`)](#formatting--hygiene-pf)
  - [Prose / style (`PP`)](#prose--style-pp)
  - [References](#references)
    - [Why These Support Current Draft Rules](#why-these-support-current-draft-rules)

## Code model

The long-term rule identifier model is:

`P<FAMILY><AA><BB>`

Where:

- `P<FAMILY>` identifies the rule family.
- `<AA>` identifies a subcategory within that family.
- `<BB>` identifies a specific rule within that subcategory.

For example, `PS0102` means:

- `PS` — Structure
- `01` — missing structural elements
- `02` — second rule in that subcategory

`<FAMILY>` is a short alphabetic code of 1–3 letters. The catalog expects only a small number of top-level families. Most future growth should happen through family-local subcategories and rule indices rather than by proliferating new family codes.

### Draft compatibility

The current draft still uses shorter family-local identifiers such as `PS001` and `PX010` in some places. These are compact draft identifiers and may later map to explicit hierarchical codes.

Projects MAY define local extensions in reserved family-local subcategory ranges such as `90`–`99`, without renumbering upstream rules.

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

## PX and PV

`PX` rules describe general prompt-side safety and control patterns such as delimiter boundaries, trusted and untrusted content separation, and tool-output isolation. They are not inherently time-bound.

`PV` rules describe concrete, time-sensitive security and vulnerability findings derived from CVEs, OSV records, fuzzing, red-team exercises, or other security review. They function as security patches to the standard.

Over time, multiple `PV` rules may converge on the same underlying structural issue. When that happens, the catalog may introduce or refine a `PX` rule to capture the broader pattern, allowing implementations to gain categorical protection against related vulnerabilities.

For the advisory intake and review workflow that feeds the `PV` family, see [../schema/security-intake.md](../schema/security-intake.md).

## Structure (`PS`)

Subcategory guidance:

| `AA` | Meaning |
|---|---|
| `01` | Missing structural elements |
| `02` | Ordering / placement defects |
| `03` | Overloaded or entangled structure |
| `90`–`99` | Reserved for project-local or experimental extensions |

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| [`PS001`](./structure/missing/PS001-missing-explicit-task-or-objective.md) | Missing explicit task or objective | The prompt provides context or role framing but does not clearly state the requested task. | `TPL` | Draft |
| [`PS002`](./structure/missing/PS002-missing-role-or-persona-section.md) | Missing role or persona section | The prompt lacks explicit role framing where the project or house style expects it. | `TPL` | Draft |
| [`PS003`](./structure/missing/PS003-missing-output-contract.md) | Missing output contract | The prompt does not clearly specify the required output shape or format when one is expected. | `TPL` | Draft |
| [`PS010`](./structure/ordering/PS010-critical-instructions-buried.md) | Critical instructions buried | Important instructions are buried in the middle of a long prompt instead of being made prominent. | `LLM` | Draft |
| [`PS020`](./structure/overloaded/PS020-overloaded-all-in-one-section.md) | Overloaded all-in-one section | Task, constraints, examples, and formatting cues are packed into one undifferentiated block. | `LLM` | Draft |

Illustrative long-form mapping:

| Compact draft code | Hierarchical form |
|---|---|
| `PS001` | `PS0101` |
| `PS002` | `PS0102` |
| `PS003` | `PS0103` |
| `PS010` | `PS0201` |
| `PS020` | `PS0301` |

## Clarity (`PC`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PC001` | Vague objective | The task is underspecified or phrased too vaguely to reliably guide model behavior. | `LLM` | Draft |
| `PC002` | Open-ended filler language | Phrases like `etc.` or `and so on` weaken boundaries and leave the task under-specified. | `TPL` | Draft |
| `PC003` | Hedged imperative | Weak or hesitant phrasing reduces instruction clarity. | `LLM` | Draft |
| `PC010` | Mixed tone instructions | Tone or style directions conflict, such as asking for both highly formal and very casual writing. | `LLM` | Draft |

## Reasoning / consistency (`PR`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PR001` | Conflicting constraints | The prompt contains constraints or goals that appear to be in tension or contradiction. | `LLM` | Draft |
| `PR002` | Conflicting length guidance | The prompt gives incompatible brevity or verbosity instructions. | `LLM` | Draft |
| `PR003` | Redundant priority ordering | Multiple sections compete to define top priority, creating ambiguity. | `TPL` | Draft |
| `PR010` | Inconsistent output examples | One or more examples do not match the described output contract. | `LLM` | Draft |

## Safety / interpolation (`PX`)

Subcategory guidance:

| `AA` | Meaning |
|---|---|
| `01` | Trust-boundary and delimitation issues |
| `02` | Tool-output boundary issues |
| `90`–`99` | Reserved for project-local or experimental extensions |

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| [`PX001`](./safety/boundaries/PX001-undelimited-untrusted-input-slot.md) | Undelimited untrusted input slot | User-controlled content is interpolated without a strong delimiter boundary. | `TPL` | Draft |
| [`PX002`](./safety/boundaries/PX002-mixed-instructions-and-untrusted-content.md) | Mixed instructions and untrusted content | Trusted instructions and untrusted content appear in the same undifferentiated block. | `TPL` | Draft |
| [`PX003`](./safety/boundaries/PX003-slot-allows-instruction-like-content.md) | Slot allows instruction-like content | A slot may allow imperative phrases that can blur the intended control boundary. | `LLM` | Draft |
| [`PX010`](./safety/tool-output/PX010-weak-tool-output-boundary.md) | Weak tool-output boundary | Tool, database, or retrieved content is inserted without clear separation from instructions. | `LLM` | Draft |

Illustrative long-form mapping:

| Compact draft code | Hierarchical form |
|---|---|
| `PX001` | `PX0101` |
| `PX002` | `PX0102` |
| `PX003` | `PX0103` |
| `PX010` | `PX0201` |

## Security / vulnerability intelligence (`PV`)

The `PV` family is reserved for concrete, time-sensitive security and vulnerability findings backed by advisories, security testing, red-team exercises, or incident review.

No concrete `PV` rules are defined in this draft yet.

For how advisories, testing findings, and incidents flow into `PV`, see
[../schema/security-intake.md](../schema/security-intake.md).

## Metadata (`PM`)

The `PM` family exists because prompts increasingly behave like governed artifacts rather than throwaway strings. Once prompts are shared across teams, checked into repositories, versioned, routed through CI, or compiled into machine-readable catalogs, missing metadata becomes a real coordination problem.

Metadata rules help standardize how prompts declare identity, ownership, applicability, model notes, tags, and other context that tooling and humans need in order to manage prompt assets safely and consistently.

These rules belong in the catalog even when they do not directly affect model behavior, because they affect discoverability, traceability, and governance.

Current draft rules:

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PM001` | Missing prompt metadata declaration | Required metadata is absent. | `TPL` | Draft |
| `PM002` | Missing tags or domain notes | The prompt lacks expected tags, domain markers, or classification notes. | `TPL` | Draft |
| `PM010` | Ambiguous model notes | Model-specific guidance is vague, incomplete, or not actionable. | `LLM` | Draft |

## Formatting / hygiene (`PF`)

The `PF` family exists because prompts are maintained as files, templates, and governed documents. Mechanical inconsistencies such as trailing whitespace, mixed line endings, inconsistent fences, or unstable indentation create noisy diffs, brittle parsing, and unnecessary friction for both tooling and collaborators.

Formatting and hygiene rules are intentionally narrow. They focus on deterministic, low-risk cleanups rather than semantic rewrites. This keeps them safe for automation and useful in editor, formatter, and CI workflows.

These rules also create a clean foundation for higher-level analysis. A prompt catalog with normalized fences, indentation, and line endings is easier to lint, compare, compile, and review.

Current draft rules:

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PF001` | Trailing whitespace | One or more lines end with trailing spaces or tabs. | `AFX` | Draft |
| `PF002` | Mixed line endings | The prompt file mixes newline styles. | `AFX` | Draft |
| `PF003` | Inconsistent delimiters or fences | Equivalent structural delimiters are used inconsistently. | `AFX` | Draft |
| `PF010` | Inconsistent indentation in examples | Example blocks do not follow the expected indentation convention. | `TPL` | Draft |

## Prose / style (`PP`)

The `PP` family exists because prompt quality is shaped not only by structure and logic, but also by writing quality. Boilerplate phrasing, redundant instructions, weak imperatives, and bloated preambles can reduce clarity, increase ambiguity, and make prompts harder to maintain.

These rules should remain modest in scope. The goal is not to enforce one universal writing style, but to catch recurring prose patterns that predictably weaken prompts or make their intent harder to follow.

This family is especially useful when a prompt is technically complete but still reads as vague, repetitive, or needlessly indirect.

Current draft rules:

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PP001` | Generic filler phrasing | Boilerplate or generic “AI-sounding” phrasing reduces specificity. | `LLM` | Draft |
| `PP002` | Redundant phrasing | Instructions repeat the same idea without adding meaning. | `LLM` | Draft |
| `PP003` | Weak imperative style | The prompt uses indirect phrasing where a direct instruction would be clearer. | `LLM` | Draft |
| `PP010` | Overlong preamble | Persona or setup text dominates before the actual task is introduced. | `LLM` | Draft |

## References

### "Structure and boundaries"

##### "Best practices for prompt engineering with the OpenAI API"

- publisher: OpenAI
- notes: Recommends placing instructions at the beginning of prompts and separating instructions from context with clear delimiters such as `###` or `"""`.
- url: <https://help.openai.com/en/articles6654000-best-practices-for-prompt-engineering-with-the-openai-api>

##### "Prompt engineering"

- publisher: OpenAI
- notes: Recommends using Markdown and XML to mark logical boundaries, distinguish sections, and make prompts more readable and structured.
- url: <https://developers.openai.com/api/docs/guides/prompt-engineering>

##### "Prompting best practices"

- publisher: Anthropic
- notes: Recommends XML tags to separate instructions, context, examples, and variable inputs, especially in complex prompts.
- url: <https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices>

##### "Prompt design strategies"

- publisher: Google
- notes: Recommends direct instructions, consistent structure, and clear delimiters such as XML-style tags or Markdown headings.
- url: <https://ai.google.dev/gemini-api/docs/prompting-strategies>

##### "Prompt engineering techniques"

- publisher: Microsoft
- notes: Recommends clear syntax, separators, and explicit output structure to improve parseability and control.
- url: <https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/prompt-engineering>

##### "Effective context engineering for AI agents"

- publisher: Anthropic
- notes: Recommends organizing prompts into distinct sections such as instructions, tool guidance, and output description.
- url: <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>

### Why these support current draft rules

These sources collectively support several recurring draft rule themes already present in the catalog:

    PS rules around missing structure and buried instructions

    PX rules around delimiters, trust boundaries, and tool-output isolation

    PP and PC rules around vague phrasing, verbosity control, and directness

Drafting policy

A public source appearing here does not automatically make a rule normative. The standard still requires editorial judgment, implementation experience, and evidence review before a draft pattern becomes a stable rule.
