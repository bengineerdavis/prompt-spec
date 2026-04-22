---
id: contributing
title: Contributing Guide
description: How to contribute to the Prompt Rules Standard.
version: 0.1.0
type: documentation
license: MIT
---

# Contributing Guide

This guide is written for human contributors, LLMs, and autonomous agents
working with this repository.

______________________________________________________________________

## 1. Architecture: Where Information Goes

We strictly separate the "what" from the "why" to respect the reader's time.

- **`README.md`:** Project entry point. Scannable in 15 seconds. Contains a
  short summary, goals/non-goals, and links to further reading. Do not add
  long narratives here.
- **`docs/motivation.md`:** The narrative home for the project. Origin story,
  historical context, and ecosystem critiques belong here.
- **`docs/rules/RULES.md`:** The canonical rule catalog. Family definitions,
  code model, subcategory guidance, and the current rule inventory.
- **`docs/rules/rule-file.template.md`:** The template to use when authoring
  or revising individual rule files.
- **Rule files (`docs/rules/**/*.md`):** Must be spec-like and structured.
  Focus on detection, repair, examples, and rule-specific references.
- **`SECURITY.md`:** Security reporting policy. All security reports start here.

______________________________________________________________________

## 2. Proposing a New Rule

1. Check `docs/rules/RULES.md` to confirm the rule does not already exist or
   overlap significantly with a current draft.
1. Identify the correct rule family (`PS`, `PC`, `PR`, `PX`, `PV`, `PM`,
   `PF`, `PP`) and the appropriate subcategory directory path.
1. Use `docs/rules/rule-file.template.md` as your starting point.
1. Include rule-specific references that directly support the rule's existence.
1. Include at least one non-compliant and one compliant example. For broad
   rules, include multiple examples that show the range of triggering patterns.
1. Open an issue or PR with your draft rule file.

______________________________________________________________________

## 3. Rule Identifier Format

Rule identifiers use the following hierarchical model:

`P<FAMILY><AA><BB>`

Where:

- `P<FAMILY>` — rule family, for example `PS`, `PC`, `PX`
- `<AA>` — subcategory within that family
- `<BB>` — specific rule within that subcategory

Example: `PS0102` means Structure family, subcategory `01` (missing elements),
rule `02`.

The current draft still uses shorter compact identifiers such as `PS001` and
`PX010` in some places. These are valid draft identifiers during the transition
to the full hierarchical model.

Fix kinds must always be formatted as: `` `AFX` ``, `` `TPL` ``, `` `LLM` ``,
or `` `NONE` ``.

______________________________________________________________________

## 4. Tone and Voice

Our voice is **practitioner-to-practitioner**. We are engineers solving a
fragmentation problem, not marketers selling a product.

- **Be honest about limitations:** Always acknowledge what static analysis
  cannot do. For example: *"Prompt rules are not a substitute for evals. A
  rule-clean prompt is not automatically a good prompt."*
- **Use empirical backing:** Do not state opinions as facts. Back up anti-patterns
  with academic papers, vendor documentation, or visible community consensus.
- **Avoid hype words:** Do not use words like "revolutionary," "game-changing,"
  or "ultimate." Use precise verbs: "abstracts," "formalizes," "detects,"
  "validates."
- **Spec vs. narrative:** Rule files and specifications should use a dry,
  neutral, institutional tone. Motivation and community docs may use a slightly
  more personal founding-motivation tone.

______________________________________________________________________

## 5. Citations and Evidence

Whenever you reference an external source, use the **Dual-Link Footnote System**
for inline convenience with academic rigor.

1. Include an inline Markdown link in the text.
1. Follow the inline link immediately with a GFM footnote marker `[^N]`.
1. Add a `## References` section at the bottom with the matching footnote.

Example:

```markdown
Adding "think step by step" ([Chain of Thought](https://arxiv.org/abs/2201.11903))[2]
was once the golden rule.

## References


Language Models," 2022. https://arxiv.org/abs/2201.11903
```

______________________________________________________________________

## 6. Markdown Formatting

- **Headings:** Use `#` for document title, `##` for major sections, `###`
  for subsections. Never skip a heading level.
- **Code blocks:** Always specify a language tag. Use ```` ```text ```` or
  ```` ```markdown ```` for prompt examples.
- **Lists over paragraphs:** When comparing three or more items, use a
  bulleted list.
- **Frontmatter:** Every document in this repository should begin with YAML
  frontmatter conforming to the project's frontmatter schema.

______________________________________________________________________

## 7. Security Reports

See [`SECURITY.md`](./SECURITY.md) for how to report security issues affecting
the repository, tooling, or the draft standard.

______________________________________________________________________

## 8. Agent and LLM Instructions

If you are an LLM or autonomous agent editing this repository, you MUST:

1. Follow the `P<FAMILY><AA><BB>` naming convention for rule identifiers.
1. Differentiate between syntactic formatting fixes (`AFX`) and semantic
   repairs that require LLM assistance (`LLM`).
1. Source rule rationale from recent academic papers or vendor docs and format
   citations using the Dual-Link System.
1. Use `docs/rules/rule-file.template.md` when creating or revising rule files.
1. Output raw, valid GitHub Flavored Markdown without markdownlint errors.
