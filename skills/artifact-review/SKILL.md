---
name: artifact-review
description: "Shared steps for having a fresh-context reviewer check an artifact before the user sees it, and for handling its findings. Use whenever a workflow runs an artifact review."
disable-model-invocation: false
---

# Artifact review

Have the `artifact-reviewer` agent check an artifact before it reaches the user. The reviewer reads with a fresh context and catches rule breaks the author misses. The calling skill sets when the review runs, which rules apply, and what the source of truth is. This skill sets how the review runs and how its findings are handled.

## 1. Run the reviewer

Pass the agent:

1. The artifact paths.
2. The step of the calling skill that just finished.
3. The calling skill's path, as the rules.
4. The source of truth the calling skill names.

## 2. Handle the findings

1. Fix each `violation` finding in the artifact. Do not act on a `decision` finding.
2. If you fixed anything, run the agent once more for the same step, and pass it the first round's `decision` findings as already routed. Do not run a third round.
3. When you present the artifact, add a numbered **Review** list: one line per violation you fixed, each `decision` finding as a question for the user, and each violation still open after the second round. If you think a finding is wrong, keep it in the list with your reason. A clean review is one line: `Review: clean`.
4. If the agent is unavailable or returns `blocked`, tell the user the review did not run, and continue.

## Rationalizations

| Rationalization | Reality |
| --- | --- |
| "It's a small fix; no need to list it." | List every fix. The user can't approve what they can't see. |
| "The decision is obvious, so I'll pick." | Obvious to you is not approved by the user. A `decision` finding goes to the user. |
| "The reviewer is wrong, so I'll drop the finding." | Keep it, with your reason. The user decides. |
| "One more round will get it clean." | Two rounds at most. What is left goes to the user. |
| "The review came back clean, so it's approved." | A clean review is not approval. The gate still needs the user. |
| "The reviewer is unavailable, so I'll reread it myself." | Say the review did not run. Your own reread is not a fresh review. |

## Boundaries

- The review never replaces the user's approval at a gate.
- Do not name a successor skill or agent.
