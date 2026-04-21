---
id: PF0201
title: Inconsistent delimiters or fences
description: Flags inconsistent use of equivalent delimiters or fences in governed prompts and documentation (for example, mixing different fence styles for the same purpose).
version: 0.1.0
type: rule
license: MIT
---

# PF0201 — Inconsistent delimiters or fences

## Summary

Within a governed file, equivalent structural delimiters and fences (such as code fences, section markers, or block delimiters) SHOULD be used consistently. Mixing multiple styles for the same purpose makes prompts harder to read, harder to lint, and more fragile for tooling.

## Why this rule exists

Prompts and docs rely on delimiters and fences to communicate structure both to humans and to tools: code fences for examples, markers for sections, and boundary tokens for input slots.[file:1188] When a file uses multiple syntactic styles for the same kind of boundary — for example, alternating between triple backticks and triple tildes for code blocks, or using several different marker styles for “BEGIN/END” sections — it becomes harder to see the intended structure at a glance.

Inconsistent delimiters:

- Make prompts more error-prone to edit, because the “house style” is unclear.
- Complicate parsing and linting, since tools must handle more variants.
- Increase the chance that a boundary is missed or malformed and that untrusted content bleeds into instructions.[file:1188]

PF0201 encourages a single, predictable delimiter style per structural role within a file so that both humans and tools can recognize boundaries reliably.

## Detection guidance

A linter SHOULD consider PF0201 when a single file:

- Uses more than one fence style for the same kind of block (for example, both ``` and ~~~ code fences for regular code examples).
- Uses multiple syntaxes for a clearly equivalent logical boundary (for example, mixing `---` and `***` as horizontal rule separators for the same purpose, or mixing `<<<` / `>>>` with `[[[` / `]]]` as slot markers).[1]

Signals to look for:

- More than one distinct code-fence opening token for code blocks that serve the same role (e.g., examples, tool outputs).
- Multiple “begin/end” marker patterns that appear to label similar sections (e.g., `BEGIN SYSTEM PROMPT` vs `START SYSTEM` vs `[[system]]` in the same file).
- Mixing of heading-like markers and ad-hoc delimiter lines for section boundaries where one approach would suffice.

Heuristics:

- Treat language-tagged vs. untagged code fences as the same style if their opening token is identical (e.g., ``` and ```json are both backtick fences).
- Distinguish between different structural roles when deciding if styles conflict. For example, it’s acceptable to use `---` for YAML frontmatter and ``` for code examples.

Implementations MAY:

- Configurably whitelist allowed delimiter styles per project.
- Only flag a file when a non-default or rare delimiter appears alongside a clearly preferred house style.

## Repair guidance

The canonical repair is to choose a single delimiter style for each structural role within the file and rewrite conflicting fences or markers to match that style.

Preferred repair patterns:

- Normalize all code examples to a single fence style (for example, triple backticks with language tags where appropriate).
- Normalize “BEGIN/END” markers for system prompts, user input slots, or tool outputs to a single pattern that aligns with the project’s guidance.
- Replace ad-hoc horizontal rules or separators with the standard Markdown or project-defined variant.

Automation:

- A formatter MAY rewrite fenced blocks from one style to another when it can confidently infer that the blocks serve the same role.
- Tools SHOULD be cautious about renaming free-form markers (like “BEGIN SYSTEM PROMPT”) unless the project defines canonical forms; those may require human review.

Human review is recommended when:

- The file mixes several custom markers and it is unclear which one should become canonical.
- Changing delimiters could affect downstream tooling that relies on exact strings (for example, regex-based slot extraction).

## Examples

### Example 1 — Mixed code fence styles

#### Non-compliant

```md
```json
{ "foo": "bar" }
```

~~~
SELECT * FROM users;
~~~
```

#### Compliant

```md
```json
{ "foo": "bar" }
```

```sql
SELECT * FROM users;
```
```

#### Why it breaks

Both blocks are regular code examples, but one uses backticks and the other uses tildes. Using a single fence style (backticks with language tags) improves readability and keeps tooling simpler.

### Example 2 — Mixed section delimiters

#### Non-compliant

```md
### System

You are a helpful assistant.

***

BEGIN SYSTEM PROMPT
You must follow all safety guidelines.
END SYSTEM PROMPT
```

#### Compliant

```md
### System

You are a helpful assistant.

### System safety

You must follow all safety guidelines.
```

(or, alternatively, consistently use the `BEGIN/END` style with no extra separator.)

#### Why it breaks

The file uses both a Markdown heading and an ad-hoc delimiter line plus a BEGIN/END block to mark sections that are conceptually similar. This makes the structure harder to follow and complicates any tooling that tries to detect system sections.

### Example 3 — Mixed slot markers

#### Non-compliant

```md
<user_input>
{{user_message}}
</user_input>

[[tool_output]]
{{tool_result}}
[[/tool_output]]
```

#### Compliant

```md
<user_input>
{{user_message}}
</user_input>

<tool_output>
{{tool_result}}
</tool_output>
```

#### Why it breaks

Two different marker syntaxes (`<...>` and `[[...]]`) are used for structurally equivalent slots. Normalizing to a single, agreed marker style improves clarity and makes it easier to reason about trust boundaries.

## Limitations

- PF0201 is deliberately qualitative: it does not attempt to fully interpret the semantics of every delimiter. Projects may need to configure which fences and markers are considered equivalent.
- Some files legitimately use different markers for different roles (for example, Markdown headings for human-readable sections and special tags for machine slots). PF0201 SHOULD only fire when multiple styles are used for the same role.
- Automated repairs that change custom marker strings can break downstream consumers that depend on exact text; such changes are best gated behind explicit project configuration or human review.

## References

- Prompt engineering guides that emphasize clear, consistent delimiters for instructions, context, and tool outputs to improve control and parseability.[file:1188]
- Markdown and documentation best practices that recommend standardizing on one code fence style and sectioning approach within a document.[file:1188]

## Notes

- PF0201 pairs naturally with `PX` boundary rules: once a project has chosen safe delimiter patterns for trust boundaries, PF0201 helps enforce consistent usage within individual files.
- In combination with PF0202 (chat citation artifacts), this rule helps keep governed Markdown free of both structural inconsistency and incidental tooling artifacts.
