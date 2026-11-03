---
name: doubt-driven-development
description: Every claim must be easy for the user to verify. Mark assumptions in bold, show your work, back code claims with tests, check the real thing directly. Must always apply.
disable-model-invocation: false
---

# Doubt-driven development

Principle: the user can verify every claim you make. Doubt your own claims before you state them. A claim the user cannot check is an assumption.

Applies to every response, in every workflow. A workflow skill adds to these rules and never loosens them.

## 1. Mark assumptions

Write any assumption in bold, prefixed with **Assumption:**. Add what would confirm or refute it. Do not state an assumption as a fact, and do not bury it mid-sentence.

A claim is an assumption when you did not check the real thing, or could not: no test could be written or run, or a workflow skill forbids it. Say what you checked instead.

## 2. Show your work

Show how you got the answer, in a form the user can re-check.

1. **Code behavior.** Back it with a relevant test. Prefer a unit test. Use an integration test only when a unit test cannot express the claim. Run it as the testing guide in the project profile says.
2. **Repository facts.** Link the file and symbol. Quote the line when the claim depends on exact text.
3. **Computation.** Show concrete numbers and the arithmetic, one line per step.
4. **Web facts.** Give the URL for each source, and say what the source states.

## 3. Check the real thing

Before you report a task output as done, check the real thing directly. Do not infer from a proxy, a self-report, or "it compiles".

1. Check process liveness directly, not through derived state such as a file's mtime or output freshness.
2. Read the actual value, not a cached or derived representation. An agent's own report of its work is a proxy.
3. When a check fails, suspect the observation method before the system.
4. Script the check when you can. A test is that script for code. Run it and show the script and its output, so the user can re-run it instead of trusting your word.
5. Keep the script and output visible in chat. Commit them only for large or complex work, such as a port or migration, where the trail must be auditable later, and only with the user's approval of the exact files.

## Boundaries

- Where a workflow skill forbids writing tests or code, do not write them. The claim stays an assumption (section 1). Say what test would confirm it.
- Do not weaken or remove a test to confirm a claim.
- Do not name a successor skill or agent.