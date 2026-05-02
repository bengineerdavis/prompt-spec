---
id: meeting-001
title: Meeting 1 — PREPs, advisory roles, and meeting kit design
description: Minutes for the first prompt-spec advisory session covering PREPs, document topology, role design, meeting governance, and reusable meeting artifacts.
version: 0.1.0
type: documentation
license: MIT
context:
  include: true
x-meeting:
  session: prompt-spec advisory panel
  date: 2026-05-02
  roles:
    chair: ben
    facilitator: guide
    note_taker: log
    advisors:
      - prag
      - zen
      - mesh
    specialists:
      - scout
      - draft
      - match
  chair_preferences:
    structural_changes_roles: once
    structural_changes_norms: always
---

# Meeting 1 — PREPs, advisory roles, and meeting kit design

## 1. Session Goal

- Clarify whether `prompt-spec` should adopt a lightweight proposal process inspired by PEPs and Oxide RFDs.[cite:2]
- Review the current project shape and decide how a document-first collaboration model should fit the repo.[cite:2]
- Design a reusable meeting kit for future advisory sessions, including roles, rules, minutes, and a bundling workflow.[cite:101][cite:103]

## 2. Agenda (Planned vs Actual)

**Planned agenda:**

1. Learn from PEPs and RFDs and decide whether PREPs are needed.[cite:2]
1. Review `prompt-spec` and identify document-topology and rule-portability questions.[cite:2]
1. Establish advisor roles, meeting governance, and reusable artifacts for future sessions.[cite:101][cite:103]

**Actual flow:**

- The discussion began with PEPs, RFDs, and the current `prompt-spec` repository structure.[cite:2]
- The conversation expanded into role design, governance, minutes, preferences, aliases, and packaging the meeting system as reusable prompts and templates.[cite:101][cite:103]
- The session ended with a plan to adjourn, preserve context in minutes, and move the resulting meeting kit into a `meetings/` subdirectory suitable for a prompt library.[cite:101][cite:103]

## 3. Key Decisions

| ID  | Decision                                                                                                                                                            | Rationale                                                                                                                                                              | Related Sections      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| D1  | PREPs will be treated as lightweight proposal documents for rules, families, and process changes rather than a full copy of the PEP or RFC process.                 | The project needs traceable discussion and decision records without prematurely adopting heavy governance.[cite:2]                                                     | §6 Q1, §6 Q2, §7 DR-1 |
| D2  | `prompt-spec` should continue to focus first on raw prompt files (`.txt` / `.md`) before expanding to harness formats like `AGENTS.md` or `CLAUDE.md`.              | This keeps early portability claims narrow and honest while the rule catalog is still in alpha.[cite:9][cite:69]                                                       | §6 Q3, §7 DR-2        |
| D3  | The meeting system should have explicit support roles in addition to advisors: Facilitator, Note-Taker, Deep Researcher, Document Architect, and Recruiter.         | The conversation showed a real need to separate flow management, note capture, research, drafting, and role design into distinct responsibilities.[cite:101][cite:103] | §6 Q4, §6 Q5          |
| D4  | Structural changes to the meeting should require Chair consent by default, with the option for the Chair to grant broader one-time permission for repeated changes. | This preserves human control over meeting governance while still allowing smoother operation when the Chair wants to delegate.[cite:101][cite:103]                     | §6 Q5                 |
| D5  | Meeting minutes should use prompt-spec-compatible frontmatter and place meeting-specific metadata under an `x-meeting` extension key.                               | The frontmatter schema allows only the core top-level keys plus `x-*` extensions, so meeting metadata must be nested under `x-meeting` to stay compliant.[cite:2]      | §6 Q6                 |
| D6  | The meeting kit should live in a `meetings/` directory with templates, role prompts, example minutes, preferences, and a CLI bundler.                               | Reusable artifacts make the process easier to run from the command line, system prompts, or direct chat copy-paste.[cite:191][cite:197]                                | §6 Q7                 |

## 4. Deferred Items

| ID  | Item                                                                                      | Why deferred                                                                                                                         | Revisit trigger                                                                |
| --- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ |
| DE1 | Decide which single document becomes the “constitution” that PREPs are submitted against. | The session identified the issue but did not fully resolve whether this should be a renamed `SPEC.md`, a meta-PREP, or another file. | Start of the next substantive prompt-spec design meeting.                      |
| DE2 | Choose the first real PREP from the roadmap or current open design questions.             | The group agreed on the need for PREPs in principle, but not yet on the first concrete proposal to draft.                            | After reviewing the roadmap and current catalog with the meeting kit in place. |
| DE3 | Classify current rules as surface-agnostic vs surface-aware.                              | The need was clear, but the group did not work through the catalog rule by rule in this session.                                     | Next design session focused on portability taxonomy.                           |
| DE4 | Expand from raw prompt files to harness and provider-specific modules.                    | The group intentionally narrowed scope to avoid over-designing the standard too early.[cite:9][cite:69]                              | After the raw-prompt rule catalog stabilizes.                                  |

