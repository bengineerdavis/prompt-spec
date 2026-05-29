---
id: tool-workflow-guide
title: Tool Workflow Guide
description: Recommended patterns for tool implementers — file scanning, frontmatter handling, batch operations, and CI integration
version: 0.4.0
type: documentation
license: MIT
context:
  include: true
---

# Tool Workflow Guide

## Overview

This guide describes how tools (e.g., `impromptu-check`, `impromptu-fix`) interact with prompt frontmatter. It is not part of the spec standard, but provides recommended patterns for tool implementers.

______________________________________________________________________

## File Scanning Workflow

```
1. Walk repo directories
2. For each file:
   a. Check .promptignore patterns
      - Match? → SKIP. No frontmatter read. No write.
   b. Check for frontmatter
      - No frontmatter? → Handle ungoverned file (see below)
   c. Parse prompt_spec
      - Invalid? → Report error, skip
   d. Evaluate compliance status
      - Derive from inspected/fixed/human overrides
   e. Run checks/fixes as needed
   f. Write tool states (inspected/fixed) back to frontmatter
```

______________________________________________________________________

## Ungoverned File Handling

When a tool finds a file without frontmatter that is not in `.promptignore`:

### Interactive Mode (Default)

```
File: docs/legacy-guide.md
No prompt_spec found. Not in .promptignore.

Is this a governed prompt file?
[Y] Yes    → Write minimal frontmatter
[N] No     → Add to .promptignore
[S] Skip   → Ask again next scan
```

**If Yes:**

```yaml
---
prompt_spec:
  version: "0.4.0"
  compliance:
    status: pending
---
```

Tool prompts for `id` and `title`, or generates from filename.

**If No:**
Tool appends file pattern to `.promptignore`:

```
# Added by impromptu on 2026-05-07
docs/legacy-guide.md
```

**If Skip:**
Tool remembers skip choice in session only (not persisted).

### Non-Interactive Mode (--ci, --non-interactive)

Tools MUST NOT prompt. Use heuristics:

| Heuristic                                  | Default Action                           |
| ------------------------------------------ | ---------------------------------------- |
| File in `prompts/` or `prompt/` directory  | Assume prompt, write pending frontmatter |
| `.prompt.md` or `.prompt` extension        | Assume prompt, write pending frontmatter |
| File in `docs/`, `tests/`, `node_modules/` | Assume not prompt, skip silently         |
| No heuristic match                         | Skip with warning to stderr              |

Override with flags:

```bash
impromptu-check --assume-all-prompts      # Write frontmatter for all ungoverned
impromptu-check --assume-none-prompts     # Skip all ungoverned
```

______________________________________________________________________

## Writing Tool States

### Writing `inspected`

```python
def write_inspected(file_path, tool_name, result, issues=None, error=None):
    fm = read_frontmatter(file_path)

    fm["prompt_spec"]["inspected"] = {
        "tool": tool_name,
        "at": datetime.utcnow().isoformat() + "Z",
        "result": result
    }

    if result == "fail":
        fm["prompt_spec"]["inspected"]["issues"] = issues or []
    elif result == "error":
        fm["prompt_spec"]["inspected"]["error"] = error

    # Clear fixed if now passing
    if result == "pass":
        fm["prompt_spec"].pop("fixed", None)

    write_frontmatter(file_path, fm)
```

### Writing `fixed`

```python
def write_fixed(file_path, tool_name, result, changes=None, error=None):
    fm = read_frontmatter(file_path)

    fm["prompt_spec"]["fixed"] = {
        "tool": tool_name,
        "at": datetime.utcnow().isoformat() + "Z",
        "result": result
    }

    if result == "pass":
        fm["prompt_spec"]["fixed"]["changes"] = changes or []
    elif result == "error":
        fm["prompt_spec"]["fixed"]["error"] = error

    write_frontmatter(file_path, fm)
```

______________________________________________________________________

## Reading Compliance Status

Tools should derive status for display/reporting but never write it:

