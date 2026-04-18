# Motivation

Building simple, safe, and reliable tools on top of LLMs still requires too much scattered, implicit knowledge about prompt quality.

Writing a good prompt — and even knowing how to structure one — requires a surprising amount of both explicit and tacit knowledge about good and bad prompt patterns. Worse, what counts as a “good” prompt shifts depending on the model or service being used, the task, the domain, and the project’s own style and quality bar.

The guidance that does exist is fragmented. Vendors publish competing advice and patterns. The wider community produces valuable ideas across blog posts, issue threads, forums, talks, code examples, and social platforms. The best institutional knowledge is often proprietary and stays siloed inside companies. As a result, it is hard to know which practices are broadly useful, which are model-specific, which are just house style, and how to talk about any of this consistently across projects and tools.

At the same time, many of these patterns are ready to be automated and abstracted. A growing set of tools already points in that direction. In only a short period of searching, it became clear that the community is actively exploring prompt-focused linting, analysis, safety checks, and formatting:

- [AryaanSheth/promptlint](https://github.com/AryaanSheth/promptlint)
- [arabindanarayandas/invari](https://github.com/arabindanarayandas/invari)
- [protectai/llm-guard](https://github.com/protectai/llm-guard)
- [alexmavr/promptsage](https://github.com/alexmavr/promptsage)
- [youcommit/promptlint](https://github.com/youcommit/promptlint/tree/master)
- [jlevy/leximetry](https://github.com/jlevy/leximetry/tree/main)
- [jlevy/flowmark-rs](https://github.com/jlevy/flowmark-rs)
- [Linting LLM prompts: catching contradictions](https://www.reddit.com/r/LocalLLaMA/comments/1qlyip2/linting_llm_prompts_catching_contradictions/)

These tools are a signal that the community is ready to formalize and abstract away more of this complexity. But they also highlight a gap: there is still no shared, portable rule catalogue or common vocabulary that tools can converge on.

This need became especially clear while building [Impromptu](https://github.com/bengineerdavis/impromptu), an effort to automate more of the prompt management lifecycle. Even something as simple as deciding which [markdownlint](https://github.com/davidanson/markdownlint#rules--aliases) rules make sense for prompts versus formal documentation turned out to require meaningful judgment. Some checks carry over well. Others do not. And prompts need categories of checks that markdown linting was never designed to express.

Part of the goal of this standard is to capture rules that do not already exist elsewhere, while avoiding unnecessary overlap with tools that already solve adjacent problems well. For example, this project should not try to replace existing tools for secret detection, PII scanning, or related security checks. Instead, prompt linters and formatters should be able to integrate those tools and build on them. Reinventing solved work is usually a mistake, especially when existing solutions are already more mature.

The broader hope is to support an ecosystem of composable QA and management tools that can plug into one another. In that world, prompt quality checks, prompt style checks, safety checks, formatting, metadata validation, and project-specific conventions can all be combined into higher-level tooling. The end result should be a simpler user experience: people should be able to create strong, relevant prompts for personal, professional, and enterprise use without needing to manually reconstruct the same scattered knowledge every time.

This project does not assume that prompt rules alone can prove prompt quality. Prompt quality is contextual, model-sensitive, and tied to evaluation. But a shared rule catalogue and shared language can still make prompt work more legible, automatable, and portable across tools.