## 5. Promises / Action Items

| ID  | Owner       | Item                                                                                                                                           | Due / Trigger                   | Status  |
| --- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------- |
| P1  | Chair       | Resume the advisory session later using the minutes as continuity context.                                                                     | Next meeting                    | Open    |
| P2  | Chair       | Review which current roadmap item is the best candidate for the first actual PREP.                                                             | Next design-focused meeting     | Open    |
| P3  | Log / Guide | Keep the meeting format optimized for machine reuse while still being readable to human participants.                                          | Ongoing                         | Open    |
| P4  | Draft       | Turn the meeting kit into reusable files for the prompt library, including role prompts and templates.                                         | Initial packaging pass          | Started |
| P5  | Scout       | Continue surveying adjacent tools and standards to test whether `prompt-spec` fills a real community gap instead of duplicating existing work. | Future research-focused meeting | Open    |

## 6. Round-by-Round Q&A

### Round 1

**Prag asked (Q1):**

> Which document is actually canonical today, given drift across `spec.md`, `CONTRIBUTING.md`, `RULES.md`, and the rule template?

**Chair answered (A1):**

> No single answer exists yet. `spec.md` feels like design intent, `CONTRIBUTING.md` gets into how to build, and `RULES.md` plus the template also matter. The drift is real and needs wrangling.

**Notes:**

- This exposed a document-topology problem rather than a mere wording problem.
- The conversation established that PREPs should not swallow every core project document.
- Related decisions: D1, D5.

______________________________________________________________________

**Zen asked (Q2):**

> What is the smallest useful thing to adopt from PEPs and RFDs without copying the whole process?

**Chair answered (A2):**

> The goal is influence, not imitation. PREPs would be structured enhancement proposals for rules or families of rules, separate from routine fixes and separate from heavy governance.

**Notes:**

- The answer strongly favored YAGNI: adopt the useful behavior, not the entire ceremony.[cite:2]
- This set the working definition of PREPs used for the rest of the session.
- Related decisions: D1.

______________________________________________________________________

**Mesh asked (Q3):**

> What is the actual output of a rule in this standard, and how portable is the current idea across surfaces and frameworks?

**Chair answered (A3):**

> The intended outputs are all three: human-readable warning, machine-parseable lint result, and suggested rewrite. Early work should focus on raw prompt files first; broader surface support and provider modules can come later.

**Notes:**

- This led directly to the raw-prompts-first scope boundary.[cite:9][cite:69]
- It also surfaced the need to classify rules as portable vs surface-aware in a future meeting.
- Related decisions: D2.

### Round 2

**Joint panel question (Q4):**

> How should the meeting itself be structured so future sessions can pick up where this one leaves off?

**Chair answered (A4):**

> The group needs a reusable meeting system with a document-first trail: meeting rules, notes, minutes, and reusable prompts for each role.

**Notes:**

- This prompted the creation of support roles and reusable artifacts.[cite:101][cite:103]
- The meeting became a meta-design exercise for its own future operation.
- Related decisions: D3, D6.

______________________________________________________________________

**Joint panel question (Q5):**

> Who controls changes to the meeting itself, such as adding roles or updating aliases and norms?

**Chair answered (A5):**

> By default, changes that alter the nature of the meeting require Chair approval, though the Chair can optionally grant one-time broader permission if they do not want to be asked repeatedly.

**Notes:**

- This established a governance model for the meeting independent of prompt-spec’s eventual PREP process.[cite:101][cite:103]
- It also created a need for preferences that can be saved and reused across sessions.
- Related decisions: D4.

______________________________________________________________________

**Joint panel question (Q6):**

> How should meeting frontmatter be designed so it stays compatible with prompt-spec’s frontmatter rules?

**Chair answered (A6):**

> The meeting metadata should be nested under `x-meeting` so the files remain compliant with the standard frontmatter conventions.

**Notes:**

- This decision came after explicitly checking the frontmatter rules document and confirming that unknown non-`x-*` keys are invalid.[cite:2]
- Related decisions: D5.

______________________________________________________________________

**Joint panel question (Q7):**

> How should these files be packaged so the meeting system is easy to use outside this specific chat?

**Chair answered (A7):**

> The kit should live under `meetings/` in the prompt library, and there should be a script that bundles the files for command-line use or prompt assembly.

**Notes:**

- The bundler requirement makes the meeting kit operable as infrastructure rather than only as ad hoc chat context.[cite:191][cite:197]
- Related decisions: D6.

## 7. Deep Researcher Briefs

### DR-1: PEPs, RFDs, and lightweight proposal design

**Question:**

> What can be borrowed from PEPs and Oxide’s RFD process to create a lightweight proposal mechanism for prompt-spec?

