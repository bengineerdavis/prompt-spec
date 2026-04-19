---
id: rules-catalog
title: Prompt Rules Catalog
description: Index and high-level descriptions of Prompt Rules Standard rule families and individual rules.
version: 0.1.0
type: index
license: MIT
***

# Prompt Rules Catalog

This document is the human-readable catalogue of rule families and draft rules in the Prompt Rules Standard. Each rule should ultimately have its own Markdown document with schema-valid frontmatter and detailed guidance.

## PX and PV

`PX` rules describe general prompt-side safety and control patterns such as delimiter boundaries, trusted and untrusted content separation, and tool-output isolation. They are not inherently time-bound.

`PV` rules describe concrete, time-sensitive security and vulnerability findings derived from CVEs, OSV records, fuzzing, red-team exercises, or other security review. They function as security patches to the standard.

Over time, multiple `PV` rules may converge on the same underlying structural issue. When that happens, the catalog may introduce or refine a `PX` rule to capture the broader pattern, allowing implementations to gain categorical protection against related vulnerabilities.

## Rule numbering within families

Within each family, the last three digits are grouped into conceptual bands rather than treated as a simple counter. This allows the catalog to reserve room for related rules and keep neighboring codes semantically related.

For example, in the `PS` family:

- `PS001`–`PS009` — missing structural elements
- `PS010`–`PS019` — ordering and placement defects
- `PS020`–`PS029` — overloaded or entangled structure

Families may define their own subcategory bands and directory layout over time.

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

## Structure (`PS`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PS001` | Missing explicit task or objective | The prompt provides context or role framing but does not clearly state the requested task. | `TPL` | Draft |
| `PS002` | Missing role or persona section | The prompt lacks explicit role framing where the project or house style expects it. | `TPL` | Draft |
| `PS003` | Missing output contract | The prompt does not clearly specify the required output shape or format when one is expected. | `TPL` | Draft |
| `PS010` | Critical instructions buried | Important instructions are buried in the middle of a long prompt instead of being made prominent. | `LLM` | Draft |
| `PS020` | Overloaded all-in-one section | Task, constraints, examples, and formatting cues are packed into one undifferentiated block. | `LLM` | Draft |

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

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PX001` | Undelimited untrusted input slot | User-controlled content is interpolated without a strong delimiter boundary. | `TPL` | Draft |
| `PX002` | Mixed instructions and untrusted content | Trusted instructions and untrusted content appear in the same undifferentiated block. | `TPL` | Draft |
| `PX003` | Slot allows instruction-like content | A slot may allow imperative phrases that can blur the intended control boundary. | `LLM` | Draft |
| `PX010` | Weak tool-output boundary | Tool, database, or retrieved content is inserted without clear separation from instructions. | `LLM` | Draft |

## Security / vulnerability intelligence (`PV`)

The `PV` family is reserved for concrete, time-sensitive security and vulnerability findings backed by advisories, security testing, red-team exercises, or incident review.

No concrete `PV` rules are defined in this draft yet.

## Metadata (`PM`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PM001` | Missing prompt metadata declaration | Required metadata is absent. | `TPL` | Draft |
| `PM002` | Missing tags or domain notes | The prompt lacks expected tags, domain markers, or classification notes. | `TPL` | Draft |
| `PM010` | Ambiguous model notes | Model-specific guidance is vague, incomplete, or not actionable. | `LLM` | Draft |

## Formatting / hygiene (`PF`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PF001` | Trailing whitespace | One or more lines end with trailing spaces or tabs. | `AFX` | Draft |
| `PF002` | Mixed line endings | The prompt file mixes newline styles. | `AFX` | Draft |
| `PF003` | Inconsistent delimiters or fences | Equivalent structural delimiters are used inconsistently. | `AFX` | Draft |
| `PF010` | Inconsistent indentation in examples | Example blocks do not follow the expected indentation convention. | `TPL` | Draft |

## Prose / style (`PP`)

| Code | Name | Summary | Fix | Status |
|---|---|---|---|---|
| `PP001` | Generic filler phrasing | Boilerplate or generic “AI-sounding” phrasing reduces specificity. | `LLM` | Draft |
| `PP002` | Redundant phrasing | Instructions repeat the same idea without adding meaning. | `LLM` | Draft |
| `PP003` | Weak imperative style | The prompt uses indirect phrasing where a direct instruction would be clearer. | `LLM` | Draft |
| `PP010` | Overlong preamble | Persona or setup text dominates before the actual task is introduced. | `LLM` | Draft |