```python
def derive_status(prompt_spec):
    compliance = prompt_spec.get("compliance", {})

    # Human overrides (priority order)
    if compliance.get("reviewed", {}).get("by"):
        return "pass"
    if compliance.get("exceptions"):
        return "pass"
    if compliance.get("status") == "ignore":
        return "ignore"
    if compliance.get("status") == "pending":
        return "pending"

    # Tool-derived states
    inspected = prompt_spec.get("inspected")
    if not inspected:
        return "pending"

    if inspected["result"] == "pass":
        return "pass"

    # fail or error
    return "fail"
```

______________________________________________________________________

## State Transitions

### Check Cycle

```
pending → inspected.result: pass → pass
pending → inspected.result: fail → fail
pass    → inspected.result: fail → fail (regression)
fail    → fixed.result: pass → fail (unverified)
fail    → inspected.result: pass → pass (verified after fix)
```

### Human Interventions

```
any     → human sets exceptions + reviewed → pass
any     → human sets status: ignore → ignore
ignore  → human sets status: pending → pending (re-enable)
```

______________________________________________________________________

## Error Handling

### Invalid Frontmatter

| Error                         | Action                                    |
| ----------------------------- | ----------------------------------------- |
| Missing `prompt_spec`         | Treat as ungoverned (see above)           |
| Missing `prompt_spec.version` | Report error, skip file                   |
| Invalid `compliance.status`   | Report error, derive as `pending`         |
| Missing `inspected.result`    | Ignore block, derive without it           |
| Future `prompt_spec.version`  | Warn "unknown version, best-effort parse" |

### Tool Crashes

If a check or fix tool crashes:

```yaml
inspected:
  tool: impromptu-check
  at: "2026-05-07T00:00:00Z"
  result: error
  error: "AST parser crashed: segmentation fault"
```

Status derived as `fail`. Human sees the error and can:

- Re-run the tool
- Report bug to tool maintainer
- Use `reviewed` to override if urgent

______________________________________________________________________

## Batch Operations

### Re-check All Files

```bash
impromptu-check --all
```

1. Scan all governed files
1. Skip `.promptignore` matches
1. For each file with `compliance.status != ignore`:
   - Run check
   - Write `inspected` block
   - Clear `fixed` if `pass`

### Fix All Failing Files

```bash
impromptu-fix --all
```

1. Scan all governed files
1. Skip `.promptignore` matches and `status: ignore`
1. For each file with derived `status: fail`:
   - Run fix
   - Write `fixed` block
   - Do NOT re-check (leave as `fail` — unverified)

### Verify Fixed Files

```bash
impromptu-check --fixed-only
```

1. Find files with `fixed` block present
1. Run check
1. Write `inspected` block
1. Clear `fixed` if `pass`

______________________________________________________________________

## CI Integration

### Recommended CI Step

```yaml
# .github/workflows/prompt-compliance.yml
- name: Check prompt compliance
  run: |
    impromptu-check --all --ci --format=json

- name: Comment on PR
  if: failure()
  uses: actions/github-script@v6
  with:
    script: |
      const report = require('./prompt-compliance-report.json');
      // Comment failing files on PR
```

### Exit Codes

| Code | Meaning                              |
| ---- | ------------------------------------ |
| 0    | All governed files pass              |
| 1    | One or more files fail               |
| 2    | Tool error (crashes, invalid config) |
| 3    | Usage error (bad flags)              |

### Output Formats

```bash
impromptu-check --format=summary    # Human-readable table
impromptu-check --format=json       # Machine-readable report
impromptu-check --format=markdown   # GitHub comment format
```

______________________________________________________________________

## Version Compatibility

Tools SHOULD declare which `prompt_spec.version` they support:

```yaml
# impromptu-check --version
impromptu-check v1.2.3
Supports prompt_spec: 0.4.0, 0.3.x (legacy mode)
```

When encountering a future version:

- Attempt best-effort parse
- Warn user about version mismatch
- Do not write tool states (risk of corrupting newer format)