**Summary:**

- PEP 1 defines PEPs as the mechanism Python uses to propose major features, collect community input, and document design decisions.[cite:2]
- Oxide’s RFD 0001 emphasizes timely writing and structured discussion, including lightweight early stages where ideas do not need to be fully polished.[cite:2]
- The combined lesson is that proposal documents are valuable when they create traceability and honest design records, but they become expensive if required for every small change.[cite:2]

**Implications for prompt-spec:**

- A PREP mechanism can borrow the idea of structured, numbered proposals without copying the full governance stack of Python or a company RFC process.[cite:2]
- PREPs should be scoped to meaningful rule, family, or process changes, not routine edits.

**Sources referenced:**

- PEP 1 — Purpose and Guidelines — [cite:2]

### DR-2: Existing prompt linting tools and the ecosystem gap

**Question:**

> Does a project like AgentLint already solve the problem prompt-spec wants to solve, or is there still a real gap?

**Summary:**

- AgentLint and similar tools focus on linting harness files such as `CLAUDE.md`, `AGENTS.md`, and related agent configuration surfaces.[cite:9][cite:69]
- AgentLint presents an evidence-backed set of checks, but those rules are embodied in the tool rather than published as a standalone, vendor-neutral standard.[cite:9][cite:69]
- This suggests an ecosystem gap between prompt-linting implementations and a shared rulebook that multiple tools could adopt.[cite:9][cite:69]

**Implications for prompt-spec:**

- `prompt-spec` still has room to matter if it positions itself as the portable rulebook beneath tools rather than as another linter competing with them.[cite:9][cite:69]
- The early scope should remain narrow and clearly differentiated: prompt content first, not full harness linting.[cite:9][cite:69]

**Sources referenced:**

- AgentLint product site — [cite:9]
- AgentLint documentation — [cite:69]

### DR-3: Meeting roles, facilitation, and reusable process artifacts

**Question:**

> Is it reasonable to separate Facilitator, Note-Taker, and Deep Researcher into distinct meeting roles and to encode their responsibilities in reusable prompts?

**Summary:**

- Meeting practice literature commonly separates facilitation from note-taking and other support roles to preserve clarity of responsibility and reduce overload.[cite:101][cite:103]
- Structuring roles explicitly improves continuity, especially when the same group expects to resume complex discussions later.[cite:101][cite:103]
- This is particularly useful in mixed human-plus-machine settings where different roles may optimize for flow, memory, or external research.[cite:101][cite:103]

**Implications for prompt-spec:**

- Explicit role prompts are justified as durable process infrastructure, not just stylistic embellishments.[cite:101][cite:103]
- The split between Guide and Log is worth preserving in future sessions.

**Sources referenced:**

- Pumble — 5 Essential Meeting Roles for More Productive Meetings — [cite:101]
- Lucid Meetings — Who Does What in a Collaborative Meeting — [cite:103]

### DR-4: CLI bundling and prompt assembly

**Question:**

> Should the meeting kit include a command-line bundler rather than rely only on copying content into chats or prompts by hand?

**Summary:**

- Python’s `argparse` is the standard library mechanism for building lightweight CLIs with subcommands and composable options.[cite:191][cite:197]
- A small bundler makes it easier to assemble repeatable prompt packages for chat, system prompts, or local review without manually concatenating Markdown files every time.[cite:191][cite:197]
- This turns the meeting kit into portable tooling rather than only a writing pattern.

**Implications for prompt-spec:**

- A bundler belongs naturally alongside the meeting kit if the goal is reuse across multiple contexts and repos.[cite:191][cite:197]
- The CLI should remain small and transparent so it complements, rather than replaces, the Markdown-first workflow.

**Sources referenced:**

- Python docs — `argparse` — [cite:191]
- Python docs — `Argparse Tutorial` — [cite:197]

## 8. Source Index

- **[cite:2]** — PEP 1 — PEP Purpose and Guidelines.
- **[cite:9]** — AgentLint product site.
- **[cite:69]** — AgentLint documentation.
- **[cite:101]** — Pumble — 5 Essential Meeting Roles for More Productive Meetings.
- **[cite:103]** — Lucid Meetings — Who Does What in a Collaborative Meeting.
- **[cite:191]** — Python docs — `argparse`.
- **[cite:197]** — Python docs — Argparse Tutorial.

## 9. Open Questions Carried Forward

- [ ] OQ1: Which document becomes the constitutional document PREPs are submitted against? (related decisions: D1, D5)
- [ ] OQ2: Which roadmap item should become the first actual PREP? (related decisions: D1)
- [ ] OQ3: Which current rules are genuinely surface-agnostic, and which require surface-aware logic? (related decisions: D2)
- [ ] OQ4: How much of the meeting kit should eventually feed back into `prompt-spec` itself versus living only in the prompt library? (related decisions: D6)
