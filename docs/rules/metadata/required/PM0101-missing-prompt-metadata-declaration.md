---
id: PM0101
title: Missing prompt metadata declaration
description: Flags governed prompt files that lack a required YAML frontmatter block declaring the file's identity and ownership metadata.
version: 0.1.0
type: rule
license: MIT
---

# PM0101 — Missing prompt metadata declaration

## Summary

Governed prompt files MUST include a YAML frontmatter block declaring at minimum the required identity and ownership fields. A file without this block cannot be reliably identified, versioned, or governed by tooling.

## Why this rule exists

Once prompts are treated as managed artifacts — checked into repositories, shared across teams, versioned, and routed through CI — the absence of metadata becomes a coordination problem. Without a frontmatter block, tools cannot determine who owns a file, what version it is, or whether it is in scope for linting and governance.

The minimal required fields are:

- `id` — a stable, machine-readable identifier for the prompt.
- `title` — a human-readable name.
- `description` — a one-sentence summary of what the prompt does.
- `version` — a version string (for example, `0.1.0`).
- `license` — the license governing use and distribution.

These fields are author-owned and should be set at file creation time. Optional fields such as `type` and `context.include` may be inferred or enforced by tooling and do not need to be present for PM0101 to pass.

## Detection guidance

A linter SHOULD flag PM0101 when:

- A governed Markdown file does not begin with a YAML frontmatter block delimited by `---`.
- A YAML frontmatter block is present but is missing one or more of the required fields: `id`, `title`, `description`, `version`, `license`.

Signals:

- The file begins with a Markdown heading or plain prose rather than `---`.
- A frontmatter block is present but one or more required fields are absent or empty.
- A frontmatter block is present but the opening `---` is not on the very first line of the file.

Scope:

- Apply to files explicitly designated as governed prompts (for example, by path convention, extension, or project configuration).
- Do not apply to raw log files, generated artifacts, or files explicitly excluded from governance.

This rule fires once per file (not per missing field). A separate, more granular rule MAY cover individual missing fields in a future revision.

## Repair guidance

The canonical repair is to add a well-formed YAML frontmatter block at the top of the file with all required fields populated.

Preferred repair pattern:

```md
***
id: your-prompt-id
title: Your Prompt Title
description: One-sentence description of what this prompt does.
version: 0.1.0
license: MIT
***
```

Guidelines:

- `id` should be lowercase, hyphenated, and stable across renames (it is the file's canonical machine identity).
- `version` should follow semantic versioning conventions (`MAJOR.MINOR.PATCH`).
- `description` should be a single sentence, written for a reader who has not seen the prompt before.
- `license` should be an SPDX identifier (for example, `MIT`, `Apache-2.0`) or a project-specific license token.

Automation:

- A formatter MAY scaffold a stub frontmatter block with placeholder values when the block is missing entirely.
- Tools SHOULD require human review to fill in actual values, since `id`, `title`, and `description` require authorial judgment.

## Examples

### Example 1 — Completely missing frontmatter

#### Non-compliant

```md
# Customer Support Prompt

You are a helpful customer support assistant for Acme Corp.
Always be polite and escalate unresolved issues to a human agent.
```

#### Compliant

```md
---
id: acme-customer-support
title: Acme Customer Support Prompt
description: Guides the model to act as a polite customer support agent for Acme Corp, with escalation rules.
version: 0.1.0
license: MIT
---


# Customer Support Prompt

You are a helpful customer support assistant for Acme Corp.
Always be polite and escalate unresolved issues to a human agent.
```

#### Why it breaks

The non-compliant version has no frontmatter at all. Without `id`, `title`, `description`, `version`, and `license`, the file cannot be tracked, versioned, or governed by tooling.

### Example 2 — Partial frontmatter

#### Non-compliant

```md
---
title: Summarization Prompt
version: 0.2.0
---

Summarize the following text in three sentences or fewer.
```

#### Compliant

```md
---
id: summarization-prompt
title: Summarization Prompt
description: Instructs the model to summarize a given text in three sentences or fewer.
version: 0.2.0
license: MIT
---

Summarize the following text in three sentences or fewer.
```

#### Why it breaks

The frontmatter block exists but is missing `id`, `description`, and `license`. The file cannot be reliably identified by tooling (no stable `id`) or distributed safely (no `license`).

### Example 3 — Frontmatter not at the top

#### Non-compliant

```md
# Onboarding Prompt

This prompt is used during user onboarding.

---
id: onboarding-prompt
title: Onboarding Prompt
description: Guides new users through the onboarding flow.
version: 0.1.0
license: MIT
---
```

#### Compliant

```md
---
id: onboarding-prompt
title: Onboarding Prompt
description: Guides new users through the onboarding flow.
version: 0.1.0
license: MIT
---

# Onboarding Prompt

This prompt is used during user onboarding.
```

#### Why it breaks

The frontmatter block must appear at the very beginning of the file (before any other content) to be recognized by standard frontmatter parsers.

## Limitations

- PM0101 does not validate the *quality* of field values — only their presence. A rule such as `PM0201` or a future `PM` rule may address empty, placeholder, or low-quality values.
- Projects that use non-YAML frontmatter formats (for example, TOML with `+++` delimiters) should adapt the detection to their chosen format; the spirit of the rule remains the same.
- Tooling that scaffolds stub frontmatter should not mark the rule as passing until real values have been supplied; placeholder text such as `id: your-prompt-id` should be treated as incomplete.

## References

- Prompt Rules Standard frontmatter specification: [`docs/schema/frontmatter.md`](../../schema/frontmatter.md).
- JSON Schema for frontmatter validation: [`schema/frontmatter.schema.json`](../../../schema/frontmatter.schema.json).
- General documentation governance practices that require identity and ownership metadata on all managed artifacts.

## Notes

- PM0101 is the entry-point rule for the `PM` family. It establishes the baseline expectation that every governed file declares itself before any other metadata rules apply.
- In repositories that enforce governance via path conventions (for example, all files under `prompts/` are governed), PM0101 can be applied broadly as a pre-commit check with minimal false positives.
