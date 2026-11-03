---
name: adr
description: "Record an Architecture Decision Record in docs/adr/. Use when a significant architectural decision has been made or proposed: choosing a framework, library or major dependency, designing a data model or database schema, choosing between competing approaches, shipping a feature that changes user-facing behavior, or any decision that is expensive to reverse. Do not use for obvious code, comments that restate code, or throwaway prototypes."
argument-hint: "Describe the decision, or point to the discussion, analysis or artifact that produced it."
---

# Record an architecture decision

This is a utility skill. It records a decision that someone has already reached or proposed. It does not make the decision. A caller, either another skill or the user, supplies the decision and its context. The output is one Markdown file in `docs/adr/`, written from [adr-template.md](references/adr-template.md).

Read [writing-agent](../writing-agent/SKILL.md) and [unslop](../unslop/SKILL.md) before drafting.

## When to write an ADR

1. Choosing a framework, library, or major dependency.
2. Designing a data model or database schema.
3. Choosing between competing approaches.
4. Shipping a feature that changes user-facing behavior.
5. Any decision that would be expensive to reverse.

## When not to write one

1. Obvious code. The code is the record.
2. Comments that restate what the code already says.
3. Throwaway prototypes.

## Common rationalizations

| Excuse | Reply |
| --- | --- |
| "Nobody reads docs." | Agents do. Future engineers do. Your self three months from now does. |
| "ADRs are overhead." | A 10-minute ADR prevents a 2-hour debate about the same decision six months later. |

## Lifecycle

```
PROPOSED -> ACCEPTED -> SUPERSEDED
                     -> DEPRECATED
```

SUPERSEDED means a newer ADR replaced this one. DEPRECATED means the decision was dropped with no replacement.

## Workflow

1. **Check that it deserves an ADR.** Apply the two lists above. If the decision fails them, say so and stop. Done when the decision matches at least one "when to write" item and no "when not to" item, or the user has been told no ADR is needed.

2. **Gather the context.** Use the caller's material, the conversation, and any analysis artifact. Read the existing files in `docs/adr/` to find related or superseded decisions and to avoid duplicates. Ask only for what is missing: options considered, why each was rejected, deciders, and cost to reverse. Do not invent options or rationale. Done when every template section has sourced content or a labeled open question.

3. **Name the file.** The filename is `YYYY-MM-DD-HHMM-kebab-case-title.md`, where the date and time are the moment of drafting in UTC. Use `HHMM`, with no colon, so the name is valid on Windows. The filename without `.md` is the ADR's identifier. It never changes, even when `Date` changes on acceptance. There is no sequence number, so parallel branches do not collide on order. Create `docs/adr/` if it does not exist. If the path already exists, choose a more specific title. Never overwrite an existing ADR. Done when the path is unique and matches the pattern.

4. **Draft with status PROPOSED.** Copy the template, fill every section, and omit the optional header lines that do not apply. Set `Date` to the date of the current status. Write the decision in active voice. State costs and risks as plainly as gains. Link evidence to code with workspace-relative links. Save the file, link it in chat, and keep the summary short. Update the same file in place after feedback. Done when the file is saved with no template placeholders left and the user has a link to review.

5. **Accept only on the user's word.** Change `Status` to `ACCEPTED` and update `Date` only after the user says the decision is accepted. Do not commit or push. Done when the status matches what the user said.

6. **Supersede or deprecate.** To supersede, write a new ADR with `Supersedes: <identifier of the old ADR>`. In the same change, set the old ADR's `Status` to `SUPERSEDED` and add `Superseded by: <identifier of the new ADR>`. To deprecate, set `Status` to `DEPRECATED` and give the reason under Notes. After acceptance, change only the status, date, and supersession lines of an existing ADR. Done when both files agree about the link.