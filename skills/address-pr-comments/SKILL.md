---
name: address-pr-comments
description: "Triage and work through review comments on a GitHub pull request. Identify repeated or superseded comments, batch simple no-drift fixes, and handle medium to big fixes one at a time. Use when addressing PR review comments, reviewer threads, or #TODO replies, or when deciding which comments need a code change, a story change, or only a reply."
argument-hint: "Optional PR number or URL. Defaults to the pull request of the current branch."
---

# Address PR review comments

Reviewers who did not write the code leave comments. The author replies in the thread. A reply tagged `#TODO` means act on that comment. Other threads may end with no change. Work through the `#TODO` comments by group: batch the simple fixes, and handle each medium to big fix on its own. Keep an artifact for each non-trivial item, so any item can be resumed later from its artifact.

Apply the `writing-agent` skill and its `unslop` rules to every message in this workflow. Number each point in chat so the user can reply per item.

## 1. Collect the threads

1. Identify the PR. Use the number or URL the user gave. Otherwise run `gh pr view --json number,url,headRefName`.
2. Fetch every review thread with `gh api graphql`. Request each thread's `id`, `isResolved`, `path`, `line`, and its comments with author and body. Read the user's replies in full. The `#TODO` tag appears in those replies.
3. Build the action set. It holds the unresolved threads where the user replied with `#TODO`. If the user names another tag, use that tag instead. Leave out resolved threads and threads without the tag.
4. Show the action set as a numbered list. Each item gives the thread id, `path:line`, the reviewer's point in one line, and the user's `#TODO` reply. Show the out-of-scope threads separately, with a short reason each.

Done when the user has confirmed the action set.

## 2. Classify each comment

Each in-scope comment falls into one of two groups.

1. **Drift.** The comment changes the story or slice: a requirement, behavior, scope, or acceptance criterion. Verify the reviewer's claim before accepting it. Read the artifact section and the code it names, and state whether the claim holds, with file references.
2. **No drift.** The comment is a missed small fix, a better design, a small code improvement, or a better name. The story stays as it is.

Within no drift, group by effort. These definitions are a starting point, and the user may change them.

- **Simple.** A name change, a rename of a string or doc term, or a local fix of a few lines. The change may touch several locations, but it changes no signature shape beyond the rename and adds no class or method.
- **Medium to big.** A new or moved class or method, changed responsibilities or data flow, a changed signature beyond a rename, or new tests for changed behavior.
`
Compare the in-scope comments before planning edits. If two ask for the same change, keep both threads in the action set but plan the work once. For each repeat, name the primary thread and the exact planned edit or artifact that covers it. Do not group comments that share a topic but ask for different behavior. Present the classification and these relationships as a numbered list. Get the user's confirmation before any change.

Done when every in-scope comment has one group and one effort level, repeats point to the work covering them, and the user has confirmed the classification.

## 3. Artifacts

Keep artifacts under `to-spec/pr-reviews/<pr-number>/`. That directory must be git-ignored, so artifacts never enter a commit. Before the first write, check `.gitignore` and add the entry if it is missing.

1. Create one artifact per medium to big change. Name it `<thread-number>-<short-slug>.md` using the primary thread number. List any repeated threads it covers in that artifact; do not create separate implementation artifacts for them.
2. Put all simple no-drift fixes in one artifact, `simple-fixes.md`. It holds one entry per comment; a repeated comment's entry points to the primary entry instead of repeating its planned edits.
3. Skip the artifact for a single trivial fix in one location. Record that fix in the chat summary only.
4. Each artifact holds: the thread ids and `path:line` for all comments it covers, the reviewers' comments quoted, the user's `#TODO` replies, the group and effort, the decision and why, the planned change with files and symbols, the status (`planned`, `in progress`, `done`, or `blocked`), what is done, what is left, the test commands and their results, and open questions. For a comment covered elsewhere, record the covering thread and artifact instead of duplicating the plan.
5. Update the artifact when its status changes, so the item can resume from it.
6. Keep `index.md` in the same directory. It has one row per in-scope item with its thread ids, group and effort, artifact link, status, and next action. It lists covered threads and out-of-scope threads, and the order of work. Update it whenever an artifact status changes.

