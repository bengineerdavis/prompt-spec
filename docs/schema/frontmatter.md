---
id: frontmatter-schema
title: Frontmatter Schema
description: Human-readable specification for the Prompt Rules Standard YAML frontmatter.
version: 0.1.0
type: schema
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Frontmatter Schema

This document defines the minimal YAML frontmatter required for governed files in the Prompt Rules Standard.

## Purpose

The frontmatter schema is intentionally minimal. It exists to make rule files, documentation files, and other governed artifacts portable, machine-readable, and reusable outside their original repository context.

This Markdown document is the human-readable normative specification. Tools SHOULD parse a file's YAML frontmatter and validate the resulting data against the published JSON Schema in `schema/frontmatter.schema.json`.

## Required fields

The following top-level fields are required in every governed file:

| Field | Type | Required | Meaning |
|---|---|---|---|
| `id` | string | Yes | A unique, URL-safe identifier for the file. |
| `title` | string | Yes | A human-readable title. |
| `description` | string | Yes | A concise summary of the file's purpose. |
| `version` | string | Yes | Semantic version in `MAJOR.MINOR.PATCH` format. |
| `type` | string | Yes | The file category. Allowed values: `documentation`, `rule`, `schema`, `index`, `roadmap`. |
| `license` | string | Yes | The reuse license for the file. An SPDX identifier SHOULD be used when possible. |

## Optional fields

The following top-level fields are optional:

| Field | Type | Required | Meaning |
|---|---|---|---|
| `context.include` | boolean | No | Whether the file is included in machine context. If omitted, files are included unless excluded by repository-level ignore rules. |

## Type vocabulary

The `type` field is a closed vocabulary in the core standard.

Allowed values are:

- `documentation`
- `rule`
- `schema`
- `index`
- `roadmap`

Projects that need finer classification SHOULD keep `type` within the standard vocabulary and use an `x-*` extension key for subtyping or tool-specific routing.

Example:

```yaml
type: documentation
x-impromptu-subtype: tutorial
```

## Extension fields

Any top-level key beginning with `x-` is reserved for non-standard, project- or tool-specific metadata.

Tools that do not recognize an `x-*` key MUST ignore it.

Unknown top-level keys that do not begin with `x-` are invalid.

## Field order

Standard frontmatter keys MUST appear before any extension keys.

The recommended order is:

1. `id`
2. `title`
3. `description`
4. `version`
5. `type`
6. `license`
7. `context`
8. any `x-*` keys

JSON Schema does not reliably enforce key order, so this requirement must be enforced by a linter or other custom validator rather than by the JSON Schema alone.

## Example

```yaml
---
id: readme
title: Prompt Rules Standard
description: Overview of the Prompt Rules Standard and its purpose.
version: 0.1.0
type: documentation
license: MIT
context:
  include: true
x-impromptu:
  indexed: true
---
```
