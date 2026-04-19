---
id: frontmatter-extensions
title: Frontmatter Extensions
description: Rules for non-standard x-prefixed frontmatter metadata.
version: 0.1.0
type: documentation
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Frontmatter Extensions

The core frontmatter schema is intentionally small. To allow experimentation without changing the standard, projects and tools may define custom top-level metadata keys using the `x-` prefix.

## Rule

Any top-level key beginning with `x-` is an extension key.

Examples:

```yaml
x-impromptu:
  indexed: true
  severity: high

x-mytool:
  owner: team-ai
```

## Requirements

- Extension keys MUST begin with `x-`.
- Extension keys MUST appear after all standard fields in the frontmatter block.
- Tools that do not recognize a given extension key MUST ignore it.
- Extension keys are not part of the core standard unless later adopted explicitly.

## Why this exists

This gives tools and projects room to evolve without bloating the standard. It also allows the core schema to remain strict: if a key is not part of the standard and does not begin with `x-`, it is invalid.
