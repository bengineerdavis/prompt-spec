# Prompt Rules Standard (Draft)

A rough draft of a markdown-first standard for cataloging prompt rules that can be used across tools.

The goal is to define a compact, neutral, portable rule catalogue for AI prompt quality
checks, prompt style checks, and related prompt-side diagnostics without binding the
rule namespace to any one implementation.

## Why?


## Goals

- Aggregate community-sourced and professionally-researched rules and evidence into 
a single, universal, prompt-specific, rule catalogue.
- Keep Markdown as the human source of truth.
- Generate machine-readable exports from Markdown.
 one, objective, catalogued standard.
- Support multiple implementations, including CLIs, editors, CI checks, and prompt management systems.
- Separate deterministic repair from template-guided and LLM-assisted repair.
- Stay honest about what prompt-side rules can and cannot validate.

## Non-goals

- Output evaluation, benchmark scoring, or runtime behavioral validation.
- Claims that a rule-clean prompt is automatically a good prompt.
- Claims that every prompt rule is model-independent.
- Claims that all prompt rewrites objectively improve quality.

## Core position

Prompt-side rules are useful for catching structural, clarity, reasoning, safety, metadata, formatting, and prose issues before runtime. They are best used as one layer in a larger quality workflow that also includes testing, review, and evals.

## Code format

Rule identifiers use a compact, neutral namespace:

`P<FAMILY><NNN>`

Where:
- `P` indicates a prompt rule.
- `<FAMILY>` is a 1-3 letter family code.
- `<NNN>` is a 3-digit rule number.

Examples:
- `PS001`
- `PC014`
- `PR003`
- `PX001`
- `PM001`
- `PF001`
- `PP010`

## Rule families

| Family | Meaning |
|---|---|
| `PS` | Structure |
| `PC` | Clarity |
| `PR` | Reasoning / consistency |
| `PX` | Safety / interpolation |
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

## Rule index

This draft uses a Ruff-style catalog layout: short family tables in the main index, with one file per rule containing rationale, detection guidance, repair guidance, limitations, community resources, research references, and other evidence.

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
| `PC020` | Overly generic caution text | Generic warning or safety language adds little concrete guidance. | `NONE` | Draft |

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

## Evidence model

Each full rule file should eventually include:

- a concise rule summary
- why the rule exists
- detection guidance
- repair guidance
- limitations and honesty notes
- community resources
- research references
- other evidence
- evidence, if any, that fixing the issue improves quality

The standard should distinguish:
- evidence that a pattern exists,
- evidence that it is harmful,
- evidence that a given fix helps,
- and evidence that the fix is safe to automate.

## Honesty criteria

This draft should challenge its own claims.

- Some prompt defects are real and statically observable.
- Some prompt rules are well-supported by community practice but only weakly supported by controlled studies.
- Some prompt rewrites are inherently semantic and may improve one metric while harming another.
- Some rules are hygiene checks and should not be marketed as quality-improving in a strong sense.
- Passing all prompt rules should never be presented as proof of prompt quality.

## Repository layout

- `README.md` — overview, code format, and rule index
- `docs/spec.md` — scope, principles, and honesty criteria
- `docs/codes.md` — namespace and reserved family guidance
- `docs/fix-kinds.md` — repair semantics and trust model
- `docs/tool-landscape.md` — related tools and how this differs
- `docs/research/` — papers, reports, and research notes tied to rules
- `docs/rules/` — one Markdown file per rule
- `scripts/build_catalog.py` — generate JSON, YAML, and TOML exports
- `schemas/rule.schema.json` — schema for generated rule entries

## Near-term next steps

- Rename existing draft rules to the `P<FAMILY><NNN>` format.
- Add a Ruff-style `docs/rules/README.md` page that links each code to its full rule file.
- Add a reusable rule-page template with embedded evidence sections.
- Add more draft rules for clarity and prose/style.
- Add simple CI to regenerate and validate the catalog.
