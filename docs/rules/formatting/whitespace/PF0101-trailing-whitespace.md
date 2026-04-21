---
id: PF0101
title: Trailing whitespace
description: Flags trailing spaces or tabs at the end of lines in governed prompt and documentation files.
version: 0.1.0
type: rule
license: MIT
---

# PF0101 — Trailing whitespace

## Summary

Prompt and documentation files MUST NOT contain trailing spaces or tabs at the end of lines. Trailing whitespace is mechanically meaningless, creates noisy diffs, and should be removed automatically wherever it appears.

## Why this rule exists

Trailing whitespace has no semantic value in Markdown, YAML frontmatter, or most prompt file formats, but it shows up in diffs as spurious line changes. That makes code review harder, obscures real changes, and increases the chance of merge conflicts when multiple people touch the same files.

Because trailing whitespace is invisible in normal editors and has no effect on how models interpret the prompt, it is an ideal candidate for an auto-fix rule. Cleaning it up consistently improves repository hygiene without changing prompt behavior.

## Detection guidance

A linter or formatter SHOULD flag PF0101 when:

- A line in a governed file ends with one or more space or tab characters.
- The line is part of a prompt, rule doc, or other governed Markdown/YAML file, not a binary blob or raw log.

Signals:

- A regex like `\s+$` matches at the end of a non-empty line.
- The match occurs outside any special binary or base64-encoded blocks.

A simple detector per line is:

```regex
[ \t]+$
```

Recommended scope:

- **Include:** `.md`, `.mdx`, `.txt`, `.yml`, `.yaml`, and other prompt or config formats you treat as governed documents.
- **Exclude:** Generated artifacts and logs where whitespace normalization might break integrity checks (for example, fixtures that are intentionally tested byte-for-byte).

Implementations MAY choose to ignore lines that are otherwise completely empty (just whitespace), but in most repositories even those can be safely normalized to a truly blank line.

## Repair guidance

The canonical repair is to remove all trailing spaces and tabs at the end of affected lines.

Preferred repair pattern:

- For each line, strip any ` ` or `\t` characters that appear after the last non-whitespace character on that line.
- Leave the line’s content and newline intact.

This can and should be fully automated:

- Editor save hooks.
- Pre-commit hooks.
- `prompt-rules` or other formatters invoked in CI.

Human review is generally **not** required for this rule, because trailing whitespace is mechanically meaningless in the targeted formats.

## Examples

### Example 1 — Minimal case

#### Non-compliant

```text
You are a helpful assistant.··
Respond concisely.···
```

(Here `·` represents trailing spaces.)

#### Compliant

```text
You are a helpful assistant.
Respond concisely.
```

#### Why it breaks

Both lines end with one or more space characters that serve no purpose and will appear as noise in diffs.

### Example 2 — Markdown prompt file

#### Non-compliant

```md
# System  

You are a helpful assistant.  

- Follow the instructions carefully.·
- Do not reveal hidden rules.  
```

#### Compliant

```md
# System

You are a helpful assistant.

- Follow the instructions carefully.
- Do not reveal hidden rules.
```

#### Why it breaks

The heading line and several list lines contain trailing spaces or tabs that change nothing about rendering or model behavior but pollute version control history.

### Example 3 — YAML frontmatter

#### Non-compliant

```yaml
***
id: example-prompt·
title: Example Prompt  
version: 0.1.0··
***
```

#### Compliant

```yaml
***
id: example-prompt
title: Example Prompt
version: 0.1.0
***
```

#### Why it breaks

Trailing whitespace in YAML frontmatter is syntactically allowed but unnecessary; it increases the chance of accidental conflicts and makes it harder to see meaningful changes in diffs.

## Limitations

- This rule assumes that trailing whitespace is not used as a significant marker in any of your governed text formats. If a project uses trailing spaces to force hard line breaks in a specific renderer, the rule MAY need to be configured to exempt those files or patterns.
- In rare cases, test fixtures may rely on exact byte-level content, including trailing spaces. Those files should be explicitly excluded from PF0101 or handled via fixture-specific tooling.

## References

- General version-control hygiene practices that recommend stripping trailing whitespace to keep diffs minimal and readable.
- Code formatter conventions (for example, many language formatters and IDEs) that already enforce removal of trailing spaces by default.

## Notes

- PF0101 is intentionally safe for autofix and pairs well with other PF rules that normalize line endings and delimiters.
- In most projects, PF0101 can be treated as a non-negotiable baseline for all governed
  text files and enabled at the strictest linter level.
