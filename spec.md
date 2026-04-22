# Prompt Lint Standard Draft

## Purpose

This document proposes a portable linting standard for authored LLM prompts. The standard is intended to be tool-agnostic and to support multiple implementations, including CLI tools, editors, CI checks, and prompt management systems.

## Scope

This standard covers prompt-side diagnostics only:

- structure
- clarity
- consistency
- metadata
- formatting
- safety/interpolation
- prose/style

This standard does not cover runtime output validation, eval suites, or behavioral scoring.

## Honesty criteria

The standard should be explicit about what it can and cannot claim.

### Claims it can reasonably make

- Some prompt defects are statically observable.
- Some prompt defects are general enough to catalogue across tools.
- Some repairs are deterministic and low-risk.
- Some repairs are semantic and should require review and verification.

### Claims it should not make

- That a lint-clean prompt is therefore a good prompt.
- That a repair which reads better to humans will behave better across models.
- That prompt linting can replace evals.
- That all rules are equally model-independent.

## Design principles

1. Markdown is the source of truth.
1. Codes are stable and portable.
1. Rule text should separate detection, rationale, and repair boundaries.
1. Repairs must be classified by trust level.
1. Scientific and community evidence should be cited where available.
1. Rules should remain honest about uncertainty and model sensitivity.
