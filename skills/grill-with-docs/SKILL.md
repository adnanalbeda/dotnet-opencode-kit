---
name: grill-with-docs
description: >
  Stress-test a plan against the existing domain model, documented decisions,
  and project language before implementation. Load instead of grill-me when a
  project has CONTEXT.md, ADRs, planning docs, or other local documentation.
---

# Grill With Docs

## Core Principles

1. **Read docs before grilling** - Check `CONTEXT.md`, `docs/adr/`, `knowledge/decisions/`, and relevant planning docs before asking questions.
2. **One question at a time** - Do not dump a questionnaire. Resolve each branch before moving deeper.
3. **Recommend an answer** - Every question includes the agent's recommended answer and rationale, grounded in repo docs when possible.
4. **Use the question tool for choices** - When selectable answers are needed and the client has a question/choice tool, use it instead of writing prose options.
5. **Update docs as decisions crystallize** - If the user confirms a durable decision, propose or apply a focused doc update in the appropriate local file.

## Documentation Sources

Inspect the smallest relevant set before asking:

| Need | Read First |
|------|------------|
| Domain language | `CONTEXT.md`, `docs/context.md`, feature docs |
| Architecture decisions | `docs/adr/`, `knowledge/decisions/` |
| Planning state | `docs/planning/prds/`, `docs/planning/issues/` |
| Project instructions | `AGENTS.md`, `.codex/AGENTS.md`, `CLAUDE.md` |

If no relevant docs exist, fall back to repo inspection and still ask one focused question at a time.

## Question Loop

Use this shape repeatedly:

```text
Finding: ADR-001 says new workflows default to Vertical Slice Architecture.

Question: Should this feature stay in one vertical slice or become a shared platform capability?

Recommended answer: Keep it as a vertical slice because the documented default is feature-local ownership until reuse is proven.

Choose: vertical slice / shared platform / defer
```

After the user answers, update decision state and ask the next highest-leverage question.

## Question Tool Contract

When a question has clear choices, use the client's question tool with the recommended option first:

```text
Question: Where should this decision be recorded?
Options:
1. Update existing ADR (Recommended) - This changes an established architectural decision.
2. Add planning note - Useful if the decision is feature-scoped only.
3. Do not document - Only appropriate for throwaway decisions.
```

If the client lacks a question tool, use the same structure as a concise numbered list.

## Documentation Update Contract

When a confirmed decision changes project understanding:

1. Identify the right local doc (`CONTEXT.md`, an ADR, `knowledge/decisions/`, or planning doc).
2. Make the smallest correct edit.
3. Preserve existing terminology and formatting.
4. Do not create remote issues or publish docs unless explicitly requested.

## Decision Summary

Finish with:

```markdown
## Decisions
- [Decision] because [doc/code reason]

## Docs Updated
- [File] - [decision captured]

## Risks
- [Risk] -> [mitigation]

## Still Open
- [Question needing user]

## Next Step
Run `/plan` or proceed with [specific action].
```

## Anti-patterns

| Anti-pattern | Correct Behavior |
|--------------|------------------|
| Asking before reading docs | Inspect relevant docs first, then ask the remaining question |
| Generic terminology | Use the project's documented domain language |
| No recommendation | Recommend the answer best supported by docs and code |
| Forgetting to capture decisions | Update or propose updates to local docs |
| Questionnaire dump | Ask one focused question at a time |
