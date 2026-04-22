---
id: PF0202
title: Chat citation artifacts
description: Flags chat/tool citation tokens like `[web:1073]` or `[file:1042]` that leak interface artifacts into governed Markdown prose.
version: 0.1.0
type: rule
license: MIT
---

# PF0202 — Chat citation artifacts

## Summary

Governed Markdown files MUST NOT contain chat or tool citation tokens such as `[web:1073]` or `[file:1042]` in normal prose. These tokens are interface artifacts, not stable citations, and should be removed or converted to real references.

## Why this rule exists

Chat systems and tooling often emit inline markers like `[web:3]` or `[file:12]` to tie sentences back to sources during a session.[file:1191] They are meaningful inside that UI, but once committed to a repository they read as broken links or unexplained codes. They also leak details of the tooling stack into canonical documents in a way that is not portable across tools or sessions.[file:1191]

Prompts, docs, and other governed files should be self-contained and readable in any Markdown renderer. If a citation matters, it should be expressed as a proper link, a human-readable reference, or project-specific metadata — not an opaque `[source_type:number]` token that only one tool understands.[file:1191]

## Detection guidance

A linter SHOULD flag this rule when all of the following are true:[file:1191]

- The file is a governed Markdown document (for example, `.md`, `.mdx`) rather than raw logs.
- Outside of code fences and inline code spans, the text contains tokens of the form `[source_type:number]`.
- `source_type` matches a known chat/tool citation prefix such as `web`, `file`, `cite`, `page`, `chart`, `code_file`, `generated_image`, `generated_video`, `calendar_event`, or `email`.[file:1191]

A recommended default detector is:

```regex
\[(cite|web|page|file|chart|code_file|generated_image|generated_video|calendar_event|email):[0-9]+\](?!\()
```

Signals to look for:

- A bare `[word:number]` token at the end of a sentence or paragraph.
- Multiple such tokens clustered near sentences that clearly reference tooling or past outputs.
- No corresponding Markdown link target `(...)` immediately following the closing `]`.[file:1191]

Projects that prefer a broader detector MAY use:

```regex
\[[a-z_]+:[0-9]+\](?!\()
```

but SHOULD ensure this does not conflict with any intentional `[word:number]` syntax used in their own docs.[file:1191]

Implementations SHOULD ignore:

- Any occurrence inside fenced code blocks.
- Any occurrence inside backtick-delimited inline code.
- Examples that clearly show these tokens as literals in documentation about this rule.[file:1191]

## Repair guidance

The canonical repair is to remove the artifact or replace it with a stable reference that makes sense outside the originating tool.[file:1191]

Preferred repair patterns:

- **Drop the token** when no citation is actually required.

  - `The frontmatter schema is documented here.[file:1042]`\
    → `The frontmatter schema is documented here.`[file:1191]

- **Replace with a real Markdown link** when you have a meaningful target.

  - `The frontmatter schema is documented here.[file:1042]`\
    → `The frontmatter schema is documented in [Frontmatter Schema](./frontmatter.md).`[file:1191]

- **Replace with a project-specific reference format** if your repo uses a different citation system (for example, a numbered reference list or footnote syntax).

Automation guidance:

- A formatter MAY safely delete `[source_type:number]` when it appears at the end of a sentence or paragraph and is not part of a known project-local syntax.
- Tools SHOULD be conservative about editing tokens in the middle of dense inline content; those cases are better surfaced for human review.[file:1191]

Human review is required when:

- The artifact appears next to ambiguous phrases like “see above” or “previous answer,” where a real citation might be needed.
- The project uses its own `[word:number]` convention and the tool cannot distinguish between house style and chat artifacts.

## Examples

### Example 1 — Minimal case

#### Non-compliant

```md
The frontmatter schema is documented here.[file:1042]
```

#### Compliant

```md
The frontmatter schema is documented here.
```

#### Why it breaks

The `[file:1042]` token is a chat/tool artifact that does not point to a real resource in the repository and is not meaningful in a normal Markdown renderer.[file:1191]

### Example 2 — Repaired with a real link

#### Non-compliant

```md
The frontmatter schema is documented here.[file:1042]
```

#### Compliant

```md
The frontmatter schema is documented in
[Frontmatter Schema](./frontmatter.md).
```

#### Why it breaks

The original uses `[file:1042]` as a pseudo-citation that only a specific tool understands. Replacing it with a direct link makes the reference stable and portable.[file:1191]

### Example 3 — Realistic template case

#### Non-compliant

```md
Dotprompt appears to be a strong interoperability target.[]
```

#### Compliant

```md
Dotprompt appears to be a strong interoperability target.
```

#### Why it breaks

The `[web:1073]` token reveals that this sentence was copied from a chat transcript and leaks an internal citation index that has no meaning in the repository.[file:1191]

### Example 4 — Edge case (example in docs)

#### Non-compliant

```md
The checker should flag `[web:1073]` when it appears in normal prose.
```

#### Compliant

```md
This rule flags chat-style citation tokens like `[web:1073]` and `[file:1042]`
when they appear in normal prose.
```

#### Why it is allowed

Here the tokens appear inside backticks, clearly as literal examples in documentation about the rule itself, and SHOULD NOT trigger PF0202.[file:1191]

## Limitations

- This rule assumes that `[source_type:number]` patterns are not part of a project’s intentional house style. Projects that use similar syntax must configure the detector more narrowly.
- Automated fixes cannot infer correct external URLs or internal cross-links; they can safely remove artifacts but may require human input to add real citations.
- Tokens embedded in complex inline formatting may require human review to avoid accidentally altering intended code or pseudo-code examples.[file:1191]

## References

- Internal implementation experience from tools that emit inline source markers like `[web:3]` or `[file:12]` during chat sessions.[file:1191]
- General documentation hygiene practices that discourage leaking environment-specific artifacts into versioned Markdown.[file:1191]

## Notes

- This rule pairs well with formatting rules that normalize whitespace and delimiters, since PF0202 assumes the surrounding Markdown is otherwise well-formed.
- In repositories that ingest chat transcripts as raw logs, PF0202 SHOULD only apply to
  governed docs and prompts, not to log archives stored for audit or
  reproductions.[file:1191]
