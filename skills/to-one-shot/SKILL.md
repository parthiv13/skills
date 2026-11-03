---
name: to-one-shot
description: "Plan a code change as test scenarios and implement it through the coder agent, with no PRD or spec. Use for changes that have no documentation behind them."
argument-hint: "Describe the change you want. Name the files or behavior involved if you know them."
disable-model-invocation: true
---

# Undocumented change to tested code

Turn a change request into approved test scenarios, then have the `coder` agent implement it. The only plan is the test scenarios. There is no design document, class inventory, or file allow-list.

Prerequisite: a change request in your words. Optional source material, such as notes or an artifact, is read as evidence, not as approved requirements.

Number each point in chat so the user can reply to it.

## 1. Understand the request

Read the code and tests the request touches, and the project rules named in the project profile that apply. Ask a numbered question only when the request is ambiguous or conflicts with the code. Do not invent behavior the user did not ask for.

Done when you can state the requested behavior and the existing behavior it must not break.

## 2. Write the plan

Create `to-one-shot/<slug>.md` from [the template](./references/one-shot-artifact-template.md). It contains:

1. The request, in the user's terms.
2. Test scenarios, decided and written by the [test-scenarios](../test-scenarios/SKILL.md) skill.
3. Existing behavior to preserve.
4. Links to the repository rules the coder must follow.

Do not add a class inventory, call tree, or file list. Use names that already exist in the code.

## 3. Refine and get approval

Follow the presenting and approval rules in [test-scenarios](../test-scenarios/SKILL.md). The user may go through as many rounds as needed.

Do not invoke `coder` until the user explicitly approves the scenarios.

## 4. Implement through the `coder` agent

Delegate to the repository's `coder` agent. Pass the artifact content unchanged and include its path.

1. On `Status: pass`, report the result to the user.
2. On `Status: fail` or `Status: blocked`, show the report and ask what to do. The user may want to refine the scenarios and run `coder` again. Update the artifact in place, get approval for the changed scenarios as in step 3, then re-invoke `coder`. Do not re-invoke `coder`, change scenarios, or weaken a test unless the user says so.
3. If `coder` or delegation is unavailable, tell the user. Do not write the implementation yourself.

## 5. Report

Give the files `coder` created and updated, the tests it ran with results, and anything left unverified.

Done when the user approved the scenarios and `coder` reported `pass`, or a `fail` or `blocked` report was shown to the user.

## Boundaries

- Do not edit production or test code yourself.
- Do not commit, push, or open a pull request without the user's approval of the exact files and message.
- Never weaken or remove a test to get a pass.
- Preserve unrelated working-tree changes.
- Do not start any other workflow or agent.