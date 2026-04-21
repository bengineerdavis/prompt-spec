---
id: PF0102
title: Mixed line endings
description: Flags files that mix different newline styles (CRLF and LF) within the same governed document.
version: 0.1.0
type: rule
license: MIT
---

# PF0102 — Mixed line endings

## Summary

Governed prompt and documentation files MUST NOT mix different newline styles (for example, `CRLF` and `LF`) within the same file. Line endings should be normalized to a single, project-defined style.

## Why this rule exists

Mixed line endings are invisible in most editors but cause several practical problems:

- They produce noisy diffs, where lines appear changed solely because the newline representation changed.
- They can confuse tools that assume a consistent newline convention, including linters, formatters, and simple line-based processors.
- They increase the likelihood of merge conflicts when collaborators use different operating systems or editor defaults.

Because the choice of newline style is almost always a project-level decision, individual files should not diverge from the agreed convention. PF0102 enforces that consistency at the file level, making prompt repositories easier to maintain across platforms.

## Detection guidance

A linter or formatter SHOULD flag PF0102 when:

- A governed text file contains at least one `CRLF` (`\r\n`) newline, and
- The same file also contains at least one `LF`-only (`\n`) newline (or vice versa).

Signals:

- Scanning the raw bytes reveals both `\r\n` and bare `\n` sequences.
- A file opened on one platform shows “mixed line endings” warnings in the editor.

A straightforward detection approach:

1. Read the file as bytes.
2. Count occurrences of `\r\n` and `\n` that are not part of a `\r\n` pair.
3. If both counts are non-zero, flag PF0102.

Scope:

- **Include:** `.md`, `.mdx`, `.txt`, `.yml`, `.yaml`, and other governed prompt/config formats.
- **Exclude:** Binary files and any fixtures that intentionally exercise mixed line endings for testing.

This rule should generally be applied at the whole-file level (one diagnostic per file) rather than per-line.

## Repair guidance

The canonical repair is to convert all line endings in the file to the project’s chosen newline style, typically `LF` (`\n`).

Preferred repair pattern:

- Normalize every newline in the file to a single representation (for example, replace all `\r\n` with `\n`).
- Do not otherwise change the file content.

Automation:

- Configure editors or `.editorconfig` to enforce a consistent end-of-line style.
- Use a formatter or pre-commit hook to normalize line endings automatically.
- In CI, PF0102 can be treated as auto-fixable where tooling supports rewriting files.

Human review is usually **not** required, as the change is mechanical and should not affect how prompts render or how models interpret them.

## Examples

### Example 1 — Minimal mixed file

#### Non-compliant (conceptual)

```text
Line one\r\n
Line two\n
Line three\r\n
```

#### Compliant

```text
Line one\n
Line two\n
Line three\n
```

#### Why it breaks

The original file uses both `\r\n` and `\n` newlines. The repaired version normalizes all lines to the same style.

### Example 2 — Markdown prompt

#### Non-compliant (conceptual)

```md
# System\r\n
You are a helpful assistant.\n
\r\n
Follow the instructions carefully.\r\n
```

#### Compliant

```md
# System\n
You are a helpful assistant.\n
\n
Follow the instructions carefully.\n
```

#### Why it breaks

Mixing newline styles is invisible in rendered Markdown but produces inconsistent diffs and can confuse simple line-based tooling.

### Example 3 — YAML frontmatter

#### Non-compliant (conceptual)

```yaml
---\r\n
id: example-prompt\n
title: Example Prompt\r\n
version: 0.1.0\n
---\r\n
```

#### Compliant

```yaml
---\n
id: example-prompt\n
title: Example Prompt\n
version: 0.1.0\n
---\n
```

#### Why it breaks

The YAML content is correct, but the mix of `CRLF` and `LF` makes the file unstable across platforms. Normalizing the line endings preserves semantics while improving portability.

## Limitations

- This rule does not prescribe which newline style to use; it only requires consistency within each file. The project’s tooling or style guide should define the preferred newline convention.
- Some version-control systems or tools may normalize line endings automatically on checkout or commit, which can mask issues locally. PF0102 is most useful when run against the actual on-disk representation used in CI and builds.

## References

- Cross-platform version-control best practices that recommend normalizing line endings per project to avoid spurious diffs and merge conflicts.
- Editor and tooling conventions (for example, `.editorconfig`) that allow projects to specify a single `end_of_line` style for all text files.

## Notes

- PF0102 pairs naturally with PF0101 (trailing whitespace) as part of a “baseline hygiene” set for governed text files.
- In many repositories, both PF0101 and PF0102 can be enabled at the strictest linter level and treated as always safe for autofix.
