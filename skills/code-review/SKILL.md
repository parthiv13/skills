---
name: code-review
description: "Review a code change for correctness, readability, architecture, security, and performance, and report severity-labelled findings with a verdict. Use when asked to review code changes, committed or not. Read-only: it reports findings and does not fix them."
argument-hint: "Optional: the changes to review, and the task or spec they implement. Defaults to every change on the current branch, committed or not."
---

# Code review

Review one change and report whether it improves the health of the codebase. You do not edit code. You report findings, each with evidence and a remedy, and a verdict.

## Standard

Approve a change when it definitely improves overall code health, even if it isn't perfect. Perfect code doesn't exist — the goal is continuous improvement. Don't block a change because it isn't exactly how you would have written it. If it improves the codebase and follows the project's conventions, approve it.

Code health is judged on the code the change touches, not only on the lines it adds. A small addition to bad code leaves bad code. When the change edits existing code that is hard to read, duplicated, or badly structured, the change must leave that code clean.

## 1. Gather the change

Review the changes the user points to. If they point to none, review every change on the current branch since it left the default branch named in the project profile, committed or not, including new files.

If a task or spec is given, read it. It is the source for the intent and the requirements under Correctness.

Read the guides the project profile names that a diff can break: code conventions, language conventions, naming conventions, testing guide, design guide, structure guide, and engineering principles. A blank line means the project has no such guide.

Done when you have the full diff, the task if one is given, and the guides.

## 2. Understand the context

Before looking at code, understand the intent:

- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behavior change?

Take the intent from the task or spec when one is given, otherwise from the commit messages. If there are none, ask the user for it.

Done when you can state the intent in one sentence. If the commit messages exist and do not let you, that is a finding against them.

## 3. Review the tests first

Tests reveal intent and coverage:

- Do tests exist for the change?
- Do they test behavior, not implementation details?
- Are edge cases covered?
- Do tests have descriptive names?
- Would the tests catch a regression if the code changed?

Answer the last question by experiment, not by reading. Copy the file, invert one condition the change adds (drop a negation, swap `&&` for `||`), run the narrowest test command that covers it, then restore the file from the copy. A mutation that stays green is a finding: name the test case that is missing.

Done when each new or changed behavior is matched to a test, or listed as untested, and the file you mutated is restored.

## 4. Review the implementation

