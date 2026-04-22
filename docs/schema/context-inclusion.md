---
id: context-inclusion
title: Context Inclusion
description: How files declare whether they are part of machine context, and how tools should resolve inclusion.
version: 0.1.0
type: schema
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Context Inclusion

This document specifies how governed files signal machine-context intent and how
tools should resolve that signal.

> **Related:**
> [Frontmatter Schema](./frontmatter.md) —
> [Extension Fields](./extensions.md) —
> [Security Intake](./security-intake.md) —
> [Document-Driven Design](../philosophy/document-driven-design.md)

## What "machine context" means

In this standard, *machine context* refers to the set of files a tool is
permitted to read as input — for RAG retrieval, agent ingestion, indexing,
or any other automated consumption.

Context inclusion is a **signal**, not a security boundary. A file marked
`context.include: false` is telling well-behaved tools to skip it. It is not
a guarantee of confidentiality. Access control is the responsibility of the
hosting environment, not this standard.

## Default behavior

Files are **included by default**. A tool walking a repository should treat
any governed file without an explicit exclusion as fair game for machine
context, unless the tool's own scan boundaries exclude it first.

## Two layers of control

Inclusion is resolved in two passes, coarse before fine:

### 1. `.promptignore` (repo-level, coarse)

An optional file at the repository root. Uses the same glob syntax as
`.gitignore`. Paths matched by `.promptignore` are **skipped entirely** —
their frontmatter is not read.

Use `.promptignore` for:

- Generated or vendored directories (`dist/`, `node_modules/`, `coverage/`)
- Third-party files you do not own
- Bulk exclusion of a directory without touching every file inside it
  (`archive/**`, `drafts/**`)

`.promptignore` is optional. If it does not exist, no paths are pre-filtered.

Example `.promptignore`:

```sh
# Generated output

dist/**
coverage/**

# Vendored and third-party

node_modules/**
vendor/**

# Human-only archives

archive/**
docs/legal/**
```

### 2. `context.include` (per-file, fine-grained)

An optional boolean field in a file's YAML frontmatter. Use this when you
own the file and want to be explicit about its intent.

```yaml
***
id: meeting-notes-2026-04
title: April 2026 Meeting Notes
description: Internal team notes, not intended for agent context.
version: 0.1.0
type: documentation
license: MIT
context:
  include: false
***
```

If `context.include` is omitted, the file is treated as included (subject to
`.promptignore` having already passed it through).

## Precedence

| Condition                                       | Result                                   |
| ----------------------------------------------- | ---------------------------------------- |
| Path matches `.promptignore`                    | Excluded — frontmatter not read          |
| `context.include: false` in frontmatter         | Excluded                                 |
| `context.include: true` in frontmatter          | Included                                 |
| `context` key absent from frontmatter           | Included (default)                       |
| File has no frontmatter, not in `.promptignore` | Tool-defined behavior; recommended: skip |

## What tools should do

Tools conforming to this standard SHOULD:

1. Read `.promptignore` at the repository root before walking files.
1. Skip any path matched by a `.promptignore` pattern.
1. For remaining files, read frontmatter and check `context.include`.
1. Treat absence of `context.include` as `true`.
1. Document any deviation from this behavior.

Tools MUST NOT treat `context.include: false` as a security or access-control
guarantee.

## What this does not cover

- **Access control** — use your hosting environment's permissions model.
- **Secret detection** — use a dedicated tool (e.g. `gitleaks`, `truffleHog`).
- **PII scanning** — out of scope; use a dedicated tool.
- **Rule linting scope** — tools may define separate scan boundaries for which
  files to lint vs. which to include in context. Those are independent concerns.
