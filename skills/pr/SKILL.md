---
name: pr
description: "Create a concise pull request body, push the changes, and open a pull request. Use when asked to create a PR."
---

# Create a pull request

Use this workflow when requested directly or invoked by another workflow. When invoked by a workflow that supplies test and review results, rely on them and do not repeat them. The story is the source of truth; do not copy its acceptance criteria or implementation details into the PR body.

## Steps

1. Read the project's PR template (profile: PR template) and preserve its headings, order, separator, and checklist. If the profile names no template, use the default format below.
2. Keep the body very concise. Use project terms from the glossary (profile). Under `What` and `Why`, describe the outcome and purpose in plain language. Use multiple lines when useful, but do not include implementation details or repeat acceptance criteria, design details, or test scenarios. When the PR is one slice of a larger story, describe only that slice's outcome, and add the slice artifact's Reviewer context under `Why` when the caller supplies it. Set the checklist from test and documentation status supplied by the caller or slice artifact; leave an item unchecked if its status is unknown. Do not rerun or independently re-confirm checks the caller already ran.
3. Inspect the current branch, working tree, staged and unstaged diffs, untracked files, and recent commits. Identify exactly which changed files belong to the story, or to the slice when a slice artifact was supplied; its scope table lists them. Do not stage, commit, or push unrelated changes.
4. Before staging, committing, pushing, or creating the PR, show the user the exact files to include and the changed files to leave out, the proposed commit message, the source and target branches, and the PR title and body. Ask for explicit approval of this scope and publication plan. Do not stage, commit, push, or create the PR until the user approves. If the scope or message changes, present the revised plan and get approval again.
5. After approval, stage only the listed files. If story changes are uncommitted, commit only those files with the approved message. Do not amend commits or force-push.
6. Push the approved branch and create the pull request with the approved title and body. Use the available Git and GitHub integration. Do not claim the push or PR succeeded unless the tool confirms it.
7. Report the PR URL, test status, and any unchecked checklist item or blocker. Keep the report brief.

## Default PR body format

Use this only when the project profile names no PR template. Otherwise follow the project's template exactly.

```markdown
### What:

### Why:

---

### Code Checklist

- [ ] tested
- [ ] documented
```
