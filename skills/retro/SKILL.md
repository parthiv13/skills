---
name: retro
description: "Review a coding session and suggest practical improvements to the project or assistant environment."
disable-model-invocation: true
---

The user has asked for a **retrospective**. Review the session for evidence-backed changes that could make future work in this project more effective or reliable. Do not assume the project uses custom agents or a separate implementation and review workflow.

## Steps

1. Identify the session to review. Use the session the user specifies, or the current session if they do not specify one. Read the relevant conversation, tool results, changed files, and test output. Prefer primary sources over recollection.

2. Look for applicable improvement opportunities. Consider only categories supported by evidence from the session or project:

- **Navigation**: Did it take extra searching to find the right files, instructions, or entry points? Would a short pointer in existing documentation help?
- **Automated checks**: Could an existing test, lint, type-check, pre-commit hook, or CI check have caught a mistake or uncertainty? Inspect the project's actual scripts and CI configuration before recommending a check. Flag a missing check when the session or project shows a concrete risk, not merely because no check was found.
- **Coding guidance**: Did an unclear, conflicting, stale, or misplaced instruction lead to a poor decision or repeated confusion? Inspect the instruction and standards files that actually exist. Recommend a deterministic check for mechanical rules; reserve written guidance for judgment that cannot be automated.
- **Instruction overhead**: Are project instructions duplicated, irrelevant, or unlikely to change behavior? Suggest simplifying them only when the session shows they caused confusion or wasted effort.
- **Information access**: Was important information unavailable or difficult to verify? Would a documented source, local diagnostic, or read-only integration close that gap?

3. Separate observed problems from possible improvements. Do not infer that a file, check, or capability does not exist from a failed search alone. State uncertainty when the available evidence is incomplete.

4. Present supported findings in descending order of impact. For each, briefly state the evidence, its effect on the work, and a concrete improvement. Omit categories with no useful finding. If the session does not support a recommendation, say so rather than inventing one.

## Guidance

- Focus on changes to the project or working environment, not a critique of the user's choices.
- Do not assume that coding, implementation, or review work is assigned to distinct agents. If a session explicitly uses separate roles, discuss those roles only where relevant to the evidence.
- Check existing project documentation and automation before proposing new files, rules, or tools.
- Make recommendations, but do not modify project files unless the user asks you to implement them.