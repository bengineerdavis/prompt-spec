---
id: roadmap
title: Roadmap
description: Planned structural additions and ecosystem features for the Prompt Rules Standard.
version: 0.1.0
type: roadmap
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Roadmap

> **Alpha notice:** This is an early-stage, community-driven project. Nothing
> in this roadmap is a firm commitment. All items are tentative high-interest
> goals — directions we think are worth exploring, not a release schedule or
> guaranteed feature list. Priorities may change as the project and its
> community evolve.

This document outlines proposed structural additions to the Prompt Rules
Standard and near-term ecosystem work we think is worth pursuing.

## 1. Prompt Anatomy & Strictness Levels

The current catalog lists individual defects, but it does not yet define a
shared view of what a well-structured prompt looks like.

We are interested in defining a **Prompt Anatomy Guide** that outlines the
structural sections of a high-quality prompt (for example: Role, Objective,
Constraints, Output Schema, Context).

To support different project needs, this guide would classify components and
rules using standard [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119)
terminology:

- **REQUIRED (Strict):** Elements that must be present for a prompt to be
  considered valid under this standard.
- **RECOMMENDED (Standard):** Elements that should be used in most cases, but
  can be safely ignored if the developer understands the trade-offs.
- **OPTIONAL (Loose):** Elements that are useful in specific edge cases (for
  example, few-shot examples) but are not strictly necessary.

This would allow individual projects to set a linter strictness level (for
example, `strict`, `standard`, `loose`) based on their team's maturity and
risk tolerance.

*This item is high-interest but has no committed timeline.*

## 2. Project-Specific & Experimental Rules

No universal standard can capture every domain-specific need. Teams building
healthcare chatbots will need different prompt rules than teams building