Done when every artifact that the workflow requires exists, `index.md` matches the current status, and each artifact matches its status.

## 4. Drift comments

Handle each distinct drift change one at a time. Keep all threads covered by that change in its artifact. Take these steps in order.

1. Verify the claim against the artifact and the code. State whether it holds.
2. If the user accepts the comment as the final decision, propose the story change first. Show the old and new text of each affected artifact section or requirement, with stable numbers. Wait for approval.
3. After approval, update the story: the affected artifact under `to-spec/`, and the master plan if one exists. Never edit the artifact of a merged slice.
4. Only after the story change is approved, discuss the code change. Use the `show-me` skill to show the changed call path and the proposed code shape. Wait for approval.
5. Implement the approved code change. Delegate to the `coder` agent when the change spans more than one file. Rerun the affected tests.

Done when the story and code both match the approved decision and the artifact records both.

## 5. Simple no-drift fixes, in one batch

1. List every simple fix in `simple-fixes.md`. For each one, write the planned edit at each location. Get the user's approval for the whole list.
2. Apply all the approved edits in one pass.
3. For renames, search the code, tests, and docs for the old name to find leftovers. Fix each leftover that the rename covers.
4. Run the tests and the lint command from the project profile, as the testing guide describes. Run them from the repository directory that owns the changed files.
5. Mark each entry in `simple-fixes.md` as `done` with its test result.

Done when every simple entry is `done`, the tests pass, and no old name remains where the rename applies.

## 6. Medium to big no-drift fixes, one at a time

Take each item in its own cycle. Do not batch them.

1. Write the item's artifact with the design. For a substantial design, show it with the `show-me` skill. Wait for the user's approval.
2. Implement the approved design. Delegate to the `coder` agent when the change spans more than one file.
3. Run the affected tests and the lint command. Record the results in the artifact.
4. Show the user the diff summary and the artifact status. Wait for approval before moving to the next item.

Done when the user has approved the item and its artifact is `done`.

## 7. Commit, reply, and resolve, incrementally

1. Show the files and commit message for the approved changes. Commit and push only after the user approves them. Add a `Co-authored-by` trailer as the repository's commit rules require.
2. Reply to each addressed thread with the change and the commit hash, or with the reason no change was made. Reply to one thread at a time, or to a small batch the user has approved.
3. Resolve a thread only after the user approves that item. Use the `resolveReviewThread` mutation with the thread `id`.
4. Do not resolve threads in bulk. Do not reply to or resolve threads without the `#TODO` tag unless the user asks.

Done when each addressed thread has a reply and is resolved only after the user approved it.

## 8. Status and handoff

After each implemented change, compare the remaining in-scope comments with the actual diff and tests. If a change already addresses another comment, tell the user which thread and changed code or artifact covers it, and why no separate edit is needed. If it only partly addresses the comment, say what remains. Update the affected artifacts and get the user's approval before treating a newly covered comment as done or replying to or resolving its thread. Keep every covered thread in the handoff until its own approval, reply, and resolution are complete.

After each step, state what is done and what is left, including repeated or newly covered comments, and name the artifact path that holds the work. When the user returns later, read `index.md` for the PR first, then the linked artifacts, then continue from the first item whose status is not `done`.

## Rules

- Act only on `#TODO` threads unless the user names another tag.
- Never accept a drift claim without verifying it first.
- For drift, change the story before the code.
- Do not commit, push, reply, or resolve without the user's approval of the exact content.
- Do not resolve threads in bulk.
- Do not edit the artifact of a merged slice.
- Do not change files outside the approved list, and leave the user's uncommitted unrelated files alone.