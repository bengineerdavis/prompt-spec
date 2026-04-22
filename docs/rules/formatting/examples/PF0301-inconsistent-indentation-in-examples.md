---
id: PF0301
title: Inconsistent indentation in examples
description: Flags inconsistent indentation within example blocks that makes prompts harder to read and more fragile for tooling.
version: 0.1.0
type: rule
license: MIT
---

# PF0301 — Inconsistent indentation in examples

## Summary

Example blocks in governed prompts and documentation SHOULD use consistent indentation. Large, unjustified shifts in indentation inside examples make them harder to read, harder to copy and adapt, and more brittle for tools that rely on indentation to infer structure.

## Why this rule exists

Examples are often copied directly into production prompts or used as templates for new prompts. When indentation inside examples is inconsistent — for instance, mixing two spaces, four spaces, and tabs for the same nesting level — it becomes difficult to see the underlying structure at a glance.

This matters because:

- Prompt authors are more likely to introduce errors when adapting poorly formatted examples.
- Some tools or languages are indentation-sensitive; incorrect or inconsistent indentation can break them outright.
- Even when indentation is not semantically significant, inconsistent style clutters diffs and makes it harder to spot true changes.

PF0301 encourages examples that are visually and structurally stable, matching the project’s expected indentation style.

## Detection guidance

A linter SHOULD consider PF0301 when:

- Within a single example block (code fence, indented block, or structured sample), lines that represent the same logical nesting level use different indentation widths or a mix of spaces and tabs.
- The example clearly appears to be a template or snippet meant for reuse (for example, YAML, JSON, code-like configuration, or structured Markdown).

Signals:

- Mixed tabs and spaces at the left margin of example lines.
- The same syntactic construct (e.g., sibling list items, sibling keys in a mapping) appearing at different indentation depths without a structural reason.
- Large “jumps” in indentation width inside a block that otherwise appears hierarchical.

Heuristics:

- Treat fenced code blocks (`…` or ~~~…~~~) and indented code blocks as the primary scope.
- Prefer flagging obvious local inconsistencies (e.g., in a small YAML or JSON example) over attempting to infer global indentation preferences across the whole file.
- Allow special cases where indentation is intentionally irregular to illustrate an anti-example; these can be suppressed via project-local mechanisms if needed.

## Repair guidance

The canonical repair is to normalize indentation inside each example block to the project’s agreed indentation style (for example, two spaces per level, no tabs).

Preferred repair patterns:

- Use spaces only, with a consistent number of spaces per logical level (e.g., 2 or 4).
- Align sibling keys, list items, or code constructs at the same indentation depth.
- Avoid mixing tabs and spaces in the same example.

Automation:

- A formatter MAY re-indent well-formed YAML/JSON/code blocks according to the project’s rules.
- For free-form examples or pseudocode, tools should be conservative and may limit themselves to flagging issues for human review.

Human review is recommended when:

- It is not clear what the intended structure is (for example, the example is pedagogical or intentionally malformed).
- The example contains language-specific formatting conventions that generic tooling might not understand.

## Examples

### Example 1 — YAML example with inconsistent indentation

#### Non-compliant

```yaml
system:
  role: assistant
    description: Helpful assistant for customer support
  behavior:
    - follow_sop: true
      escalate_on_uncertainty: true
```

#### Compliant

```yaml
system:
  role: assistant
  description: Helpful assistant for customer support
  behavior:
    - follow_sop: true
      escalate_on_uncertainty: true
```

#### Why it breaks

The `description` key is indented more deeply than its sibling `role` without a structural reason, making the mapping harder to read and potentially confusing tools that assume consistent indentation.

### Example 2 — JSON-like example

#### Non-compliant

```json
{
  "role": "assistant",
    "task": "Summarize the following text.",
  "constraints": {
      "max_words": 200,
    "language": "en"
  }
}
```

#### Compliant

```json
{
  "role": "assistant",
  "task": "Summarize the following text.",
  "constraints": {
    "max_words": 200,
    "language": "en"
  }
}
```

#### Why it breaks

Indentation in the non-compliant example is inconsistent between sibling keys and within the `constraints` object, making the structure less clear and more error-prone to copy.

### Example 3 — Markdown list-based example

#### Non-compliant

```md
- Step 1: Collect input
    - Ask for name
  - Ask for email
 - Step 2: Confirm details
```

#### Compliant

```md
- Step 1: Collect input
  - Ask for name
  - Ask for email
- Step 2: Confirm details
```

#### Why it breaks

List items at the same logical level use different indentation widths, making the hierarchy ambiguous and the example harder to adapt.

## Limitations

- PF0301 does not attempt to fully parse all languages; it relies on heuristics about indentation patterns within examples.
- Some examples intentionally show “bad” indentation as a teaching tool; those should be handled with local overrides if they conflict with this rule.
- In free-form prose or mixed-content blocks, indentation may be used artistically; PF0301 is primarily aimed at code-like or structured examples.

## References

- Documentation and coding style guides that recommend consistent indentation for readability and maintainability.[file:1188]
- General prompt-structuring guidance that relies on clearly indented examples to communicate structure to both humans and tools.[file:1188]

## Notes

- PF0301 complements PF0101 and PF0102, giving you a complete baseline for whitespace and indentation hygiene in governed text files.
- For languages where indentation is semantically significant (for example, Python), PF0301 can help catch errors early when examples are likely to be copied into real code.
