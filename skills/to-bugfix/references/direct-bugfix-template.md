# BUGFIX-{slug}: {short title}

> Author notes start with `>`. Delete them when you fill in the task. This artifact is self-contained and does not need a story or master plan.

## 1. Identity

**1.1 Task ID:** BUGFIX-{slug}

**1.2 Diagnosis:** {diagnosis artifact}. Conclusion: confirmed.

**1.3 Responsibility:** {one sentence describing the correction}

**1.4 Direct fix:** No story or to-spec plan. Do not create either.

**1.5 Master plan:** None. This is a direct bug fix, not a feature slice.

**1.6 Reviewer context:** {user-visible bug and correction}

## 2. Dependencies

**2.1 Depends on:** none

**2.2 Depended on by:** none

## 3. Scope

**3.1 Create or change**

| File | Class | Responsibility | New or existing |
| --- | --- | --- | --- |
| {path} | {class or symbol} | {production correction or regression test} | {new or existing} |
| {reproduction test path} | {test class} | Reproduction test kept by the diagnosis; must pass after the fix | existing |

**3.2 Not in scope:** {related behavior or files excluded from this fix}

## 4. Design

**4.1 Changed call path**

```text
{existing caller} -> {affected method} -> {corrected behavior}
```

**4.2 Class inventory**

| Class | Type | Responsibility | Dependencies | Constructor-supplied values | New or existing |
| --- | --- | --- | --- | --- | --- |
| {class} | {Service, Utility or Computation} | {one responsibility} | {dependencies or none} | {values or none} | {new or existing} |

**4.3 Configuration:** {configuration keys and behavior, or "No configuration changes."}

**4.4 Diagnosis reference:** {section and evidence in diagnosis artifact}

## 5. Business-to-code translation

| # | Diagnosis pointer | Reported behavior | Code construct | New or existing |
| --- | --- | --- | --- | --- |
| 5.1 | {evidence or report field} | {short phrase} | `{ClassName.method_name(parameter: Type) -> ReturnType}` | {new or existing} |

## 6. Contracts with neighbouring work

**6.1 Provides:** {public signatures used elsewhere, or none}

**6.2 Expects:** {existing signatures this fix uses}

## 7. Test scenarios

| ID | Class | Behavior to verify | Diagnosis evidence |
| --- | --- | --- | --- |
| 7.1 | {ClassName} | {input condition and observable corrected result} | {pointer} |

**7.2 Reproduction test:** {test path}, run with {run configuration or command}. It fails before the fix. It is the acceptance check: it must pass after the fix. Do not weaken, rename, or remove its assertions; extend it or add tests alongside it.

## 8. Constraints

**8.1 Configuration behavior:** {key and behavior, or "No configuration changes."}

**8.2 Repository rules that apply.** Paths come from the project profile:

- Testing guide: {path}
- Code conventions: {path}
- Language conventions: {path}
- Naming conventions: {path}

## 9. Main stays releasable

**9.1 This is a direct correction to existing behavior.** Do not add a feature flag or introduce behavior beyond the confirmed fix. If the requested change is a feature rather than a bug correction, stop and report it.

**9.2 Existing behavior that must not change:** {behavior and the test that covers it}

## 10. Reconcile before implementing

**10.1 Dependencies to compare against:** none

**10.2 Re-check sections 3.1, 4, 5, and 6.2 against the current code.** If a scope, behavior, or contract mismatch appears, stop and report it before editing.
