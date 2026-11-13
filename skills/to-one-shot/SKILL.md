---
name: to-one-shot
description: "Plan a code change as approved test scenarios in a one-shot artifact, with no PRD or spec. Use for changes that have no documentation behind them."
argument-hint: "Describe the change you want. Name the files or behavior involved if you know them."
disable-model-invocation: true
---

# Undocumented change to an approved plan

Turn a change request into approved test scenarios in a one-shot artifact. The only plan is the test scenarios. There is no design document, class inventory, or file allow-list.

Prerequisite: a change request in your words. Optional source material, such as notes or an artifact, is read as evidence, not as approved requirements.

Number each point in chat so the user can reply to it.

## 1. Understand the request

Read the code and tests the request touches, and the project rules named in the project profile that apply. Ask a numbered question only when the request is ambiguous or conflicts with the code. Do not invent behavior the user did not ask for.

Done when you can state the requested behavior and the existing behavior it must not break.

## 2. Write the plan

Create `to-one-shot/<slug>.md` from [the template](./references/one-shot-artifact-template.md). It contains:

1. The request, in the user's terms.
2. An open scope, plus anything the user excluded.
3. Test scenarios, decided and written by the [test-scenarios](../test-scenarios/SKILL.md) skill.
4. Existing behavior to preserve.
5. Links to the repository rules the coder must follow.

The template follows the artifact contract in the `coder` agent. Keep its headings.

Do not add a class inventory, call tree, or file list. Use names that already exist in the code.

## 3. Refine and get approval

Follow the presenting and approval rules in [test-scenarios](../test-scenarios/SKILL.md). The user may go through as many rounds as needed.

Done when the user has explicitly approved the scenarios.

## 4. Hand off

Tell the user the artifact path. Do not start any other workflow or agent. The user decides what to do with the artifact.

If the user later wants to refine the scenarios, update this artifact in place and get approval for the changed scenarios as in step 3.

Done when the user has the artifact path.

## Boundaries

- Do not edit production or test code.
- Do not create branches, commit, push, or open a pull request.
- Do not start any other workflow or agent.
- Preserve unrelated working-tree changes.