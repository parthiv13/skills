# {NN}. {STORY-ID} {slice title}

> Copy this structure for each PR slice. Replace every `{placeholder}`. Delete every line that starts with `>` in the artifact.
>
> The artifact must let a fresh session implement the slice and open its PR from this file alone. Cut every section from the approved master plan and keep the plan's names. Do not copy story text; cite pointers. Do not use stand-in words ("appropriate", "relevant", "valid", "necessary", "process", "handle", "manage", "data") in names or translation rows. Do not include full method bodies. Do not edit the design here first; change the master plan, then update this artifact.

## 1. Task

**1.1 Slice ID:** {NN}

**1.2 Story:** {STORY-ID}. Pointers covered: {procedure and acceptance-test pointers}

**1.3 Responsibility:** {one sentence, no "and also"}

**1.4 Boundary justification:** {which boundary tests from the plan this slice passes, and why}

**1.5 Master plan:** [{plan file name}]({relative link})

**1.6 Reviewer context:** {one or two sentences for a reviewer who has not read the plan: what this PR delivers in the feature, which PR calls it or first uses it, and what a reader should not expect to find here}

> `to-code` passes 1.6 to the `pr` skill, which puts it under Why. Write it in plain language. It does not repeat the responsibility in 1.3.

## 2. Dependencies

**2.1 Depends on:** {slice IDs, or none}

**2.2 Depended on by:** {slice IDs, or none}

## 3. Scope

**3.1 Create or change**

| File | Class | Responsibility | New or existing |
| --- | --- | --- | --- |
| {path} | {ClassName} | {one responsibility} | {new or existing} |

> Every class listed states exactly one responsibility. If a class needs two, the plan's design is wrong; stop and raise it to the user.

**3.2 Not in this PR**

> List what a reader would expect to find here but another slice owns, with that slice's ID.

{not in this PR, with owning slice IDs}

## 4. Design

> This slice only. Cut from the plan. Show only this slice's classes and the neighbours they touch. Omit unchanged internals, other slices' classes, and full method bodies. Pick diagram types with the show-me skill.

**4.1 Changed call path**

```mermaid
flowchart TD
    {Existing.entry_method} --> {New.method}
```

> Start at the nearest existing method whose behavior changes. Label neighbours owned by other slices with their slice ID. Mark proposed classes and methods as new.

**4.2 Class inventory (this slice's classes only)**

| Class | Type | Responsibility | Dependencies | Constructor-supplied values | New or existing |
| --- | --- | --- | --- | --- | --- |
| {ClassName} | {Service, Utility or Computation} | {one responsibility} | {stateless services, external resource} | {domain values and configuration} | {new or existing} |

**4.3 Configuration**

> Name the actual configuration keys this slice reads and the behavior when each is on or off. Do not name the accessor unless the accessor changes.

{configuration keys and behavior}

**4.4 Plan reference:** {plan section numbers this design was cut from}

## 5. Business-to-code translation

> One row per concept that changes name when it becomes code. The construct column must carry the meaning. Add pseudocode below a row only if the user agreed the name cannot carry the logic.

| # | Story pointer | Business phrase | Code construct | New or existing |
| --- | --- | --- | --- | --- |
| 5.1 | {pointer} | {few words, not story text} | `ClassName.method_name(parameter: Type) -> ReturnType` | {new or existing} |

## 6. Contracts

**6.1 Provides**

> Signatures other slices call, so they can be implemented without reading this one.

{provided signatures}

**6.2 Expects**

> Signatures from earlier slices, each with the slice ID, so this slice can be implemented without reading later work.

{expected signatures, with slice IDs}

## 7. Test scenarios

> Copy this slice's scenarios from the plan in full, with their IDs. Write each in code vocabulary from section 5. Cite acceptance criteria by pointer only.

| ID | Class | Behavior to verify | AC pointer |
| --- | --- | --- | --- |
| {ID} | {ClassName} | {method, input, observable result} | {pointer} |

## 8. Repository rules

**8.1 Documents that apply:** {links to the documents, not restated rules}

## 9. Release gating

> Main stays releasable after this slice merges.

**9.1 Unreachable or flag-gated until:** {slice ID}

**9.2 Configuration and flag behavior:** {key, behavior when off, behavior when on}

## 10. Behavior to preserve

**10.1 Existing behavior that must not change:** {class.method, and the test that proves it}

## 11. Reconcile before implementing

**11.1 Merged dependencies to compare against:** {slice IDs, and PR links once known}

**11.2 Fields to re-check:** sections 3.1, 4, 5, and 6.2

> Before coding, compare those sections with the code that actually merged. If review changed a name or signature, update this artifact and the master plan. If the design in section 4 is wrong, stop and raise it to the user. Do not change it silently.
