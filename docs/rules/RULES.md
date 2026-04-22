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

The canonical rule identifier model is:

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

Rule files live under:

`docs/rules/<family>/<subset>/`

Where:

- `<family>` is the family directory.
- `<subset>` is the normalized subset directory for that family.
- any Markdown file in one of those paths is treated as a rule file.

Projects MAY define local extensions in reserved family-local subcategory ranges such as `90`–`99`, without renumbering upstream rules.

## Rule families

| Family | Meaning                               |
| ------ | ------------------------------------- |
| `PS`   | Structure                             |
| `PC`   | Clarity                               |
| `PR`   | Reasoning / consistency               |
| `PX`   | Safety / interpolation                |
| `PV`   | Security / vulnerability intelligence |
| `PM`   | Metadata                              |
| `PF`   | Formatting / hygiene                  |
| `PP`   | Prose / style                         |

## PX and PV

`PX` rules describe general prompt-side safety and control patterns such as delimiter boundaries, trusted and untrusted content separation, and tool-output isolation. They are not inherently time-bound.

`PV` rules describe concrete, time-sensitive security and vulnerability findings derived from CVEs, OSV records, fuzzing, red-team exercises, or other security review. They function as security patches to the standard.

Over time, multiple `PV` rules may converge on the same underlying structural issue. When that happens, the catalog may introduce or refine a `PX` rule to capture the broader pattern, allowing implementations to gain categorical protection against related vulnerabilities.

For the advisory intake and review workflow that feeds the `PV` family, see [../schema/security-intake.md](../schema/security-intake.md).

## Structure (`PS`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `missing`     | Missing structural elements                           |
| `02`      | `ordering`    | Ordering / placement defects                          |
| `03`      | `overloaded`  | Overloaded or entangled structure                     |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

| Code                                                                         | Name                               | Summary                                                                                           | Fix   | Status |
| ---------------------------------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------- | ----- | ------ |
| [`PS0101`](./structure/missing/PS0101-missing-explicit-task-or-objective.md) | Missing explicit task or objective | The prompt provides context or role framing but does not clearly state the requested task.        | `TPL` | Draft  |
| [`PS0102`](./structure/missing/PS0102-missing-role-or-persona-section.md)    | Missing role or persona section    | The prompt lacks explicit role framing where the project or house style expects it.               | `TPL` | Draft  |
| [`PS0103`](./structure/missing/PS0103-missing-output-contract.md)            | Missing output contract            | The prompt does not clearly specify the required output shape or format when one is expected.     | `TPL` | Draft  |
| [`PS0201`](./structure/ordering/PS0201-critical-instructions-buried.md)      | Critical instructions buried       | Important instructions are buried in the middle of a long prompt instead of being made prominent. | `LLM` | Draft  |
| [`PS0301`](./structure/overloaded/PS0301-overloaded-all-in-one-section.md)   | Overloaded all-in-one section      | Task, constraints, examples, and formatting cues are packed into one undifferentiated block.      | `LLM` | Draft  |

## Clarity (`PC`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `objective`   | Objective and instruction clarity                     |
| `02`      | `boundaries`  | Boundary-softening or under-specifying language       |
| `03`      | `directives`  | Tone and directive consistency                        |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

| Code                                                                  | Name                       | Summary                                                                                           | Fix   | Status |
| --------------------------------------------------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------- | ----- | ------ |
| [`PC0101`](./clarity/objective/PC0101-vague-objective.md)             | Vague objective            | The task is underspecified or phrased too vaguely to reliably guide model behavior.               | `LLM` | Draft  |
| [`PC0201`](./clarity/boundaries/PC0201-open-ended-filler-language.md) | Open-ended filler language | Phrases like `etc.` or `and so on` weaken boundaries and leave the task under-specified.          | `TPL` | Draft  |
| [`PC0301`](./clarity/directives/PC0301-hedged-imperative.md)          | Hedged imperative          | Weak or hesitant phrasing reduces instruction clarity.                                            | `LLM` | Draft  |
| [`PC0302`](./clarity/directives/PC0302-mixed-tone-instructions.md)    | Mixed tone instructions    | Tone or style directions conflict, such as asking for both highly formal and very casual writing. | `LLM` | Draft  |

## Reasoning / consistency (`PR`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `conflicts`   | Constraint and objective conflicts                    |
| `02`      | `priority`    | Priority and ordering conflicts                       |
| `03`      | `examples`    | Output and example consistency                        |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

