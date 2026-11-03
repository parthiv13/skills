---
name: to-code
description: "Implement a scoped coding artifact, verify and review the changes, then prepare a pull request. Use with a direct task artifact or a sequenced set of artifacts."
argument-hint: "Path to the coding artifact or artifact index."
disable-model-invocation: true
---

# Artifact to reviewed pull request

Use this workflow to implement one coding artifact, review the changes with the user, and prepare a pull request. The artifact defines the requested behavior, allowed scope, design, and tests. If the artifact set includes an index or master plan, use it for sequencing and design decisions.

Number each point in chat so the user can reply to it. Put questions in numbered form and cite the relevant artifact section.

## Artifact requirements

Before coding, confirm that the selected artifact provides:

- A task identity or title and a clear implementation responsibility.
- The requested or confirmed behavior, including the condition that triggers it and the expected outcome.
- The exact files allowed to change, with each file's responsibility, and any explicit exclusions.
- The relevant existing code flow and intended correction or design.
- Required contracts, dependencies, and configuration behavior, or a clear statement that none are added.
- Test scenarios and existing behavior that must remain unchanged.
- Any implementation constraints, release gates, and review context.

Artifacts may use different section names and formats. A direct-coding task can stand alone. An index, master plan, or lifecycle status is optional. When an artifact set supplies these, follow them. Do not invent missing behavior or expand the allowed files. If a required detail is missing or conflicts with the code, ask the user before implementation.

## 1. Select the work

1. Use the artifact path named by the user. If the user supplies an index, choose its lowest merge-order item marked `planned`. If no artifact or index is identified, ask which one to use.
2. If the selected item is marked `in progress` or `in review`, do not restart it. Ask which stage to resume.
3. If the artifact or index lists dependencies, confirm each is marked `merged` or otherwise complete according to its stated workflow. Stop if a dependency is not complete.

Done when one artifact is selected, it meets the artifact requirements above, and all declared dependencies are complete.

## 2. Reconcile with the code

Read the relevant code and tests. Follow any reconciliation checklist in the artifact. Compare its allowed files, call path, names and contracts, test scenarios, and constraints with the current code on the main branch.

If a mismatch or missing dependency could change scope, behavior, or acceptance, list each issue as a numbered question and wait for the user's direction. Update the artifact or any linked unmerged artifacts only as directed. Do not implement from stale or ambiguous instructions.

Done when the artifact matches the code or the user has approved each necessary update.

## 3. Branch

Create a new branch from the remote default branch (`origin/main` unless the project profile names another): run `git fetch origin`, then branch from that remote ref, not from the local default branch or the current branch. Use the artifact's task ID when present. Otherwise use a short branch name that reflects the work, such as `bugfix/{slug}` or `feature/{slug}`.

Do not overwrite or discard existing work. If the worktree is not clean, stop and ask how the user wants to handle it. Update the artifact or associated index to `in progress` only if it has a status field.

Done when the branch starts at the remote default branch, the worktree is clean, and any supplied status field shows `in progress`.

## 4. Implement through the `coder` agent

Delegate implementation to the repository's `coder` agent. Pass the selected artifact content unchanged as the step block and include its path. The coder returns the report fields defined in its contract: step, status, tests run, files created, and files updated.

1. On `Status: pass`, continue to Section 5.
2. On `Status: fail` or `Status: blocked`, show the coder's report and ask what to do. Do not weaken or remove tests to get a pass.
3. Compare the coder's files with the artifact's allowed-file list. A file outside that list is not kept until the user approves it.
4. If the coder or delegation is unavailable, tell the user and ask how to proceed. Do not silently write the implementation yourself.

Done when the coder reports `pass` and every changed file is allowed by the artifact or approved by the user.

## 5. Verify

1. Read the testing guide in the project profile. Run the artifact's required tests with the project's test command. Also run its named existing-behavior tests.
2. Honor release gates and configuration constraints stated in the artifact. Do not introduce a gate when none is required.
3. Review the diff for correctness, regressions, and scope. Fix in-scope issues through the `coder` agent and rerun affected tests. Present findings that require changes beyond the artifact before editing.

Done when the required tests and existing-behavior checks pass and the diff has no open in-scope correctness issue.

## 6. Review with the user locally

Present the implementation and changed call path for local review. Compare the behavior with the artifact's design, link each changed file, and state its responsibility.

1. Number each point the user may want to comment on, using stable hierarchical numbers such as `1.1` for a file and `1.1.1` for a method.
2. For each comment the user accepts, have the `coder` agent make the change using a step block that names the files, comment, and affected artifact sections. Rerun affected tests.
3. Repeat until the user explicitly approves. Do not treat silence as approval.

Done when the user has approved the local change.

## 7. Keep artifacts in sync

If implementation or review changes a name, signature, file, design, or test:

1. Update the master plan first if one exists and owns that design decision.
2. Update the selected artifact and any affected unmerged artifacts or index entries.
3. Never edit a merged artifact.
4. If the user states a naming rule, ask for confirmation, then record it in the naming conventions guide for a general rule, or in the glossary for a domain concept. Both paths come from the project profile.
5. Tell the user what changed as a numbered list, with the file and section for each item.

Run this step after local review and after each round of pull request review.

Done when the implementation and all affected unmerged artifacts agree.

## 8. Open the pull request

Prepare a pull request using the repository's pull request workflow. Include the artifact path, reviewer context, changed files, test results, and review findings. Show the user the files, commit message, branch, and pull request body for approval before staging, committing, or pushing.

After the pull request is created, set the status to `in review` and record the link in the artifact or associated index if it has those fields.

Done when the pull request exists and any supplied status record shows `in review` with its link.

## 9. Handle pull request review

1. Number each review comment and identify the file it concerns.
2. Apply each accepted comment through the `coder` agent, rerun affected tests, and review the resulting diff.
3. Show the user the files and commit message, and get approval before pushing. Add new commits to the same branch. Do not amend or force-push.
4. Repeat until the user is satisfied.

## 10. After merge

Wait for the user to say the pull request is merged. Do not verify it through `gh`. Then set the status to `merged` and record the pull request link in the artifact or associated index if it has those fields. If the index lists another planned item, return to Section 1 when the user is ready to start it.

## Rules

- One artifact, one pull request. Never implement outside its allowed-file list without the user's approval.
- The artifact defines behavior, scope, design, names, and tests. Do not rename or restructure on your own. Raise a numbered question instead.
- Do not commit, push, or create a pull request without the user's approval of the exact files and message.
- Do not weaken or remove tests to make them pass.
- Never edit a merged artifact.