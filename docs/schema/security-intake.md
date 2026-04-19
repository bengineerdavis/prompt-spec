---
id: security-intake
title: Security Intake
description: How external vulnerability advisories map to the PV rule family and flow into the standard.
version: 0.1.0
type: schema
license: MIT
***

# Security Intake

This document specifies how external vulnerability intelligence — CVEs, OSV advisories, Dependabot alerts, fuzzing results, red-team findings, and similar sources — maps to the `PV` (Security / vulnerability intelligence) rule family and flows into the standard.

## PV and PX

`PV` rules capture concrete, time-sensitive security issues uncovered via CVEs, OSV records, fuzzing, red-team testing, or incident reports. They function as security patches to the standard: when a `PV` rule is added or updated, adopters SHOULD treat it as a timely signal to review and update affected prompts, templates, and toolchains.

`PX` rules describe general, evergreen safety and interpolation patterns for handling untrusted input, tool output, and instruction layering. They are not tied to specific advisories or timelines.

In practice, `PV` rules often arrive first, driven by concrete incidents. As patterns repeat, the catalog may be refactored so that `PX` rules encode the generalized safety guidance, while `PV` rules remain focused on time-sensitive, evidence-backed vulnerabilities.

Over time, multiple `PV` rules may converge on the same underlying structural issue. When that happens, the project MAY introduce or refine a `PX` rule to capture the broader pattern, allowing implementations to gain categorical protection against related vulnerabilities even before future `PV` rules are filed.

## Why security is its own family

Security diagnostics are categorically different from other rule families. A `PS` or `PC` violation describes a quality problem that can be repaired. A `PV` violation describes a known, externally grounded risk and may have no safe automated repair at all.

`fix_kind` answers how something can be repaired. `PV` answers what class of problem this is. These are orthogonal axes and must stay separate. A `PV` rule will often carry `fix_kind: NONE` in catalog metadata because the correct response is human review, prompt redesign, or toolchain patching rather than automated rewriting.

## Advisory sources

The standard recognises several primary intake channels:

| Source | Format | What it covers |
|---|---|---|
| OSV | OSV JSON | Open-source package vulnerabilities across ecosystems |
| CVE / NVD | CVE JSON | Broadly published vulnerability identifiers |
| Dependabot / GitHub | GitHub Advisory DB | Dependency alerts surfaced in CI for the repo's own toolchain |
| Security testing | Internal or external reports | Prompt fuzzing, red-team findings, exploit proofs, and incident analysis |

OSV is the preferred machine-readable format where available because it describes affected packages, versions, and ecosystems precisely.

## The intake flow

Advisories do not automatically become rule changes. The flow has a mandatory human review gate:

```text
External advisory or finding (OSV / CVE / Dependabot / fuzzing / red-team)
        │
        ▼
 Does it affect prompt or doc processing tooling,
 known unsafe prompt patterns, or parser behavior?
        │
       Yes
        │
        ▼
 Open a review issue tagged `security-intake`
 with the advisory ID, source, and affected scope
        │
        ▼
 Human reviewer maps to PV candidate:
   - Toolchain vulnerability          → PV001+
   - Known vulnerable prompt pattern  → PV00x
   - Trust boundary gap confirmed     → PV00x
   - Rule conflict or supersession    → PV00x
        │
        ▼
 PR updates the standard (catalog, rule docs, CHANGELOG)
        │
        ▼
 Advisory-linked PV rule or note is published
```

Automation can feed the top of this funnel. Human judgment is required at every step after that.

## Optional security frontmatter

Files that consume untrusted input or have been reviewed against advisories MAY declare this in frontmatter using `x-security`:

```yaml
x-security:
  consumes_untrusted_input: true
  trust_boundary: user-content-delimited
  advisory_reviewed_at: 2026-04-19
  advisory_sources:
    - osv
    - github
```

This is an extension field (`x-*`) and is not validated by the core schema. Tools that understand it SHOULD surface it; tools that do not MUST ignore it.

## What this standard does not replace

The `PV` family and this intake process cover prompt- and document-pipeline security risks. They are not a substitute for broader security and privacy tooling:

- **Secret detection** — use dedicated scanners such as Gitleaks or TruffleHog to scan code, configs, and history for hard-coded credentials.
- **PII and sensitive data scanning** — use freely available tools designed for this job, such as Microsoft Presidio for text and image anonymization pipelines or Hawk Eye for scanning filesystems and storage backends for PII and sensitive data.
- **Dependency vulnerability patching** — use GitHub Dependabot or equivalent to track advisories for your dependencies and open upgrade pull requests automatically.
- **Runtime behavioral evals** — use runtime analytics systems or evaluation harnesses to detect harmful behavior over time. This standard defines static rule structures, not runtime scoring.

When referencing adjacent tools, prefer options that are free to use, preferably open source, and easy to automate locally or in CI. Tools with simple CLI installation, Docker entrypoints, or support for git-hook automation frameworks such as `pre-commit`, `prek`, Husky, or plain `.git/hooks` are especially useful for teams adopting this standard incrementally.

Integrations are encouraged: prompt linters and formatters SHOULD call out to these systems where appropriate, but MUST NOT attempt to re-implement them as `PV` rules.