| Code                                                                    | Name                         | Summary                                                                                 | Fix   | Status |
| ----------------------------------------------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------- | ----- | ------ |
| [`PR0101`](./reasoning/conflicts/PR0101-conflicting-constraints.md)     | Conflicting constraints      | The prompt contains constraints or goals that appear to be in tension or contradiction. | `LLM` | Draft  |
| [`PR0102`](./reasoning/conflicts/PR0102-conflicting-length-guidance.md) | Conflicting length guidance  | The prompt gives incompatible brevity or verbosity instructions.                        | `LLM` | Draft  |
| [`PR0201`](./reasoning/priority/PR0201-redundant-priority-ordering.md)  | Redundant priority ordering  | Multiple sections compete to define top priority, creating ambiguity.                   | `TPL` | Draft  |
| [`PR0301`](./reasoning/examples/PR0301-inconsistent-output-examples.md) | Inconsistent output examples | One or more examples do not match the described output contract.                        | `LLM` | Draft  |

## Safety / interpolation (`PX`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `boundaries`  | Trust-boundary and delimitation issues                |
| `02`      | `tool-output` | Tool-output boundary issues                           |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

| Code                                                                               | Name                                     | Summary                                                                                      | Fix   | Status |
| ---------------------------------------------------------------------------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------- | ----- | ------ |
| [`PX0101`](./safety/boundaries/PX0101-undelimited-untrusted-input-slot.md)         | Undelimited untrusted input slot         | User-controlled content is interpolated without a strong delimiter boundary.                 | `TPL` | Draft  |
| [`PX0102`](./safety/boundaries/PX0102-mixed-instructions-and-untrusted-content.md) | Mixed instructions and untrusted content | Trusted instructions and untrusted content appear in the same undifferentiated block.        | `TPL` | Draft  |
| [`PX0103`](./safety/boundaries/PX0103-slot-allows-instruction-like-content.md)     | Slot allows instruction-like content     | A slot may allow imperative phrases that can blur the intended control boundary.             | `LLM` | Draft  |
| [`PX0201`](./safety/tool-output/PX0201-weak-tool-output-boundary.md)               | Weak tool-output boundary                | Tool, database, or retrieved content is inserted without clear separation from instructions. | `LLM` | Draft  |

## Security / vulnerability intelligence (`PV`)

The `PV` family is reserved for concrete, time-sensitive security and vulnerability findings backed by advisories, security testing, red-team exercises, or incident review.

No concrete `PV` rules are defined in this draft yet.

For how advisories, testing findings, and incidents flow into `PV`, see
[../schema/security-intake.md](../schema/security-intake.md).

## Metadata (`PM`)

Subcategory guidance:

| `AA`      | Subset dir       | Meaning                                               |
| --------- | ---------------- | ----------------------------------------------------- |
| `01`      | `required`       | Required metadata presence                            |
| `02`      | `classification` | Tags, classification, and domain markers              |
| `03`      | `model-notes`    | Model notes and compatibility annotations             |
| `90`–`99` | project-local    | Reserved for project-local or experimental extensions |

The `PM` family exists because prompts increasingly behave like governed artifacts rather than throwaway strings. Once prompts are shared across teams, checked into repositories, versioned, routed through CI, or compiled into machine-readable catalogs, missing metadata becomes a real coordination problem.

Metadata rules help standardize how prompts declare identity, ownership, applicability, model notes, tags, and other context that tooling and humans need in order to manage prompt assets safely and consistently.

These rules belong in the catalog even when they do not directly affect model behavior, because they affect discoverability, traceability, and governance.

Current draft rules:

| Code                                                                          | Name                                | Summary                                                                  | Fix   | Status |
| ----------------------------------------------------------------------------- | ----------------------------------- | ------------------------------------------------------------------------ | ----- | ------ |
| [`PM0101`](./metadata/required/PM0101-missing-prompt-metadata-declaration.md) | Missing prompt metadata declaration | Required metadata is absent.                                             | `TPL` | Draft  |
| [`PM0201`](./metadata/classification/PM0201-missing-tags-or-domain-notes.md)  | Missing tags or domain notes        | The prompt lacks expected tags, domain markers, or classification notes. | `TPL` | Draft  |
| [`PM0301`](./metadata/model-notes/PM0301-ambiguous-model-notes.md)            | Ambiguous model notes               | Model-specific guidance is vague, incomplete, or not actionable.         | `LLM` | Draft  |

## Formatting / hygiene (`PF`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `whitespace`  | Whitespace and line-ending hygiene                    |
| `02`      | `delimiters`  | Delimiters, fences, and markup hygiene                |
| `03`      | `examples`    | Example indentation and layout stability              |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

