---
id: PS0102
title: Missing role or persona section
description: Detects prompts that omit role or persona framing where the project or house style expects it.
version: 0.1.0
type: rule
license: MIT
---

# PS0102 — Missing role or persona section

## Summary

The prompt lacks explicit role or persona framing in contexts where the project’s conventions require it (for example, all production prompts must start with a “You are…” section).  
As a result, the model has no guidance about the voice, perspective, or domain posture it should adopt.

## Rationale

Many teams adopt a house style that requires explicit role or persona framing for important prompts, such as “You are a senior support engineer…” or “You are a careful JSON-only validator…”.  
This framing helps models maintain consistent tone, domain assumptions, and safety posture, and it gives reviewers a clear way to reason about how the model is being positioned.  
When the rest of the system assumes such a persona exists—for example, in documentation, evals, or UX copy—omitting it from the prompt introduces inconsistency and can degrade responses.

## Detection guidance

This rule is inherently project- or domain-specific.  
Detectors SHOULD only apply PS002 when the following conditions are met:

- The repository or project configuration declares that certain prompts (by path, tag, or metadata) MUST include a role or persona section.
- The prompt file does not contain any recognizable persona framing matching the expected pattern (for example, a “Role” section, a leading “You are…” sentence, or a known role template).

Possible signals:

- Absence of “You are…”, “Act as…”, or a similar persona pattern near the top of the file.
- No headings or labeled sections like “Role”, “Persona”, or “Assistant persona”.
- Metadata or tags (for example, `x-tags: [requires-persona]`) indicating that a persona is required but not present.

Detectors SHOULD allow:

- Configuring the exact phrases or regexes that count as a valid persona.
- Restricting PS002 to specific directories, file types, or prompt categories.

## Repair guidance

Because persona requirements are house-style decisions, repair is best handled via templates:

- Insert a minimal persona line that matches project conventions, such as “You are a helpful, concise assistant for [domain].”
- When possible, derive the persona from nearby documentation or tags (for example, if the prompt is tagged `support`, use a “support engineer” persona).

Example repair:

- From:  
  > Summarize each customer support ticket in one or two sentences.

- To:  
  > You are a senior customer support engineer who writes clear, empathetic responses.  
  > Summarize each customer support ticket in one or two sentences.

Tools SHOULD:

- Offer a house-style persona snippet configured per project rather than hard-coding generic text.
- Present persona insertion as an optional patch for human review, especially in sensitive domains.

## Limitations

- Many perfectly valid prompts intentionally avoid persona framing, especially when the tone is derived from upstream system prompts or from the UI context.
- Overly rigid enforcement of PS002 can lead to redundant, noisy personas that do not actually improve behavior.
- This rule depends on explicit project configuration—without it, tools cannot know whether a persona is expected.

Implementations SHOULD allow PS002 to be disabled globally or scoped narrowly to avoid forcing persona usage where it is not wanted.

## Examples

### Non-compliant (in a project that requires personas)

> Summarize each internal incident in 3–5 bullet points, highlighting impact, root cause, and remediation.

If house style requires every production prompt to include a persona, this is missing that section.

### Compliant

> You are a staff reliability engineer writing post-incident summaries for leadership.  
> Summarize each internal incident in 3–5 bullet points, highlighting impact, root cause, and remediation.

## Evidence notes

- Persona framing is widely recommended in vendor and community guidance for shaping tone and behavior, but not universally required.  
- This rule encodes project-specific expectations rather than claiming persona framing is always necessary.
