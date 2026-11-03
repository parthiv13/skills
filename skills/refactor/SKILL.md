---
name: refactor
description: "Refactor a provided method, class, or code block while preserving behavior. Use for long methods, duplicated logic, mixed responsibilities, or readability-focused restructuring. Discuss proposed edits and get approval before changing code."
argument-hint: "Provide the code block, class, or file and the refactoring concern."
---

# Refactor provided code

Refactor the specific method, class, or code block the user provides or identifies. Preserve its observable behavior. Do not turn a refactoring request into a feature change or broad cleanup.

## Procedure

1. **Inspect the target.** Use the provided code as the anchor. Read only the nearby implementation, relevant call sites and tests, and applicable repository guidance. Follow the code conventions and testing guide named in the project profile, when present. Confirm the target and the reported issue from code rather than assuming that length alone is a defect.
2. **Propose before editing.** Explain the specific problem and the smallest refactoring that would address it. Name the affected methods and files, what responsibilities would move or be extracted, what behavior and interfaces must remain unchanged, and which existing tests cover the behavior. Say whether additional tests appear necessary and why. Ask for approval and wait. Do not edit production code or tests before explicit approval. If the user changes the proposed scope, present the revised proposal and wait for approval again.
3. **Refactor narrowly.** Keep public APIs, control flow, ordering, side effects, exception behavior, and return values unchanged. Make the smallest change that improves the identified structure. Do not mix in unrelated fixes, formatting, renames, or behavior changes.
4. **Validate.** Run the narrowest relevant existing tests before and after the refactor when practical, following repository test instructions. Add a focused test only when existing tests do not protect affected observable behavior or a concrete coverage gap is found. Include any proposed new test in the pre-edit discussion and get approval before adding it. Do not add tests solely to exercise extracted private helpers or to mirror the new implementation structure.
5. **Report.** Summarize the structural change, state whether behavior or public interfaces changed, and report the exact tests run and any validation limits.

## Refactoring guidance

- For a long method, identify its logical phases and extract only cohesive operations into concise, descriptively named methods. Keep the original method as a readable sequence when it orchestrates those operations.
- Give each method one clear responsibility and name it for a domain action. Avoid names that narrate every implementation detail or helpers that merely wrap one expression without clarifying ownership.
- For duplicated logic or mixed responsibilities, verify the duplication or responsibility split in the nearby code before proposing a shared method or new type. Avoid abstractions that do not remove meaningful complexity.
- Split a class only when the code shows a distinct responsibility or ownership boundary. Do not split a class merely because it is long.
- Follow the existing architecture and naming conventions. Do not introduce a class, type, utility, or helper solely to make a method shorter.
- Treat existing unit tests as the expected behavioral protection, but inspect that they cover the affected behavior. Do not assume a passing test validates behavior it does not assert.
- When tests are needed, organize them as the testing guide says. Put the cases for one method or cohesive feature together in the matching test file.

## Boundaries

- If the user provides only a guideline or excerpt and no concrete code to refactor, do not invent a target. Ask for the method, class, or file.
- If the requested restructuring appears to require changing behavior, explain the conflict and ask how to proceed rather than silently changing behavior.
- If relevant tests fail before editing, report the baseline failure in the proposal. Do not weaken or remove tests to make the refactor pass.