---
name: to-prototype
description: "Start a prototype assessment from a goal. Record the accepted expectations, agree test scenarios for the first trial, build it through the coder agent on an unmerged branch, and log the result. Use to explore whether an idea works before committing to production code."
argument-hint: "Describe the goal. Link notes or an artifact as source material if you have them."
disable-model-invocation: true
---

# Start a prototype

Turn a goal into a prototype artifact and a first trial. The prototype code lives on an unmerged branch, and the `coder` agent writes it. The skill records evidence. It does not decide whether the prototype becomes production code.

Prerequisite: a goal in your words. Optional source material, such as notes or an artifact, is read as evidence, not as approved requirements.

Number each point in chat so the user can reply to it.

## 1. Understand the goal

Read the source material, the code the goal touches, and the project rules named in the project profile that apply. Ask a numbered question only when the goal is ambiguous or conflicts with the code. Do not invent expectations the user did not state.

Done when you can state the goal and what the user would accept as a good outcome.

## 2. Create the artifact

Create `to-prototype/<slug>.md` from [the artifact template](./references/prototype-artifact-template.md). Fill:

1. The goal and source material.
2. The accepted expectations, in the user's terms. Show them in chat and get the user's confirmation. Record the confirmed target as history entry 3.1.
3. An empty trial log and an assessment with status `open`.

Leave the branch and base commit blank until step 4.

## 3. Agree the scenarios for trial 1

Trial 1 has no earlier signal, so log it as `iteration`. State the hypothesis it explores.

Write the test scenarios into the trial 1 entry, following the [test-scenarios](../test-scenarios/SKILL.md) skill. The source of truth is the accepted expectations. Present them and get approval as that skill describes, updating the same file in place.

Do not continue until the user explicitly approves the scenarios.

## 4. Create the prototype branch

Propose a branch name, for example `prototype/<slug>`. Create it from the current commit only after the user approves the name. Record the branch name and base commit in the artifact. Preserve unrelated working-tree changes.

## 5. Build trial 1 through the `coder` agent

Build a brief from [the trial brief template](./references/trial-brief-template.md). Delegate to the repository's `coder` agent. Pass the brief and include its artifact path.

1. On `Status: pass`, `fail` or `blocked`, show the report. A `fail` or `blocked` trial is a valid result. Do not re-invoke `coder`, change scenarios, or weaken a test unless the user says so.
2. If `coder` or delegation is unavailable, tell the user. Do not write the implementation yourself.

## 6. Record trial 1

1. Show the files `coder` created and updated. Commit them to the prototype branch only after the user approves the exact files and message. Stage only those files. Record the commit sha, or `None` if nothing was committed.
2. Fill in the trial entry: change made, coder status, result in observed terms, and effect on expectations.
3. If the result suggests an expectation change, state it as a proposal. Change section 2 of the artifact and append a history entry only after the user approves.

Tell the user the artifact path and the branch name. Do not start any other workflow or agent. The user decides what happens next.

Done when the artifact holds the confirmed expectations and a complete trial 1 entry.

## Boundaries

- Do not edit production or test code yourself.
- Do not commit, push, or open a pull request without the user's approval of the exact files and message.
- Do not merge the prototype branch.
- Never weaken or remove a test to get a pass.
- Exploratory results never change the accepted expectations without the user's approval.
- Preserve unrelated working-tree changes.