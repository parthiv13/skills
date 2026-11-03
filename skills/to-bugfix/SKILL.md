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

1. Read the relevant implementation and tests. Identify the observable behavior to assert and the smallest input that can exercise the suspected condition.
2. Add a new test in the nearest established test location. Follow the testing guide in the project profile and run it with the project's test command. Do not modify production code or existing test files. If reproduction requires either, stop and explain the blocker.
3. Check that the result reflects the reported behavior, not a bad test, unrelated failure, or environment problem. Record the test path, run configuration or command, result, and relevant output.
4. If the conclusion is `confirmed`, keep the test. It fails on the current code and is the acceptance check for the fix. Otherwise remove only the new test file after recording its result. Never remove or alter pre-existing work.

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

Then create `to-bugfix/<slug>-coder-task.md` from [the coder task template](./references/direct-bugfix-template.md). Fill every section using the `coder` agent's slice-artifact structure. Include the confirmed behavior, triggering condition, requested outcome, exact allowed files, code flow, the kept reproduction test, the approved regression-test scenarios, and behavior to preserve. Keep it self-contained: it must not need a story or master plan.

Do not create a fix task artifact for a contradicted, not-reproduced, or inconclusive bug.

Tell the user the artifact paths. For a confirmed bug, also say the reproduction test was left in the working tree and fails until the bug is fixed. Do not start any other workflow or agent; the user decides what to do with the artifacts.

Done when the diagnosis artifact is complete and, for a confirmed bug, the regression scenarios are approved and the fix task artifact is complete.

## Boundaries

- Do not edit production code or create a pull request.
- Do not weaken, rewrite, or remove pre-existing tests.
- Preserve unrelated working-tree changes.