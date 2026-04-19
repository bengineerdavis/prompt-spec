---

id: motivation
title: Motivation
description: Why the Prompt Rules Standard exists and why a portable standard now matters.
version: 0.1.0
type: documentation
license: MIT
---

<!-- Above: YAML frontmatter used by tools. -->

# Motivation

> **tl;dr** — See the [Why section in the README](../README.md#why) for the short version.

## The fragmentation problem

Prompt knowledge is real, but scattered and often contradictory. The ecosystem cannot even agree on basic syntax or file formats. For example, [Anthropic’s official documentation](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/use-xml-tags) heavily dictates using `<xml>` tags to structure prompts and isolate variables. Meanwhile, [OpenAI and the open-source community](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api) lean heavily toward Markdown formatting. We don't even have a universally agreed-upon file extension for storing prompts—some use `.md` or `.txt`, while Microsoft introduced [`.prompty`](https://microsoft.github.io/promptflow/tutorials/prompty-quickstart.html) (Markdown with YAML frontmatter) and orchestration tools often wrap prompts in JSON.

Beyond formatting, practitioners have to reconcile an overwhelming volume of competing, reputable knowledge sources. To figure out how to structure a single complex prompt, a developer might have to synthesize:

- **Vendor API guides** that push entirely different paradigms.
- **Academic papers** identifying new model behaviors or limitations.
- **Reputable applied AI blogs** and community guides.
- **Grassroots practitioner communities** testing new models in real time.

Compounding this fragmentation is the sheer speed of the AI field. Model capabilities—such as context window sizes, instruction-following accuracy, and native reasoning—evolve so rapidly that a definitive "best practice" article written just three to six months ago may already be obsolete. Tricks that were mandatory in early 2024 are often anti-patterns today. For example, for years, adding "think step by step" ([Chain of Thought](https://arxiv.org/abs/2201.11903)) was the golden rule of prompt engineering; but when OpenAI released the `o1` reasoning models, their [official guidance](https://developers.openai.com/api/docs/guides/reasoning-best-practices) explicitly told developers to *stop* using it, as it interferes with the model's native reasoning.

The best institutional knowledge often stays proprietary. As a result, it is incredibly difficult to know which practices are broadly useful, which are model-specific, which are outdated, which are just house style, and how to discuss any of this consistently across projects and tools.

## Drawing inspiration from LSP, ESLint, and uv

We are not trying to build the definitive prompt linter. We are building the rulebook so the community can build the tooling ecosystem.

When the software world struggled with IDE integration fragmentation, Microsoft didn't build a new IDE for every language—they built the Language Server Protocol (LSP), standardizing how editors and compilers talk to each other. When JavaScript developers drowned in competing style guides, ESLint didn't enforce one true style—it built a pluggable rule engine so the community could define their own standards. Most recently, Astral's `uv` proved that when foundational Python tooling is rebuilt for raw speed and correctness, the entire ecosystem benefits.

If prompts are now critical infrastructure, they require the same foundational tooling. By defining a portable, machine-readable catalog of prompt defects, we give tool-makers the shared vocabulary they need to build fast, interoperable formatters, analyzers, and CI/CD pipelines.

## From prompts to governed documents

The project has evolved beyond a prompt-only framing. Prompts remain central, but they are now treated as one type of governed document among many.

Specifications, rule docs, READMEs, architecture notes, runbooks, policies, and similar human-language artifacts can all provide machine-usable design context when a project chooses to govern them that way. This is the direction described as **Document-Driven Design (DDD+)**: prompts, specifications, and operational documents all become part of a broader context surface for design, implementation, and tooling.

That does not mean every document should be ingested by every tool. Some documents are for humans only, some are archival, and some are intentionally excluded from agent or retrieval context. This is why the standard is evolving toward explicit frontmatter inclusion metadata and repository-level ignore behavior.

## The complexity is already visible in public

The public guidance from major vendors already shows how deep prompt work goes. [OpenAI's documentation](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api) discusses structure, delimiters, prompt placement, model differences, snapshot pinning, and the need for evals. [Google's Gemini documentation](https://ai.google.dev/gemini-api/docs/prompting-strategies) covers precision, delimiter strategy, verbosity control, long-context organization, and multimodal prompting. Microsoft's guidance emphasizes explicit syntax and parseable prompt structure. [Anthropic's writing on context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) shows how prompt design expands into managing the broader inference context for agent systems.

Beyond vendor docs, a broader set of community-maintained guides and widely taken courses reflect the same breadth of knowledge involved. Resources like the DAIR.AI [*Prompt Engineering Guide*](https://www.promptingguide.ai/) and [*Learn Prompting*](https://learnprompting.org/) act as massive, community-maintained encyclopedias. Foundational courses—such as DeepLearning.AI's [*ChatGPT Prompt Engineering for Developers*](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/) and the [*Google Prompting Essentials Specialization*](https://www.coursera.org/specializations/prompting-essentials-google) on Coursera—have trained hundreds of thousands of developers on how to establish structured, reusable prompt libraries.

None of these references agree on a single shared rule set. Together, they illustrate how many competing frameworks, vocabularies, and techniques currently exist—and how much that fragmentation costs practitioners who need to build reliable, consistent tools.

## The role of academia: Discoveries, shifting paradigms, and the call for standards

Academia continuously reshapes our understanding of LLM behavior, providing the empirical foundation for how prompts *should* be written. However, keeping up with this research—and translating it into daily tooling—is exhausting.

Academic discoveries routinely dictate prompt structure. Research like the [*Lost in the Middle*](https://arxiv.org/abs/2307.03172) (2023) paper proved that language models severely degrade when recalling information placed in the center of long contexts, fundamentally changing how developers must sequence their prompt sections.

Recently, researchers have begun formalizing prompt quality and management itself. This standard draws heavily on this emerging consensus:

- **Formalizing Defects:** Papers like [*A Taxonomy of Prompt Defects in LLM Systems*](https://arxiv.org/abs/2509.14404) (2025) provide the vocabulary for our rule families, proving that issues like conflicting constraints, missing context, and structural ambiguity can be systematically categorized and detected.
- **Validating Repairs:** Research such as [*An Empirically-grounded tool for Automatic Prompt Linting and Repair*] validates that while static prompt linting is highly effective, automated *semantic repairs* carry high risks and require strict trust boundaries (which informs our separation of `AFX` vs `LLM` fixes).
- **The Need for Standardization:** A recent empirical study, [*Understanding Prompt Management in GitHub Repositories*](https://arxiv.org/abs/2509.12421) (2025), proved just how fragmented the ecosystem is. Finding a chaotic mix of file formats and metadata inconsistencies across GitHub, the authors explicitly concluded that this inconsistency breaks automated tooling, calling on the community to establish "clear guidelines and standardized formats designed specifically for prompt management."

## The shift from "Awesome Prompts" to engineering systems

In the last 18 months (2024–2026), community sentiment has shifted dramatically. Early in the LLM boom, developers relied on massive GitHub repositories of "awesome prompts"—crowdsourced lists of clever, highly specific phrasing tricks.

Today, the community consensus has rejected that approach. In [highly visible recent discussions](https://www.reddit.com/r/ChatGPTPro/comments/1shbzcg/prompt_engineering_repos_on_github_up_to_date_for/), developers explicitly note that these repos are outdated. The focus has shifted away from "witty wording" and toward structured schemas, explicit constraints, and parallel task execution. Prompting is no longer treated as a dark art; it is treated as software engineering.

Recent security research underscores why this structural rigor is necessary. A March 2026 report by Palo Alto Networks' Unit 42 on [*Prompt Fuzzing*](https://unit42.paloaltonetworks.com/genai-llm-prompt-fuzzing/) proved that simply rephrasing a prompt—altering its structure or syntax while preserving its meaning—is enough to bypass safety guardrails in both open and closed models. This demonstrates that catching structural anomalies and prose inconsistencies in prompts is not just a matter of style; it is a matter of system security and reliability.

Furthermore, the industry is moving from static prompts to dynamic systems. Recent literature, such as the [*Context Engineering*](https://arxiv.org/abs/2603.09619) (March 2026) paper, argues that as AI systems evolve into multi-step agents, prompts must be managed as governed, versioned, and testable code artifacts.

If prompts are now critical infrastructure, they require the same ecosystem of tooling—linters, formatters, and static analyzers—that we demand for Python, Rust, or TypeScript.

## The community is already moving

The tooling landscape makes it clear this is a felt need. In a short period of searching, a number of tools have emerged that tackle prompt linting, analysis, safety, and formatting from different angles:

- [AryaanSheth/promptlint](https://github.com/AryaanSheth/promptlint)
- [arabindanarayandas/invari](https://github.com/arabindanarayandas/invari)
- [protectai/llm-guard](https://github.com/protectai/llm-guard)
- [alexmavr/promptsage](https://github.com/alexmavr/promptsage)
- [youcommit/promptlint](https://github.com/youcommit/promptlint/tree/master)
- [jlevy/leximetry](https://github.com/jlevy/leximetry/tree/main)
- [jlevy/flowmark-rs](https://github.com/jlevy/flowmark-rs)
- [Linting LLM prompts: catching contradictions (r/LocalLLaMA)](https://www.reddit.com/r/LocalLLaMA/comments/1qlyip2/linting_llm_prompts_catching_contradictions/)

What is missing is a shared standard these tools can converge on—a portable catalogue of rule codes, a common vocabulary, and a clear separation between what can be automated and what still requires human judgment or model-assisted repair.

## Where this started

This project grew out of building [Impromptu](https://github.com/bengineerdavis/impromptu), an effort to automate more of the prompt management lifecycle. Even deciding which [markdownlint](https://github.com/davidanson/markdownlint#rules--aliases) rules carry over cleanly to prompts versus formal documentation turned out to require real judgment. Some checks make sense for prompts. Others do not. And prompts have categories of issues—under-specification, conflicting constraints, unsafe interpolation—that markdown linting was never designed to express.

As the project evolved, it became clear that the overlap between prompt rules and documentation rules is large enough that the standard should reuse existing Markdown rule semantics whenever possible and only introduce new rules when the prompt or document domain actually requires them.

## What this standard is not trying to do

This project should not overlap with tools that already solve adjacent problems well. Secret detection, PII scanning, and similar security checks are better handled by existing, more mature tools. The goal is for prompt linters and formatters to integrate those solutions rather than rebuild them. Reinventing solved work is usually a mistake.

Equally important: prompt rules are not a substitute for evals or behavioral testing. Prompt quality is contextual, model-sensitive, and ultimately tied to how a prompt performs, not just how it reads. A rule-clean prompt is not automatically a good prompt. This standard should be honest about that boundary throughout.

## The longer-term goal

A shared rule catalogue and shared vocabulary can still make prompt work more legible, automatable, and portable—even without making strong behavioral guarantees. The hope is to support an ecosystem of composable tools that can plug into one another: prompt quality checks, style checks, safety boundaries, metadata validation, documentation validation, and project-specific conventions, assembled into higher-level tooling.

The end result should be a simpler experience: building great prompts and governed documents for personal, professional, or enterprise use should not require manually reconstructing the same scattered knowledge every time.

***

## References

Anthropic: [Use XML Tags](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/use-xml-tags)
OpenAI: [Best practices for prompt engineering](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api)
Microsoft: [Prompty file format specification](https://microsoft.github.io/promptflow/tutorials/prompty-quickstart.html)
*Chain-of-Thought Prompting Elicits Reasoning in Large Language Models* (arXiv:2201.11903, Jan 2022). [Read paper](https://arxiv.org/abs/2201.11903)
OpenAI: [Reasoning Best Practices (o1 anti-patterns)](https://developers.openai.com/api/docs/guides/reasoning-best-practices)
Google: [Prompt design strategies (Gemini)](https://ai.google.dev/gemini-api/docs/prompting-strategies)
Anthropic: [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
DAIR.AI: [Prompt Engineering Guide](https://www.promptingguide.ai/)
Open-Source Guide: [LearnPrompting.org](https://learnprompting.org/)
DeepLearning.AI: [ChatGPT Prompt Engineering for Developers](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)
Google Coursera: [Google Prompting Essentials Specialization](https://www.coursera.org/specializations/prompting-essentials-google)
*Lost in the Middle: How Language Models Use Long Contexts* (arXiv:2307.03172, July 2023). [Read paper](https://arxiv.org/abs/2307.03172)
*A Taxonomy of Prompt Defects in LLM Systems* (arXiv:2509.14404, Sept 2025). [Read paper](https://arxiv.org/abs/2509.14404)
*An Empirically-grounded tool for Automatic Prompt Linting and Repair*. Validates that automated semantic repairs carry high risks and require strict trust boundaries.
*Understanding Prompt Management in GitHub Repositories* (arXiv:2509.12421, Sept 2025). [Read paper](https://arxiv.org/abs/2509.12421)
Reddit `r/ChatGPTPro`: [Prompt engineering repos on Github up to date for 2026?](https://www.reddit.com/r/ChatGPTPro/comments/1shbzcg/prompt_engineering_repos_on_github_up_to_date_for/) (April 2026).
Palo Alto Networks Unit 42: [Open, Closed and Broken: Prompt Fuzzing Finds LLMs Still Fragile](https://unit42.paloaltonetworks.com/genai-llm-prompt-fuzzing/) (March 2026).
*Context Engineering* (arXiv:2603.09619, March 2026). [Read paper](https://arxiv.org/abs/2603.09619)
