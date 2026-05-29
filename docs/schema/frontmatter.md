---
id: frontmatter-spec
title: Prompt Spec Frontmatter Standard
description: Complete specification for prompt_spec YAML frontmatter structure, compliance states, and tool behavior
version: 0.4.0
type: schema
license: MIT
context:
  include: true
---

<!-- Above: YAML frontmatter used by tools. -->

# Prompt Spec Frontmatter Standard v0.4.0

## Overview

Every governed prompt file MUST include a `prompt_spec` block in YAML frontmatter (delimited by `---`). This block declares the spec version, compliance state, and optional audit trail.

Files without `prompt_spec` are treated as ungoverned. Tools SHOULD prompt users when encountering ungoverned files that may be prompts (see [Tool Workflow](#tool-workflow)).

______________________________________________________________________

## Structure

```yaml
---
prompt_spec:
  version: "0.4.0"

  compliance:
    status: pass           # pending | pass | fail | ignore
    exceptions:             # Optional. Human override.
      exempt: [PS001]       # Rule does not apply
      modified: []         # Rule was altered
      extended: []         # Additional constraints added
    reviewed:              # Optional. Human accountability override.
      by: "sarah-chen"     # REQUIRED if reviewed present
      at: "2026-05-07T00:00:00Z"  # Optional
      reason: "Legacy exception approved"  # Optional

  inspected:               # Optional. Tool-written. Last check result.
    tool: impromptu-check
    at: "2026-05-07T00:00:00Z"
    result: pass           # pass | fail | error
    issues: [PS001]        # REQUIRED if result=fail
    error: "..."           # REQUIRED if result=error

  fixed:                  # Optional. Tool-written. Last fix result.
    tool: impromptu-fix
    at: "2026-05-07T00:01:00Z"
    result: pass           # pass | error
    changes: [PS001]       # REQUIRED if result=pass
    error: "..."           # REQUIRED if result=error

  # Extension fields (tool-specific, non-standard)
  x-team: "platform"
  x-priority: 1
---
```

______________________________________________________________________

## Fields

### `prompt_spec.version` (required)

String. The version of this prompt spec standard the file conforms to. Use SemVer.

### `prompt_spec.compliance` (required)

#### `compliance.status` (required)

Enum: `pending`, `pass`, `fail`, `ignore`

| Status    | Meaning                                           | Source            |
| --------- | ------------------------------------------------- | ----------------- |
| `pending` | Never checked, or reset to unknown                | Derived (default) |
| `pass`    | Checked and passed, or human approved             | Derived or Human  |
| `fail`    | Check failed, fix failed, or fixed but unverified | Derived           |
| `ignore`  | Intentionally excluded from compliance            | **Human only**    |

**`ignore` is human-only.** Tools MUST NOT write `ignore`. It exists only when a human explicitly opts a file out of the compliance lifecycle. This is distinct from `.promptignore`, which is a silent, repo-local scan optimization with no frontmatter footprint.

#### `compliance.exceptions` (optional)

Human override declaring that certain rules don't apply or are modified.

```yaml
exceptions:
  exempt: [PS001, PS002]      # Rules that don't apply
  modified: [PS003]           # Rules that were altered
  extended: [PS004]            # Rules with additional constraints
```

If `exceptions` is present, `status` MUST be `pass` (the human has approved the deviation).

#### `compliance.reviewed` (optional)

Human accountability record for approving a file that would otherwise fail.

```yaml
reviewed:
  by: "sarah-chen"            # REQUIRED — who is accountable
  at: "2026-05-07T00:00:00Z"   # Optional
  reason: "Approved for prod"   # Optional
```

If `reviewed` is present, `status` MUST be `pass`.

### `prompt_spec.inspected` (optional, tool-written)

Result of the last check/inspection run on this file.

| Field    | Required          | Notes                         |
| -------- | ----------------- | ----------------------------- |
| `tool`   | Yes               | Tool name/identifier          |
| `at`     | Yes               | ISO 8601 timestamp            |
| `result` | Yes               | `pass`, `fail`, or `error`    |
| `issues` | If `result=fail`  | Rule IDs that failed          |
| `error`  | If `result=error` | Error message/tool crash info |

Tools MUST NOT write `inspected` if `.promptignore` matched the file.

### `prompt_spec.fixed` (optional, tool-written)

Result of the last fix/format attempt on this file.

| Field     | Required          | Notes                         |
| --------- | ----------------- | ----------------------------- |
| `tool`    | Yes               | Tool name/identifier          |
| `at`      | Yes               | ISO 8601 timestamp            |
| `result`  | Yes               | `pass` or `error`             |
| `changes` | If `result=pass`  | Rule IDs that were fixed      |
| `error`   | If `result=error` | Error message/tool crash info |

### Extension Fields

Any key starting with `x-` is allowed under `prompt_spec` and is not part of this standard. Tools MUST ignore unknown `x-*` fields.

______________________________________________________________________

## Status Derivation

`compliance.status` is derived from available data in this priority order:

| Priority | Condition                                         | Resulting Status |
| -------- | ------------------------------------------------- | ---------------- |
| 1        | `reviewed.by` present                             | `pass`           |
| 2        | `exceptions` present                              | `pass`           |
| 3        | Human manually set `ignore`                       | `ignore`         |
| 4        | Human manually set `pending`                      | `pending`        |
| 5        | `inspected.result=pass`                           | `pass`           |
| 6        | `inspected.result=fail/error` + any `fixed` state | `fail`           |
| 7        | No `inspected` data                               | `pending`        |

**Human overrides (1-4) always win over derived states (5-7).**

### Clearing Rules

When a tool writes `inspected.result: pass`, the `fixed` block SHOULD be cleared (it is no longer relevant — the file is verified clean).

| Tool writes | Result  | Clear `fixed`? |
| ----------- | ------- | -------------- |
| `inspected` | `pass`  | **Yes**        |
| `inspected` | `fail`  | No             |
| `inspected` | `error` | No             |
| `fixed`     | any     | No             |

______________________________________________________________________

## Tool Behavior

### What Tools Can Write

- `inspected` block — after any check
- `fixed` block — after any fix attempt
- `fixed` clearance — when `inspected.result: pass`

### What Tools Must NOT Write

- `compliance.status` — derived only
- `compliance.status: ignore` — human only
- `exceptions` — human only
- `reviewed` — human only

### `.promptignore` Interaction

`.promptignore` is a repo-level file that tells tools which paths to skip during scanning.

| File matches `.promptignore`? | Tool Action                                                           |
| ----------------------------- | --------------------------------------------------------------------- |
| Yes                           | **Skip entirely.** No frontmatter read. No frontmatter write. Silent. |
| No                            | Evaluate frontmatter normally                                         |

`.promptignore` NEVER writes to frontmatter. If a human wants a file marked as `ignore` in frontmatter, they must do it explicitly.

### Missing Frontmatter Workflow

When a tool encounters a file without frontmatter that is not in `.promptignore`:

**Interactive mode:**

```
File: docs/legacy-guide.md
No prompt_spec found. Not in .promptignore.

Is this a governed prompt file?
[Y] Yes → Write minimal frontmatter (status: pending)
[N] No  → Add to .promptignore
[S] Skip this time only
```

**Non-interactive mode (CI):**
Tools SHOULD use heuristics (directory, extension, content patterns) to decide. Default to `skip` or `not a prompt`.

______________________________________________________________________

## Manifest Offload (Deferred)

For v0.4.0, frontmatter is the **only** canonical location for `inspected` and `fixed` data.

Manifest offload (aggregating audit data into a separate file) MAY be added in v0.5.0+ as an **optional, derived, `.gitignore`d cache**. If added:

- Frontmatter remains canonical
- One manifest MAY cover multiple prompts (repo-level or directory-level)
- Tools regenerate manifest from frontmatter on demand
- If manifest and frontmatter disagree, **frontmatter wins**

______________________________________________________________________

## Examples

### Never Checked

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pending
```

### Check Passed

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pass
  inspected:
    tool: impromptu-check
    at: "2026-05-07T00:00:00Z"
    result: pass
```

### Check Failed

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: fail
  inspected:
    tool: impromptu-check
    at: "2026-05-07T00:00:00Z"
    result: fail
    issues: [PS001]
```

### Fixed, Not Re-checked

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: fail
  inspected:
    tool: impromptu-check
    at: "2026-05-07T00:00:00Z"
    result: fail
    issues: [PS001]
  fixed:
    tool: impromptu-fix
    at: "2026-05-07T00:01:00Z"
    result: pass
    changes: [PS001]
```

### Human Exceptions

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pass
    exceptions:
      exempt: [PS001]
    reviewed:
      by: "sarah-chen"
      at: "2026-05-07T00:00:00Z"
      reason: "PS001 not applicable for context files"
  inspected:
    tool: impromptu-check
    at: "2026-05-07T00:00:00Z"
    result: fail
    issues: [PS001]
```

### Intentionally Ignored

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: ignore
```

______________________________________________________________________

## Version History

| Version | Date       | Changes                                                                                                                                       |
| ------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.4.0   | 2026-05-07 | Refactored compliance model: `pending`, `pass`, `fail`, `ignore`; introduced `inspected`/`fixed` blocks; dropped `level`; human-only `ignore` |
