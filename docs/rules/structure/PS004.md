---
id: PS003
title: Missing output contract
description: Detects prompts that omit a clear output shape or format when one is expected for downstream tooling.
version: 0.1.0
type: rule
license: MIT
---

# PS003 — Missing output contract

## Summary

The prompt does not clearly specify the required output shape or format in situations where downstream code, tools, or reviewers expect a predictable structure.  
This makes it harder to parse, validate, or compare results and increases the risk of brittle integrations.

## Rationale

Many prompts feed into downstream systems that assume a particular structure: JSON objects, bullet lists, headings plus sections, or fixed response slots.  
When the output contract is implicit or under-specified, models may respond with prose or formatting that breaks parsers, invalidates evals, or confuses human operators.  
Being explicit about the expected format also makes prompts more portable and easier to lint for other issues (for example, inconsistent examples).

## Detection guidance

Detectors SHOULD consider PS003 when:

- There is evidence that downstream consumers expect structure:
  - Comments or metadata indicating JSON, CSV, or a specific schema.
  - Template placeholders that suggest structured fields.
  - File naming or tags (for example, `*-json.prompt.md`, `x-output: json`).
- The prompt text does not contain any clear formatting contract, such as:
  - “Respond with JSON only, using this schema: …”
  - “Return exactly three bullet points.”
  - “Use the following headings: …”
- Example responses (if present) are inconsistent or purely free-form prose while the project conventions suggest structured outputs.

Heuristics:

- Look for phrases like “strict JSON”, “YAML”, “CSV”, “markdown table”, “list of”, “exactly N items”, “schema”, “fields” in comments or surrounding context.
- If the prompt references “parsing” or “downstream processing” but never names an output format, this is a strong signal.
- Conversely, purely conversational prompts without any obvious downstream consumer may be out of scope and SHOULD NOT be flagged.

## Repair guidance

Template-guided repairs can be safe when the expected format is clear from context:

- Add a short “Output format” section near the end of the prompt, specifying the required structure.
- Reference an existing schema defined elsewhere in the repository rather than inlining a new one if that is the project pattern.

Examples:

- From:  
  > Read the following log lines and identify any errors or anomalies.

- To:  
  > Read the following log lines and identify any errors or anomalies.  
  > **Output format:**  
  > - If no issues are found, respond with the single word `OK`.  
  > - Otherwise, respond with a JSON array of objects, each with `timestamp`, `severity`, and `message` fields.

Tools SHOULD:

- Reuse known schemas from configuration when possible.
- Avoid inventing complex schemas without evidence; in ambiguous cases, they SHOULD propose a minimal contract for human review instead of auto-applying changes.

## Limitations

- Some prompts are intentionally format-agnostic, especially exploratory or ideation prompts; PS003 SHOULD NOT be applied there.
- In many cases, the “expected format” is only known to the developer and cannot be inferred reliably from the prompt text alone.
- Over-enforcing output contracts can make prompts brittle to benign variation in model outputs.

Implementations SHOULD provide configuration to:

- Limit PS003 to prompts tagged or annotated as “structured-output”.
- Allow false positives to be suppressed on a per-file basis when free-form output is acceptable.

## Examples

### Non-compliant

> Read the following product reviews and decide whether each one is positive, negative, or mixed.  
> Explain your reasoning.

No explicit indication of how the result should be structured, despite being a classification task.

### Compliant

> Read the following product reviews and decide whether each one is positive, negative, or mixed.  
> Explain your reasoning.  
>  
> **Output format:**  
> Return a JSON array where each element has:  
> - `label`: one of `"positive"`, `"negative"`, or `"mixed"`.  
> - `rationale`: 1–2 sentences explaining the decision.

## Evidence notes

- Structured output guidance in vendor docs shows that clear output contracts significantly improve reliability and downstream integration.  
- Research and community practice around evaluation frameworks similarly assume predictable output shapes.
