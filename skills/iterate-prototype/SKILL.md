---
name: iterate-prototype
description: "Run the next trial on an existing prototype, or close it with an assessment. Extends or rewrites the prototype through the coder agent on its unmerged branch, logs each trial, and records findings and the reuse decision. Use with the slug of an existing prototype artifact."
argument-hint: "Give the prototype slug. Name the feedback signal or the idea to try next, or say the assessment is done."
disable-model-invocation: true
---

# Iterate a prototype

Run one trial at a time on an existing prototype, then record the result. When the user declares the work done, write the assessment. The `coder` agent writes the code on the prototype branch. The skill records evidence. It does not decide whether the prototype becomes production code.

Prerequisite: a slug with an existing `to-prototype/<slug>.md`. If the file is missing, tell the user and stop. Do not create one.

Number each point in chat so the user can reply to it.

## 1. Load the prototype

1. Read the artifact: accepted expectations, expectation history, trial log, and assessment. The layout is in [the artifact template](../to-prototype/references/prototype-artifact-template.md).
2. Read the prototype branch and the code the next trial touches.
3. Check out the recorded branch. If the working tree has unrelated changes that block the switch, tell the user. Do not discard them.
4. If the assessment status is `done`, tell the user and ask whether to reopen it. Do not append trials to a closed assessment otherwise.

## 2. Propose one trial

Propose exactly one trial. Do not run several in one step.

1. **Type.** `feedback` when the user named a signal, such as a failing check, metric or comment. The trial is a targeted correction for that signal. `iteration` when it is an exploratory idea. If the user gives no signal, the type is `iteration`.
2. **Change.** Say whether it extends the existing prototype code or rewrites part of it. A rewrite is still a new commit on the same branch.
3. **Signal or hypothesis.** The named signal, or the idea being explored.
4. **Test scenarios.** Decide and write them following the [test-scenarios](../test-scenarios/SKILL.md) skill. The source of truth is the accepted expectations and, for a `feedback` trial, the named signal.

Append the trial to the log with coder status `pending`. Present the trial and its scenarios and get approval as the test-scenarios skill describes, updating the same entry in place.

Do not run the trial until the user explicitly approves it.

## 3. Run the trial through the `coder` agent

Build a brief from [the trial brief template](../to-prototype/references/trial-brief-template.md). Copy the current accepted expectations into it. Delegate to the repository's `coder` agent. Pass the brief and include the artifact path.

1. On `Status: pass`, `fail` or `blocked`, show the report. A `fail` or `blocked` trial is a valid result. Do not re-invoke `coder`, change scenarios, or weaken a test unless the user says so.
2. If `coder` or delegation is unavailable, tell the user. Do not write the implementation yourself.

## 4. Record the trial

1. Show the files `coder` created and updated. Commit them to the prototype branch only after the user approves the exact files and message. Stage only those files. Add a new commit. Never amend, reset or force-push. Record the commit sha, or `None` if nothing was committed.
2. Complete the trial entry: change made, coder status, result in observed terms, and effect on expectations. For an `iteration` trial, record what happened and do not claim it helped.
3. If the result suggests an expectation change, state it as a proposal. Change the accepted expectations and append a history entry that cites the trial only after the user approves. Otherwise write `None` under effect on expectations.
4. Never edit an earlier trial entry or history entry. Correct a mistake with a note in the current entry.

Then ask what the user wants next. Do not propose the next trial until the user answers.

Done when the trial entry is complete and the user has said what happens next.

## 5. Write the assessment

Do this only when the user declares the work done.

1. Fill the artifact's Assessment section. Keep observations, inferences and open questions in separate lists. Cite the trial behind each observation. Do not turn an inference into an observation.
2. Ask the user for the reuse decision: `findings only` or `findings and prototype code`. If the user chooses code, list the code paths on the prototype branch. Do not recommend or decide it for them.
3. Show the assessment and apply the user's corrections in place. Set its status to `done` when the user confirms.

Tell the user the artifact path and the branch name. Do not start any other workflow or agent. The user decides what to do with the findings.

Done when the user has declared the assessment done.

## Boundaries

- Do not edit production or test code yourself.
- Do not commit, push, or open a pull request without the user's approval of the exact files and message.
- Do not merge the prototype branch.
- Never weaken or remove a test to get a pass.
- Exploratory results never change the accepted expectations without the user's approval.
- Preserve unrelated working-tree changes.