---
name: test-scenarios
description: "Shared rules for deciding, writing, presenting, and getting approval for test scenarios. Use whenever a workflow needs test scenarios the user will review before code or tests are written."
disable-model-invocation: false
---

# Test scenarios

The scenarios a workflow shows the user, and the way it gets them approved. The calling skill sets the artifact, the audience and what happens after approval. This skill sets how scenarios are thought through, written and approved. Change a rule here and every calling skill follows it.

Read the testing guide named in the project profile for rules on writing tests. This skill covers scenarios, not test code.

Number each point in chat so the user can reply to it.

## 1. Decide the scenarios

1. Read the code and the existing tests for the area first. Name only classes, methods and types that exist, or that the calling skill marks as new.
2. List scenarios by production class, emphasizing unit tests.
3. Cover the important acceptance paths, boundary cases and relevant failure behavior.
4. Do not write exhaustive scenarios for every method. Do not repeat the same scenario across classes without a reason. Say the reason when you do.
5. Existing behavior that must not change is not a scenario. Record it separately in the calling skill's artifact.

## 2. Write each scenario

1. Give each scenario a numeric ID.
2. State the input condition and the observable result. Write both in the code's own vocabulary: the method, the input, and the result type or side effect involved. When the result is a side effect on a collaborator, name the call and its exact arguments.
3. When the behavior computes something, use concrete numbers. Show the input and the expected output as values, not prose.
4. When the computation takes more than one step, show the working as arithmetic, one line per step. Use symbols and numbers, not sentences.
5. Use the core columns: ID, class or area, and behavior to verify. The calling skill's template may add a column, such as evidence.
6. If a scenario needs a name that does not exist in the code, ask the user. Do not invent it.
7. When a source of truth exists, such as a story's acceptance criteria, a diagnosis, or the user's stated goal, point to it by ID or section. Do not copy its text.

Illustrative only; these names are not from this repository:

| ID  | Class or area | Behavior to verify                                                                        |
| --- | ------------- | ----------------------------------------------------------------------------------------- |
| 2.1 | `Fee.total`   | `total(amount=200, rate=0.05, min_fee=15)` → `15`. 200 × 0.05 = 10; 10 < 15 → min_fee    |
| 2.2 | `Fee.total`   | `total(amount=400, rate=0.05, min_fee=15)` → `20`. 400 × 0.05 = 20; 20 ≥ 15 → rate       |

## 3. Keep it terse

Nothing in a scenario list is verbose. This covers scenarios, mismatch notes, and the chat presentation.

1. One row per scenario. Put the working in the same row, after the result, only for a computation.
2. No preamble, no restating the code, no filler words.
3. A mismatch note is the scenario ID, the source pointer, and one line on the difference and its impact.

## 4. Report mismatches

Compare each scenario with its source of truth. If a scenario and the source disagree, or the source leaves behavior unspecified, note it as "Keep it terse" describes. Do not resolve it on the user's behalf. The user decides whether to keep the scenario or change the source.

## 5. Present and get approval

1. Show the scenarios in chat as a numbered list, with any mismatches beside them, and link the artifact that holds them. Keep the list terse.
2. Ask the user to review them.
3. After each round of feedback, update the same file in place. Do not create versioned copies. Re-read the code when feedback changes the scope. Show only what changed and ask again.
4. Do not treat silence, a request to continue, or partial feedback as approval. Only an explicit approval of the scenarios counts.
5. A scenario changes after approval only when the user asks. Show the change and get approval again.

Done when the user has explicitly approved the scenarios. The calling skill decides what happens next.

## Boundaries

- Do not write test or production code.
- Do not weaken or drop a scenario to make later tests pass.
- Do not name a successor skill or agent.