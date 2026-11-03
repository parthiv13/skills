---
name: wth-is-this
description: "Investigate code the user flags as bad, find the missed LLM-facing guidance or safeguard and related occurrences, and propose a grouped refactor. Never edit before the user's approval."
argument-hint: "Select or paste a code block, or provide a method name or file path."
disable-model-invocation: true
---

# Diagnose code the user flags as bad

Use this skill when the user invokes `wth-is-this` with a code selection, code block, method name, or file. A supplied code block means the user has identified it as bad. Do not respond with only a plain-English explanation or dismiss it as valid because the reason is not immediately obvious. Assume the user understands the code and project context.

Assume the LLM created the anti-pattern unless the user explicitly says otherwise. Do not investigate or seek evidence about authorship; only the user can override this assumption. Find what in the instructions, repository guidance, examples, or safeguards failed to prevent the pattern. Do not speculate about model internals or claim knowledge of the exact generation context unless the user provides it.

Do not blame the user. Treat their concern as authoritative. If the specific defect is unclear after checking the relevant instructions and code, ask the user what they want addressed instead of inventing a rationale.

## Workflow

### 1. Resolve the target and required guidance

Use the supplied selection, code block, method name, or path. First read the LLM instruction index named in the project profile (for example `AGENTS.md`, `CLAUDE.md`, or `.github/copilot-instructions.md`), and follow its links to the files it says the LLM must read. Then read the relevant target code, nearby callers, tests, conventions, and glossary. Do not assume a failed path or search proves that guidance does not exist. If the target cannot be identified, ask for the method or file.

### 2. Confirm the suspected defect

Compare the supplied code with the indexed guidance and surrounding code. If a specific missed rule or convention is clear, state it with the exact evidence and ask whether that is what the user flagged. Do not start a repository-wide recurrence search or draft a fix proposal until the user confirms. If the defect is unclear, ask the user what looks wrong. Do not guess. Ask one focused question at a time.

### 3. Use step-by-step interaction by default

Do not ask the user to choose a response style. After the concern is confirmed, present the recurrence and guidance-gap findings, then confirm that explanation before proposing remediation. If the user explicitly asks for the full diagnosis at once, present all findings and grouped remediation together. Both approaches use the same investigation and scope. Group occurrences that share a fix; never make the user review the same fix once per occurrence.

### 4. Find recurrence and the LLM-facing failure

Trace the confirmed issue through relevant call sites and tests. Search for structurally similar cases, then inspect each match to exclude false positives. State the search scope and distinguish confirmed occurrences from unverified leads. For other matches, distinguish confirmed repeats from similar-looking valid code or unresolved cases. Do not dismiss the supplied code as valid without first asking the user about the concern.

Identify what in the LLM's required inputs or safeguards failed to prevent the code: an absent rule, an instruction missing from the required-read index, ambiguous guidance, a missing example, or an ineffective check. Verify claims about repository guidance against its files. Do not investigate authorship or use git history to challenge the user's default attribution. Do not claim which exact prompt or context the model received unless the user provides it.

Cite relevant instructions, code, tests, and line ranges. Explain the code's behavior only enough to establish why the confirmed pattern is bad.

### 5. Confirm the explanation and propose a grouped fix

By default, show the recurrence and LLM-facing failure first. Ask whether that explanation matches the user's concern and wait for confirmation before presenting a remediation proposal. If the user requested the full diagnosis at once, present those findings with the proposed fix and ask whether the diagnosis and scope are right.

Propose a surgical refactor for all confirmed related occurrences. Group files and methods by shared remedy. Pair the code fix with the smallest useful correction to the LLM-facing instructions or safeguards, such as updating the authoritative rule or its required-read index, adding a targeted example, or adding a reliable test or lint check. Do not duplicate guidance or propose process for its own sake.

State the behavior, public interfaces, and error handling that must remain unchanged. Name the tests that cover them and any focused tests or documentation changes needed. Do not edit code, tests, or guidance while diagnosing.

### 6. Get implementation approval

Ask the user to explicitly approve the proposed scope before changing any code, tests, or documentation. Confirmation that the diagnosis is correct is not approval to edit. Treat approval as limited to the files and behavior described. If investigation reveals a new design choice or scope expansion, stop and ask before proceeding.

### 7. Implement and verify only the approved work

Make the approved grouped changes, preserve unrelated worktree edits, and run the narrowest established tests and checks that verify behavior. Report changed files, test results, and any unverified items. If a check exposes a necessary change outside the approval, ask before expanding scope.

## Response guidance

- Lead with the confirmed finding, not a long paraphrase of the code.
- Keep identical remediation together even in step-by-step mode.
- Tie every recommendation to a verified rule, behavior, or maintenance cost.