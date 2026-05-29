---
id: migration-guide-v03-to-v04
title: Migration Guide from v0.3.x to v0.4.0
description: Step-by-step migration mapping for frontmatter compliance status refactor from v0.3.x to v0.4.0
version: 0.4.0
type: documentation
license: MIT
context:
  include: true
---

# Migration Guide: Frontmatter Compliance to v0.4.0

## What Changed

| v0.3.x                           | v0.4.0                               | Change                |
| -------------------------------- | ------------------------------------ | --------------------- |
| `compliance_level`               | `compliance`                         | Renamed               |
| `level: simple/full`             | **Dropped**                          | YAGNI                 |
| `status: compliant`              | `status: pass`                       | Renamed               |
| `status: partial`                | `status: fail` + `exceptions`        | Refactored            |
| `status: exempt`                 | `status: pass` + `exceptions.exempt` | Refactored            |
| `status: unstable`               | `status: fail`                       | Simplified            |
| `status: draft`                  | `status: pending`                    | Simplified            |
| `status: ignored`                | `status: ignore`                     | Human-only now        |
| `checked_at`                     | `inspected.at`                       | Moved into tool block |
| `checked_by`                     | `inspected.tool`                     | Moved into tool block |
| `formatted`/`fixed` single block | `inspected` + `fixed` separate       | Two-stage audit       |

______________________________________________________________________

## Migration Mapping

### Case 1: Compliant File

**v0.3.x:**

```yaml
compliance_level:
  status: compliant
  checked_at: "2026-04-20T09:00:00Z"
  checked_by: impromptu-check
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pass
  inspected:
    tool: impromptu-check
    at: "2026-04-20T09:00:00Z"
    result: pass
```

______________________________________________________________________

### Case 2: Partial Compliance

**v0.3.x:**

```yaml
compliance_level:
  status: partial
  exempt: [PS001]
  modified: [PS002]
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pass
    exceptions:
      exempt: [PS001]
      modified: [PS002]
    reviewed:
      by: "migration-script"
      at: "2026-05-07T00:00:00Z"
      reason: "Migrated from v0.3.x partial status"
  inspected:
    tool: impromptu-check
    at: "2026-04-20T09:00:00Z"
    result: fail
    issues: [PS001, PS002]
```

______________________________________________________________________

### Case 3: Exempt File

**v0.3.x:**

```yaml
compliance_level:
  status: exempt
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pass
    exceptions:
      exempt: [PS001]
    reviewed:
      by: "migration-script"
      at: "2026-05-07T00:00:00Z"
      reason: "Migrated from v0.3.x exempt status"
```

**Note:** The migration script must infer which rules were exempt. If unknown, use `exempt: ["unknown"]` and flag for human review.

______________________________________________________________________

### Case 4: Ignored File

**v0.3.x:**

```yaml
compliance_level:
  status: ignored
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: ignore
```

**Important:** In v0.4.0, `ignore` is human-only. If the v0.3.x `ignored` was set by a tool (e.g., matching `.promptignore`), the migration script should consider:

- If `.promptignore` still matches → keep `ignore` (human intent inferred)
- If no `.promptignore` match → consider `pending` (the file may need checking)

______________________________________________________________________

### Case 5: Draft/Unstable File

**v0.3.x:**

```yaml
compliance_level:
  status: draft
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pending
```

**Rationale:** `draft` and `unstable` were ambiguous. In v0.4.0, if the file hasn't been checked, it's `pending`. If it has been checked and failed, it's `fail`.

______________________________________________________________________

### Case 6: File with Fixed Status

**v0.3.x:**

```yaml
compliance_level:
  status: partial
  fixed:
    tool: impromptu-fix
    at: "2026-04-20T09:01:00Z"
    changes: [PS001]
```

**v0.4.0:**

```yaml
prompt_spec:
  version: "0.4.0"
  compliance:
    status: fail
  inspected:
    tool: impromptu-check
    at: "2026-04-20T09:00:00Z"
    result: fail
    issues: [PS001]
  fixed:
    tool: impromptu-fix
    at: "2026-04-20T09:01:00Z"
    result: pass
    changes: [PS001]
```

______________________________________________________________________

## Migration Script Logic

```python
def migrate_v03_to_v04(frontmatter: dict) -> dict:
    v03 = frontmatter.get("compliance_level", {})
    status = v03.get("status", "pending")

    new = {
        "prompt_spec": {
            "version": "0.4.0",
            "compliance": {"status": "pending"}
        }
    }

    # Map status
    if status == "compliant":
        new["prompt_spec"]["compliance"]["status"] = "pass"
    elif status in ("partial", "unstable", "draft"):
        new["prompt_spec"]["compliance"]["status"] = "pending"
    elif status == "exempt":
        new["prompt_spec"]["compliance"]["status"] = "pass"
        new["prompt_spec"]["compliance"]["exceptions"] = {
            "exempt": ["unknown"]
        }
        new["prompt_spec"]["compliance"]["reviewed"] = {
            "by": "migration-script",
            "reason": "Migrated from v0.3.x exempt"
        }
    elif status == "ignored":
        new["prompt_spec"]["compliance"]["status"] = "ignore"

    # Migrate checked_at/checked_by to inspected
    if "checked_at" in v03:
        new["prompt_spec"]["inspected"] = {
            "tool": v03.get("checked_by", "unknown"),
            "at": v03["checked_at"],
            "result": "pass" if status == "compliant" else "fail"
        }
        if status != "compliant":
            new["prompt_spec"]["inspected"]["issues"] = ["unknown"]

    # Migrate fixed block
    if "fixed" in v03:
        new["prompt_spec"]["fixed"] = {
            "tool": v03["fixed"].get("tool", "unknown"),
            "at": v03["fixed"]["at"],
            "result": "pass",
            "changes": v03["fixed"].get("changes", [])
        }

    # Migrate exceptions
    if "exempt" in v03 or "modified" in v03 or "extended" in v03:
        new["prompt_spec"]["compliance"]["exceptions"] = {
            "exempt": v03.get("exempt", []),
            "modified": v03.get("modified", []),
            "extended": v03.get("extended", [])
        }

    return new
```

______________________________________________________________________

## Post-Migration Steps

1. **Review all `ignore` files** — Verify they should remain ignored, or reset to `pending`
1. **Review all `exempt` → `pass` with exceptions** — Confirm the exempted rules are correct
1. **Run `impromptu-check` on all `pending` files** — Establish baseline `inspected` data
1. **Commit `.promptignore`** if using repo-level exclusions — Document which files are silently skipped
