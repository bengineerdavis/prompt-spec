---
id: PS0101
title: Missing explicit task or objective
description: Detects prompts that provide context or role framing but never state a clear task or requested outcome.
version: 0.1.0
type: rule
license: MIT
---

# PS0101 — Missing explicit task or objective

## Summary

The prompt provides background, role framing, or scenario setup but never clearly states what the model is supposed to do.\
This leaves the model to infer the task from context, which is unreliable and often produces off-target responses.

## Rationale

Even strong models do not reliably infer user intent from context alone.\
When a prompt only describes a situation or persona without a clear directive, responses tend to be generic, misaligned, or incomplete.\
Explicitly stating the task or objective makes the prompt easier to read, easier to review, and easier to evolve over time.\
It also allows tools to reason about whether the rest of the prompt (constraints, examples, formatting) actually supports the requested outcome.

## Detection guidance

Detectors SHOULD consider flagging PS001 when all of the following are true:

- The prompt contains substantial non-empty content (e.g., descriptive paragraphs, role setup, scenario framing).
- No imperative verb phrases or clear task statements are present near the end of the prompt, such as “Write…”, “Summarize…”, “Classify…”, “Generate…”, “Compare…”, “Extract…”, or house-style equivalents.
- There is no explicit question or request that targets a concrete output (for example, “What should we do next?” or “How would you rewrite this?”).
- The prompt is not clearly labeled or typed as an instruction template where the actual task will be filled in elsewhere (for example, a meta-prompt describing how to write prompts).

Heuristics that can help:

- Look for a section that appears to be “background” or “persona” followed by end-of-file, with no “Task”, “Goal”, “Your job”, or similar header.
- Search for modal/imperative verbs in the second person (“You will…”, “Please…”, “Your task is…”, “I want you to…”) and check if any of them describe a concrete action.
- If the only explicit instruction is something like “Act as…” or “You are…”, and nothing follows that describes what to produce, the rule likely applies.

Detectors MAY use language-specific patterns or model-assisted classification to identify whether a sentence is describing a task versus purely providing context.

## Repair guidance

This rule is typically repairable via a template-guided patch:

- Add a brief “Task” or “Your task” sentence near the end of the prompt, using language that matches the project’s house style.
- If the prompt describes a scenario but not the desired output, convert the implied goal into a direct request.

Examples of safe repairs:

- From: “You are a senior support engineer helping users debug issues in our error tracking tool.”\
  To: “You are a senior support engineer helping users debug issues in our error tracking tool. **Your task is to read each user message and respond with a concise, step-by-step troubleshooting plan.**”

- From: “Below is a customer email about a billing dispute.”\
  To: “Below is a customer email about a billing dispute. **Your task is to draft a polite, clear response that explains our policy, proposes a resolution, and asks for any missing information.**”

Repair tools MUST:

- Avoid inventing tasks that contradict the surrounding context.
- Prefer reusing existing wording from nearby comments or metadata when available (for example, repo documentation that already says “This prompt generates support responses”).
- Present the suggested task sentence as a patch for human review when the intent is ambiguous.

## Limitations

- Some prompts are intentionally meta-level (“Here is how to construct prompts for this model…”); in those cases, the “task” is implicit in the surrounding usage and may not need restating.
- System prompts or shared preambles may defer the exact task to later user messages; overzealous detection can flag these even when the design is intentional.
- Highly unconventional or non-English prompts may require model-based classification to avoid both false positives and false negatives.

Implementations SHOULD allow projects to suppress PS001 for files or sections that are explicitly designated as “context-only” or “meta-prompt” artifacts.

## Examples

### Non-compliant

> You are a senior reliability engineer at a large streaming company.\
> You care about safety, quality, and clear communication.\
> You are reviewing incidents as part of a weekly postmortem process.

There is no explicit task or requested output; the model has to guess what to do.

### Compliant

> You are a senior reliability engineer at a large streaming company.\
> You care about safety, quality, and clear communication.\
> **Your task is to review each incident summary and produce a concise postmortem: a 3–5 sentence narrative, a list of contributing factors, and 2–3 concrete follow-up actions.**

The task and expected output shape are now explicit.

## Evidence notes

- This rule aligns with vendor guidance that emphasizes clearly stating the task or objective rather than relying on implicit intent.
- It also reflects emerging taxonomies of prompt defects where “missing or vague goals” are treated as a distinct structural issue that can be detected statically.