Walk through each changed file, and enough of the code around it to follow the call path, against the five axes under [Things to check](#things-to-check). Check every function or block the change edits, not only the added lines: if it was already bad, the change must refactor it.

Done when every changed file has been read against all five axes.

## 5. Categorize findings

Before reporting a finding, check it against the callers, guards and tests that reach the changed code, and drop it if they rule it out. Zero findings is a valid result; do not invent one.

Label defects that already exist in the base separately from regressions this change introduces. A pre-existing defect in code the change touches is still a required change: the change must refactor it. A pre-existing defect in code the change does not touch is **FYI**.

Label every finding with its severity so the author knows what's required vs optional:

| Prefix | Meaning | Author Action |
|--------|---------|---------------|
| *(no prefix)* | Required change | Must address before merge |
| **Critical:** | Blocks merge | Security vulnerability, data loss, broken functionality |
| **Nit:** | Minor, optional | Author may ignore — formatting, style preferences |
| **Optional:** / **Consider:** | Suggestion | Worth considering but not required |
| **FYI** | Informational only | No action needed — context for future reference |

This prevents authors from treating all feedback as mandatory and wasting time on optional suggestions.

**Lead with what matters.** Order findings by leverage: correctness and security first, then structural regressions and missed simplifications, then everything else. Don't bury a real issue under cosmetic nits — a few high-conviction comments beat a long list. If you have one structural problem and ten nits, the structural problem *is* the review.

When you flag a structural problem, propose the move from [Remedies](#remedies), not just the problem.

## 6. Verify the verification

Check the author's verification story:

- What tests were run?
- Did the build pass?
- Was the change tested manually?
- Are there screenshots for UI changes?
- Is there a before/after comparison?

Done when each item is answered from evidence in the change, its description, or a command you ran, or listed as missing.

## 7. Report

Report in this shape:

```
Reviewed: [base]..[head], [number] files
Intent: [one sentence]
Verdict: approve | request changes
Findings:
1. [prefix] [file:line] — [problem]
   Evidence: [short exact quote, or the input and the wrong result]
   Remedy: [the move]
Untested behavior: [list] or none
Verification gaps: [list] or none
Not checked: [axis or guide: reason] or none
```

Number findings so the author can answer each by number. `Verdict: approve` means no required or **Critical:** finding is open.

## Things to check

### 1. Correctness

- Does it match the spec or task requirements?
- Are error paths handled (not just the happy path)?
- Does it pass all tests? Are the tests actually testing the right things?
- Are there any state inconsistencies?

### 2. Readability

- Are names descriptive and consistent with project conventions? (No `temp`, `data`, `result` without context)
- Is the control flow straightforward (avoid nested ternaries, deep callbacks)?
- Is the code organized logically (related code grouped, clear module boundaries)?
- **Could this be done in fewer lines?** (1000 lines where 100 suffice is a failure)
- **Are abstractions earning their complexity?** (Don't generalize until the third use case)
- Would comments help clarify non-obvious intent? (But don't comment obvious code.)
- Are there dead code artifacts: no-op variables (`_unused`), backwards-compat shims, or `// removed` comments?
- **Is a new conditional bolted onto an unrelated flow?** That's a design smell, not a nit — push the logic into its own helper, state, or policy instead of tangling an existing path.
- **Do repeated conditionals on the same shape appear?** They signal a missing model or dispatcher. A "temporary" branch is usually permanent debt.
- **Does the change add to existing code that is already bad?** Then the change refactors that code first. A clean addition to a messy function is still a messy function.

### 3. Architecture

- Does it follow existing patterns or introduce a new one? If new, is it justified?
- Does it maintain clean module boundaries?
- Is there code duplication that should be shared?
- Are dependencies flowing in the right direction (no circular dependencies)?
- Is the abstraction level appropriate (not over-engineered, not too coupled)?
- **Does this refactor reduce complexity or just relocate it?** Count the concepts a reader must hold to follow the change. If a "cleaner" version leaves that count unchanged, it isn't cleaner — prefer the restructuring that makes whole branches, modes, or layers disappear over one that re-centralizes the same logic. Prefer deleting an abstraction to polishing it.
- **Is feature-specific logic leaking into a shared or general-purpose module?** Keep logic in its owning layer, reuse the existing canonical helper instead of a near-duplicate, and don't normalize architectural drift.
- **Are type boundaries explicit?** Question gratuitous `any`/`unknown`/optional/casts and silent fallbacks that paper over an unclear invariant — making the boundary explicit often makes the surrounding control flow simpler.

### 4. Security

- Are secrets kept out of code, logs, and version control?
- Are dependencies from trusted sources with no known vulnerabilities?
- Are external data flows validated at system boundaries before use in logic or rendering?

### 5. Performance

- Any N+1 query patterns?
- Any unbounded loops or unconstrained data fetching?
- Any synchronous operations that should be async?
- Any large objects created in hot paths?

### Dead code

After any refactoring or implementation change, look for code that is now unreachable or unused. List each element in one finding. Do not delete it yourself.

### Change description

Every change needs a description that stands alone in version control history.

- **First line:** Short, imperative, standalone. "Delete the FizzBuzz RPC" not "Deleting the FizzBuzz RPC." Must be informative enough that someone searching history can understand the change without reading the diff.
- **Body:** What is changing and why. Include context, decisions, and reasoning not visible in the code itself. Link to bug numbers, benchmark results, or design docs where relevant. Acknowledge approach shortcomings when they exist.
- **Anti-patterns:** "Fix bug," "Fix build," "Add patch," "Moving code from A to B," "Phase 1," "Add convenience functions."

## Change sizing

Small, focused changes are easier to review, faster to merge, and safer to deploy. If a change is too large to review well, ask the author to split it rather than reviewing one massive changeset.

**Watch file size, not just diff size.** A small diff can still push a file past a healthy boundary — around 1000 total lines in a single file is a common inspection signal, not a hard cap. When a change materially grows an already-large file, ask whether to extract helpers, subcomponents, or modules *first*, before piling more on. Decompose, then add.

**What counts as "one change":** A single self-contained modification that addresses one thing, includes related tests, and keeps the system functional after submission. One part of a feature — not the whole feature.

**Splitting strategies when a change is too large:**

| Strategy | How | When |
|----------|-----|------|
| **Stack** | Submit a small change, start the next one based on it | Sequential dependencies |
| **By file group** | Separate changes for groups needing different reviewers | Cross-cutting concerns |
| **Horizontal** | Create shared code/stubs first, then consumers | Layered architecture |
| **Vertical** | Break into smaller full-stack slices of the feature | Feature work |

**When large changes are acceptable:** Complete file deletions and automated refactoring where the reviewer only needs to verify intent, not every line.

**Refactor first, then add.** Refactoring the touched code is required, not optional. Keep it a separate step from the new behavior: the refactor lands first, as its own commit or stacked change that preserves behavior, and the feature lands on top. Small cleanups (variable renaming) can ride along with the feature.

## Remedies

When you flag a structural problem, propose the move — not just the problem. A review that only says "this is complex" leaves the author guessing. Reach for a named restructuring:

- **Replace a chain of conditionals** with a typed model or an explicit dispatcher.
- **Collapse duplicate branches** into a single clearer flow.
- **Separate orchestration from business logic** so each reads on its own.
- **Move feature-specific logic** out of a shared module into the package that owns the concept.
- **Reuse the canonical helper** instead of a bespoke near-duplicate.
- **Extract a helper, or split a large file** into focused modules.

## Handling disagreements

When resolving review disputes, apply this hierarchy:

1. **Technical facts and data** override opinions and preferences.
2. **Software design** must be evaluated on engineering principles, not personal preference.
3. **Codebase consistency** is acceptable if it doesn't degrade overall health.

**Don't accept "I'll clean it up later."** Deferred cleanup rarely happens. Require cleanup before submission unless it's a genuine emergency. If surrounding issues can't be addressed in this change, require filing a bug with self-assignment.

## Honesty in review

When reviewing code — whether written by you, another agent, or a human:

- **Don't rubber-stamp.** "LGTM" without evidence of review helps no one.
- **Don't soften real issues.** "This might be a minor concern" when it's a bug that will hit production is dishonest.
- **Quantify problems when possible.** "This N+1 query will add ~50ms per item in the list" is better than "this could be slow."
- **Push back on approaches with clear problems.** Sycophancy is a failure mode in reviews. If the implementation has issues, say so directly and propose alternatives.
- **Accept override gracefully.** If the author has full context and disagrees, defer to their judgment.

## Rationalizations

| Rationalization | Reality |
|---|---|
| "It works, that's good enough" | Working code that's unreadable, insecure, or architecturally wrong creates debt that compounds. |
| "I wrote it, so I know it's correct" | Authors are blind to their own assumptions. Every change benefits from another set of eyes. |
| "We'll clean it up later" | Later never comes. The review is the quality gate — use it. Require cleanup before merge, not after. |
| "AI-generated code is probably fine" | AI code needs more scrutiny, not less. It's confident and plausible, even when wrong. |
| "The tests pass, so it's good" | Tests are necessary but not sufficient. They don't catch architecture problems, security issues, or readability concerns. |
| "The refactor makes it cleaner" | Relocating complexity isn't reducing it. If the reader still holds the same number of concepts, the structure didn't improve — look for the version where branches disappear. |
| "It's only a small addition to this file" | Small diffs still push files past a healthy size and bolt branches onto unrelated flows. Judge the resulting structure, not the diff size. |
| "The existing code was already bad; I'm only adding a small change to it" | Touching it makes it yours. Following a bad pattern spreads it, and every small addition makes the eventual cleanup bigger. If the code you touch is bad, refactor it clean first, then add the change. |
| "It's just a version bump" | A bump is a behavior change you didn't write. Read the changelog; semver doesn't guarantee no breakage. |
| "I'll upgrade everything in one PR to save time" | A bulk bump that breaks the build hides which package did it. One dependency per change keeps the cause and the revert clean. |
| "I should report something, or the review looks thin" | Zero findings is a valid result. |

## Boundaries

- Do not edit code to fix a finding. The only edit allowed is the mutation in step 3, restored before you report.
- Do not post comments to the code host. Report in chat.
- Do not name a successor skill or agent.