The `PF` family exists because prompts are maintained as files, templates, and governed documents. Mechanical inconsistencies such as trailing whitespace, mixed line endings, inconsistent fences, or unstable indentation create noisy diffs, brittle parsing, and unnecessary friction for both tooling and collaborators.

Formatting and hygiene rules are intentionally narrow. They focus on deterministic, low-risk cleanups rather than semantic rewrites. This keeps them safe for automation and useful in editor, formatter, and CI workflows.

These rules also create a clean foundation for higher-level analysis. A prompt catalog with normalized fences, indentation, and line endings is easier to lint, compare, compile, and review.

Current draft rules:

| Code                                                                             | Name                                 | Summary                                                                                         | Fix   | Status |
| -------------------------------------------------------------------------------- | ------------------------------------ | ----------------------------------------------------------------------------------------------- | ----- | ------ |
| [`PF0101`](./formatting/whitespace/PF0101-trailing-whitespace.md)                | Trailing whitespace                  | One or more lines end with trailing spaces or tabs.                                             | `AFX` | Draft  |
| [`PF0102`](./formatting/whitespace/PF0102-mixed-line-endings.md)                 | Mixed line endings                   | The prompt file mixes newline styles.                                                           | `AFX` | Draft  |
| [`PF0201`](./formatting/delimiters/PF0201-inconsistent-delimiters-or-fences.md)  | Inconsistent delimiters or fences    | Equivalent structural delimiters are used inconsistently.                                       | `AFX` | Draft  |
| [`PF0202`](./formatting/delimiters/PF0202-chat-citation-artifacts.md)            | Chat citation artifacts              | Chat/tool citation tokens like `[web:1073]` or `[file:1042]` appear in governed Markdown prose. | `AFX` | Draft  |
| [`PF0301`](./formatting/examples/PF0301-inconsistent-indentation-in-examples.md) | Inconsistent indentation in examples | Example blocks do not follow the expected indentation convention.                               | `TPL` | Draft  |

## Prose / style (`PP`)

Subcategory guidance:

| `AA`      | Subset dir    | Meaning                                               |
| --------- | ------------- | ----------------------------------------------------- |
| `01`      | `filler`      | Filler and redundancy                                 |
| `02`      | `directness`  | Imperative strength and directness                    |
| `03`      | `preamble`    | Preamble and framing excess                           |
| `90`–`99` | project-local | Reserved for project-local or experimental extensions |

The `PP` family exists because prompt quality is shaped not only by structure and logic, but also by writing quality. Boilerplate phrasing, redundant instructions, weak imperatives, and bloated preambles can reduce clarity, increase ambiguity, and make prompts harder to maintain.

These rules should remain modest in scope. The goal is not to enforce one universal writing style, but to catch recurring prose patterns that predictably weaken prompts or make their intent harder to follow.

This family is especially useful when a prompt is technically complete but still reads as vague, repetitive, or needlessly indirect.

Current draft rules:

| Code                                                           | Name                    | Summary                                                                        | Fix   | Status |
| -------------------------------------------------------------- | ----------------------- | ------------------------------------------------------------------------------ | ----- | ------ |
| [`PP0101`](./prose/filler/PP0101-generic-filler-phrasing.md)   | Generic filler phrasing | Boilerplate or generic “AI-sounding” phrasing reduces specificity.             | `LLM` | Draft  |
| [`PP0102`](./prose/filler/PP0102-redundant-phrasing.md)        | Redundant phrasing      | Instructions repeat the same idea without adding meaning.                      | `LLM` | Draft  |
| [`PP0201`](./prose/directness/PP0201-weak-imperative-style.md) | Weak imperative style   | The prompt uses indirect phrasing where a direct instruction would be clearer. | `LLM` | Draft  |
| [`PP0301`](./prose/preamble/PP0301-overlong-preamble.md)       | Overlong preamble       | Persona or setup text dominates before the actual task is introduced.          | `LLM` | Draft  |

## References

### "Structure and boundaries"

##### "Best practices for prompt engineering with the OpenAI API"

- publisher: OpenAI
- notes: Recommends placing instructions at the beginning of prompts and separating instructions from context with clear delimiters such as `###` or `"""`.
- url: <https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api>

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

### Why These Support Current Draft Rules

These sources collectively support several recurring draft rule themes already present in the catalog:

- `PS` rules around missing structure and buried instructions
- `PX` rules around delimiters, trust boundaries, and tool-output isolation
- `PP` and `PC` rules around vague phrasing, verbosity control, and directness

### Drafting policy

A public source appearing here does not automatically make a rule normative. The standard still requires editorial judgment, implementation experience, and evidence review before a draft pattern becomes a stable rule.
