---
name: to-bugfix
description: "Investigate a reported software bug, test the suspected cause with a focused reproduction, and create a diagnosis artifact and, for a confirmed bug, a fix task artifact. Use for bug reports, stack traces, suspected data conditions, and quick bug fixes."
argument-hint: "Describe the bug and provide a stack trace, failing input, or steps to reproduce if available."
disable-model-invocation: true
---

# Verify a bug

Investigate the report and create one diagnosis artifact at `to-bugfix/<slug>.md` from [the template](./references/bugfix-artifact-template.md). For a confirmed bug, the skill also creates a self-contained fix task artifact.

Prerequisite: a bug report. A stack trace, failing input, or reproduction steps help but are optional.

## 1. Record the report

Capture the reported and expected behavior, reproduction details, and assumptions in the user's terms. Do not invent expected behavior.

If the user provides a stack trace, use relevant frames to form a hypothesis about the triggering input or data condition. Mark it as unverified. Keep only relevant frames, redact credentials and personal or customer data, and link to repository files and symbols where possible. Do not copy raw production data into the artifact.

## 2. Reproduce the behavior

1. Create the bugfix branch. Propose a name, `bugfix/<slug>`, and create it only after the user approves the name. Run `git fetch origin`, then branch from the remote default branch (`origin/main` unless the project profile names another), so the bug is reproduced against main. If the working tree is not clean, stop and ask how the user wants to handle it.
2. Read the relevant implementation and tests. Identify the observable behavior to assert and the smallest input that can exercise the suspected condition.
3. Add a new test in the nearest established test location. Follow the testing guide in the project profile and run it with the project's test command. Do not modify production code or existing test files. If reproduction requires either, stop and explain the blocker.
4. Check that the result reflects the reported behavior, not a bad test, unrelated failure, or environment problem. Record the test path, run configuration or command, result, and relevant output.
5. If the conclusion is `confirmed`, keep the test. It fails on the current code and is the acceptance check for the fix. Otherwise remove only the new test file after recording its result, then offer to delete the bugfix branch. Delete it only if the user agrees. Never remove or alter pre-existing work.

Record one conclusion:

| Conclusion       | Meaning                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------- |
| `confirmed`      | The focused test reproduces the reported behavior under the identified condition.             |
| `contradicted`   | Evidence shows the reported claim conflicts with current behavior under the stated condition. |
| `not reproduced` | The test and setup are sound, but the behavior did not occur.                                 |
| `inconclusive`   | Missing data, environment limits, or another blocker prevents a reliable conclusion.          |

A stack trace or unrelated test failure is not proof of a bug. For `contradicted`, `not reproduced`, or `inconclusive`, record why and what evidence could change the conclusion.

## 3. Review and finish the artifacts

Present the diagnosis and evidence to the user. Apply any corrections to the artifact, keeping observations, hypotheses, and conclusions distinct. Do not turn a hypothesis into an established cause.

For a confirmed bug, decide the regression-test scenarios following the [test-scenarios](../test-scenarios/SKILL.md) skill and write them into the "Regression scenarios" section of the diagnosis artifact. The source of truth is the diagnosis. Present them and get approval as that skill describes. Do not create the fix task artifact until the user explicitly approves the scenarios.

Then commit the reproduction test to the bugfix branch. Show the user the exact file and the commit message, for example `Add failing test for <short bug title>`, and commit only after the user approves both. Stage only that file. Do not push. Record the commit sha.

Then create `to-bugfix/<slug>-coder-task.md` from [the coder task template](./references/direct-bugfix-template.md). Fill every section. The template follows the artifact contract in the `coder` agent. Include the confirmed behavior, triggering condition, requested outcome, exact allowed files, code flow, the committed reproduction test, the approved regression-test scenarios, behavior to preserve, and the branch and reproduction commit sha. Keep it self-contained: it must not need a story or master plan.

Do not create a fix task artifact for a contradicted, not-reproduced, or inconclusive bug.

Tell the user the artifact paths. For a confirmed bug, also give the branch and the reproduction commit, and say the test fails until the bug is fixed. Do not start any other workflow or agent; the user decides what to do with the artifacts.

Done when the diagnosis artifact is complete and, for a confirmed bug, the regression scenarios are approved, the reproduction test is committed on the bugfix branch, and the fix task artifact records that branch and commit.

## Boundaries

- Do not edit production code or create a pull request.
- Commit only the reproduction test, and only after the user approves the file and message. Do not push.
- Do not weaken, rewrite, or remove pre-existing tests.
- Preserve unrelated working-tree changes.