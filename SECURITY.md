---
id: security-policy
title: Security Policy
description: How to report security issues affecting the repository, tooling, and draft standard.
version: 0.1.0
type: policy
license: MIT
---

# Security Policy

## Reporting a vulnerability

If you believe you have found a security issue in this repository, its tooling, or the draft standard, please report it responsibly.

Until a dedicated private reporting channel is established, open a GitHub issue only if the report does **not** contain exploit details, sensitive data, private credentials, or step-by-step instructions that would increase risk for others.

If the issue is sensitive, treat it as a private disclosure workflow item in project operations rather than posting full details publicly.

## Scope

This policy covers security issues affecting:

- repository tooling and automation
- schema generation or parsing behavior
- prompt or governed-document processing pipelines
- rule-catalog build scripts and related packaging
- dependency and supply-chain concerns affecting this project

It also covers suspected prompt-side vulnerabilities or trust-boundary failures that may need review for inclusion in the `PV` family of the standard.

## What to include in a report

Please include as much of the following as possible:

- affected file, script, schema, or workflow
- steps to reproduce
- expected behavior
- actual behavior
- impact assessment
- whether the issue is already known in a public advisory such as CVE or OSV
- any affected versions, commits, or dependency ranges

## Draft status

This repository currently hosts a draft standard. Some processes, schemas, and rule layouts are still evolving. Security reports are still valuable during this phase, especially when they reveal:

- unsafe default assumptions
- trust-boundary confusion
- parser ambiguity
- catalog or schema decisions that could normalize insecure patterns

## Related documents

- [`docs/rules/RULES.md`](./docs/rules/RULES.md) — canonical rule catalog, including the distinction between `PX` and `PV`
- [`docs/schema/security-intake.md`](./docs/schema/security-intake.md) — how advisories, testing, and incident findings are reviewed and mapped into the `PV` family
- [`docs/schema/frontmatter.md`](./docs/schema/frontmatter.md) — frontmatter and schema guidance relevant to repository tooling

## Disclosure expectations

Please avoid publishing full exploit details until the project has had a reasonable opportunity to review the report, understand impact, and prepare mitigation guidance.

The project may document confirmed issues through rule updates, changelog entries, dependency upgrades, documentation changes, or future `PV` family entries where appropriate.
