---
id: PS0301
title: Overloaded all-in-one section
description: Detects prompts that pack tasks, constraints, examples, and formatting cues into a single undifferentiated block instead of using clear sections.
version: 0.1.0
type: rule
license: MIT
---

# PS0301 — Overloaded all-in-one section

## Summary

The prompt mixes multiple conceptual roles—task description, constraints, examples, formatting instructions, and sometimes metadata—into one long, undifferentiated block.  
This makes it harder for both models and humans to parse, increasing the likelihood of missed constraints, inconsistent behavior, and fragile maintenance.

## Rationale

Clear separation between different parts of a prompt (task, rules, examples, output format, context) helps models follow instructions and helps developers reason about changes.  
When everything is crammed into one paragraph or section, any edit risks breaking implicit structure, and it becomes difficult to spot contradictions or missing elements.  
Structured prompts also align better with rule-based tooling and evals, since each section can be validated independently.

## Detection guidance

Detectors SHOULD consider flagging PS020 when:

- The prompt is long (for example, multiple sentences or paragraphs) but contains **no** headings, section markers, or structural delimiters.
- Within a single block of text, you can detect:
  - At least one explicit task statement (“Your task is…”, “Summarize…”, “Classify…”).
  - At least one constraint (“Do not…”, “Always…”, “Only…”).
  - At least one example pattern (“For example…”, “Here is an example: …”) or inline formatted sample output.
  - Possibly output formatting cues (“Respond with JSON…”, “Return a bullet list…”).
- There are no blank lines or markers that visually separate these roles.

Heuristics:

- Count “role markers” within a single paragraph:
  - Task-like verbs.
  - Constraint keywords.
  - Example phrases.
  - Output-format phrases.
- If two or more roles appear in the same dense block and there are no headings like “Task”, “Rules”, “Examples”, “Output format”, consider PS020.

This rule is about *organization*, not content: a short, simple prompt that happens to mention both a task and a constraint in one line may be fine; detectors SHOULD focus on longer, complex prompts.

## Repair guidance

Repairs for PS020 primarily involve restructuring, not changing meaning:

- Introduce lightweight headings or separators:
  - “Task”, “Context”, “Rules”, “Examples”, “Output format”.
- Move content into the appropriate sections with minimal rewrites.
- Ensure examples are clearly separated from instructions (for example, using fenced code blocks or indentation).

Example repair:

- From (single block):  
  > You are a helpful assistant. Your task is to summarize customer emails. Do not change the meaning or tone. Only respond with the summary text, no bullet points or headings. For example, when given: "Hi, I’m upset about my order", you might respond with "The customer is dissatisfied with their recent order." Always keep it under three sentences.

- To (structured):  
  > **Role:**  
  > You are a helpful assistant.  
  >
  > **Task:**  
  > Summarize customer emails.  
  >
  > **Rules:**  
  > - Do not change the meaning or tone.  
  > - Always keep the summary under three sentences.  
  >  
  > **Output format:**  
  > - Respond with plain text only, no bullet points or headings.  
  >  
  > **Example:**  
  > Input: “Hi, I’m upset about my order.”  
  > Output: “The customer is dissatisfied with their recent order.”

Tools MAY automate a first-pass restructuring by:

- Splitting on sentences and grouping them by role (task, constraint, example, format).
- Inserting minimal headings around each group.

However, such patches SHOULD be presented for human review, as small restructuring choices can affect behavior.

## Limitations

- Highly compact prompts (for example, single-sentence prompts used for quick chats) are not appropriate targets; structure would add noise.
- Some systems intentionally use minimal headings for brevity; forcing additional sections can conflict with house style.
- Determining when “enough” structure exists is subjective; projects MAY tune thresholds or disable PS020 where it does not add value.

Implementations SHOULD:

- Allow PS020 to be scoped to prompts above a size/complexity threshold.
- Support house-style mappings for section titles (for example, “Rules” vs. “Constraints”).

## Examples

### Non-compliant

> You are an AI copywriter. Your task is to write product descriptions for our e-commerce site, focusing on benefits not features. Do not mention competitors. Keep each description under 80 words and include a short tagline at the end. For example, you might write “Effortless style for any occasion” as a tagline.

All roles are mixed into one block with no visual or structural separation.

### Compliant

> **Role:**  
> You are an AI copywriter.  
>
> **Task:**  
> Write product descriptions for our e-commerce site, focusing on benefits, not features.  
>
> **Rules:**  
>
> - Do not mention competitors.  
> - Keep each description under 80 words.  
>  
> **Output format:**  
>
> - A description paragraph followed by a short tagline on a new line.  
>  
> **Example tagline:**  
> “Effortless style for any occasion.”

## Evidence notes

- Context-engineering and prompt design guidance increasingly advocates for separating task, rules, examples, and output schema as prompts grow more complex.  
- Taxonomies of prompt defects recognize “structural ambiguity” and “mixed roles” as a distinct source of failures that static analysis can help detect.
