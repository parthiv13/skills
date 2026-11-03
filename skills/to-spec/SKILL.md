---
name: to-spec
description: "Turn one or more stories into a reviewed plan, then split it into independently reviewable pull-request slice artifacts. Use when planning code from stories or asking for a code spec."
argument-hint: "Provide one or more story IDs (the file names in to-prd/), or paste the story text."
disable-model-invocation: true
---

# Stories to reviewed plan and PR slice artifacts

Use this workflow when the user wants to plan one or more stories and break the work into small, reviewable pull requests. Read each story from the text the user pastes or from its file in `to-prd/`. This skill plans only: it never writes production or test code, delegates to `coder`, or opens pull requests. Its output is a reviewed plan plus one self-contained artifact per PR slice, which a separate implementation skill turns into code.

The user reviews all generated code, and large change sets are hard to review. The workflow therefore has three gates, and each requires explicit user approval before the next begins:

1. **Plan review.** The whole story set: sequence, questions, code flow, and test scenarios (Sections 1 to 3).
2. **Slice-boundary review.** A one-table outline of PR slices per story (Section 4).
3. **Artifact handoff.** One artifact per approved slice, plus an index with the merge order (Section 5). Per-PR code review happens afterwards, outside this skill.

Keep all documents under the repository-root `to-spec/` directory. A story ID is the story's file name without `.md`, for example `checkout-tax-rounding` for `to-prd/checkout-tax-rounding.md`. The master plan is one evolving Markdown document for a related story set, named with the story IDs, for example `to-spec/tax-rounding-cart-totals-spec.md`. Slice artifacts go in a sibling directory with the same stem, for example `to-spec/tax-rounding-cart-totals/`. Do not change story content as part of this workflow.

## 1. Retrieve and sequence stories

1. Collect all story IDs provided by the user. If a story's text is missing or its file cannot be read, ask for the story text or state what could not be read; do not invent story content.
2. Read each story's full text, including its procedure and acceptance criteria, from the pasted text or its file in `to-prd/`. Cite story IDs in the spec.
3. Compare scope, terminology, acceptance criteria, dependencies, and ordering. Distinguish explicit dependencies from inferred sequencing. Do not infer that stories are dependent merely because they touch the same area.
4. When there are multiple stories, invoke the `show-me` skill and use its flow diagram to present the proposed sequence. Use arrows for required ordering and separate branches for independent or parallel stories. State when the relationship is uncertain. For one story, describe its scope without manufacturing a sequence.
5. Re-read stories before raising gaps. Report a question only when an ambiguity, contradiction, or missing decision can change implementation or acceptance. Do not turn a detail already specified by the story into a question. Preserve unresolved issues for the user to decide.
6. Every question about story wording or a story gap must cite the originating story ID and the exact available pointer, such as a numbered procedure step, acceptance test, or named section. Include a short description of what that cited item says so the user can cross-check context. If the story omits the decision, cite the nearest relevant story requirement and say explicitly that the requested detail is unspecified. Keep repository-code evidence separate and cite the relevant class or method.

## 2. Propose the code flow

Create a high-level design for the requested behavior, grounded in the stories and the repository's existing architecture. Before proposing a design, inspect the relevant code and read these documents from the project profile. Apply them; do not restate their rules in the spec.

1. The engineering principles doc, for explicit data flow, readable control flow, and composing small responsibilities.
2. The structure guide, for services, the composition root, and where responsibilities live.
3. The code conventions and language conventions docs, for service and computation classes, method shape, and language rules.
4. The extended coding rules, for the extended defaults and safety constraints.
5. The domain doc, for domain concepts and external contracts that affect the design.

Use these documents to keep each proposed class to one responsibility. The check runs both ways. Before finalizing the class inventory, check every new and changed class: split a class that owns more than one responsibility, and fold a class that fails the test below into its caller as a private method. Name the responsibility of each class in the inventory. If a requirement forces a class to carry several responsibilities, raise it to the user as a numbered question instead of accepting it silently.

A new class must have at least one of these reasons to exist:

- It is a Service: it owns external I/O or coordinates other services.
- Another class reuses it.
- It is the one computation that a service or caller delegates to for a whole use case.

A Computation class with one public method and one caller is a private method of that caller. Do not create a class for each numbered step of a story procedure, or for each row of the translation table. Steps become private methods on the class that owns the use case.

Calibrate granularity against code that does similar work today. Name the existing pipeline stage the new work resembles, for example how many classes `OrderService` composes and how many steps each keeps as private methods, and match that granularity.

When the user supplies a structure or class names, treat them as the complete inventory. Re-derive the design from it instead of keeping classes from an earlier draft. Add a class beyond the user's inventory only after stating the reason as a numbered question.

### Translate business language into code language

Stories describe business behavior, not code structure. A core purpose of this workflow is to translate that behavior into the vocabulary of the codebase, so the spec must not restate or paraphrase the stories. Cite the story pointer for the requirement, and write only what the story leaves out: the code-level decision.

1. Take names from the glossary and the naming conventions guide (both in the project profile). Read those two files, not the whole repository, and build names from the glossary's domain terms and the convention rules. Read other code only to verify an existing name you cite or to place a new class.
2. Write the translation as code, not English. Use the repository's language, with real or proposed class, method, parameter, and type names, and write conditions and operations as expressions. The reader knows the repository and the stories, so prose like "filter rows to the ones with a matching key" is noise. If a story says "scan the orders" and means "group them into batches", the translation is `ClassName._get_batches(orders)`. The name and signature must carry the meaning, so aim for no pseudocode body. If a name cannot carry the logic, do not paper over it with pseudocode: raise it to the user as a numbered question with the candidate names you considered, and let the user decide the name or the structure.
3. Do not use stand-in words such as "appropriate", "relevant", "valid", "necessary", "process", "handle", "manage", or "data" in a name or in pseudocode. Replace each with the criterion, key, or type it stands for. If you cannot name it, the translation is incomplete: ask the user, citing the story pointer.
4. Include a numbered business-to-code translation table in the plan, one row per concept that changes name when it becomes code. Columns: story pointer, business phrase (a few words, not a copy of the story text), code construct in the form `ClassName.method_name(parameter: Type) -> ReturnType`, and, only for a row the user has agreed needs it, a pseudocode line or short block. Mark each construct existing or new. Keep the same names in the diagrams, the class inventory, the test scenarios, and every slice artifact.
5. Get the user's review of every new name before the code flow is approved. Present the new names as a numbered list so the user can reply per item. The user may hold naming opinions that the files do not record. When the user states or corrects a naming rule, add it to the naming conventions guide if it is a general rule, or to the glossary if it names a domain concept, and tell the user which file you changed. Do not edit either file without the user's confirmation.
6. Where more than one translation is plausible and the choice changes behavior or structure, present the options and ask the user, citing the story pointer. Do not choose silently.

### Present the design

Present the design visually by invoking the `show-me` skill and following its guidance. Pick the views that fit: a Mermaid flowchart or sequence diagram for the changed call path and data flow, a structure tree for class composition, a file tree for responsibility, and a `diff`-style view when the change is best read against the existing flow. Use an HTML artifact only when the flow is too dense for Mermaid. The restrictions in this section take precedence over `show-me` where they differ: the spec contains no full implementations, so skip the `show-me` whole-block code view. Pseudocode from `show-me` is allowed where it carries a translation from business logic to code, as described above.

Show the changed call path, beginning at the nearest existing application method where behavior changes. Use actual class and method names to show ownership, dependencies, and how calls or data move between them. Verify every existing name against the repository, and mark newly proposed classes and methods as new so the reader can tell them from existing code. Use developer-facing terminology rather than replacing class/method names with English descriptions; add short labels only for business decisions, important inputs, or outputs that have no useful domain type name. Whenever the diagram or prose names more than one class or file, state the responsibility of each. The diagram and prose should make the proposed structure easy to verify. Omit established entry points, composition-root wiring, and other unchanged internals unless they are needed to explain a changed dependency. Do not include full implementations or explanations of individual method internals; short pseudocode is allowed only for rows of the translation table.

Describe configuration decisions in business terms. When discussing a feature flag, name the flag key itself (for example, `checkout.useNewTaxRounding`), not the method that reads it (such as `ConfigService.get_flag`). Mention the accessor only when its implementation is part of the proposed change.

Keep diagrams focused on what the stories change and where the change takes effect. Show the changed branch, the named classes and methods that own the new behavior, their dependencies, and any existing behavior that remains as an alternative. Do not diagram unchanged entry points (request handlers, bootstrapping, publishing steps), or other established flow merely to show the whole application path. Summarize that context in prose only when it affects a design decision.

After the proposed flow, include a numbered inventory of every newly proposed class. The inventory complements the call-flow diagram: the diagram shows how classes and methods connect; the inventory gives each new class's type and construction details. Mark each class as a **Service**, **Utility**, or **Computation** class. For each Service, state its dependencies on other stateless service classes and the external resource it owns, if any. For each Utility, label it explicitly as a Utility and state its role. For each Computation class, list its constructor-supplied domain values and configuration; state service dependencies only when the design has a concrete reason for them. Distinguish new classes from existing classes, and do not list unchanged repository classes as new.

If a design choice depends on an unresolved story detail, show the affected part of the flow and ask the user rather than choosing silently.

The proposal may use short class-level labels to distinguish responsibilities, but the names and connections should carry the explanation. Do not turn the design section into implementation instructions.

Use numbered pointers throughout the document so the user can refer to specific items in feedback. Questions must carry the story citations described in Section 1. Keep identifiers stable while revising the same spec. Include at minimum:

1. Story sequence and dependencies.
2. Evidence-backed questions or story/design drift, if any.
3. Proposed class and method flow.
4. The business-to-code translation table.
5. Explicit assumptions that do not change story behavior.
6. For each story, a short scope note listing the distinct responsibilities and layers it touches (for example pure computation, service with I/O, configuration, orchestration), and which classes are shared with other stories. This feeds the slice-boundary review in Section 4.

Review the design in two steps. First, before writing the full document, send the user a compact class outline in chat: a structure tree with one line per new class, its type, and the total count of new classes. Stop and wait for approval of the structure. Then save the full proposal in `to-spec/` with the class outline and its count near the top, ahead of the questions, and ask the user to review the sequence and code flow. Stop and wait for their feedback and approval before preparing test scenarios as a confirmed plan. Revise the same document in place. Do not treat silence or a request to continue drafting as approval to proceed.

## 3. Propose test scenarios

After the user approves the code flow, decide, write, and present the test scenarios following the [test-scenarios](../test-scenarios/SKILL.md) skill. The source of truth is the story's acceptance criteria. Map each scenario to its criterion where possible. Write each scenario in code vocabulary from the translation table, and reference the criterion by pointer instead of copying its text. Report any gap or drift between the story and the proposed scenarios as that skill describes.

Keep the scenarios in the `to-spec/` document and revise it in place. Wait for explicit approval of both the flow and test scenarios before proposing slices.

## 4. Propose PR slices

Once the code flow and unit-test scenarios are approved, split the work into pull-request slices. Do not write artifacts yet; first present an outline for review.

Every story gets at least one PR, and one PR belongs to exactly one story. A story is split into several slices when it contains several meaningful boundaries. Never size slices by line, file, or class counts; use meaning only. A slice is valid only if it passes all of these tests:

1. **One responsibility.** It can be stated in one sentence without "and also", such as "calculates the daily total from computed line items and the previous total" or "wires the flag branch into the orchestrating service".
2. **Reviewable on its own.** Its correctness can be judged from its diff, its tests, and the contracts in its artifact, without reading a later slice.
3. **Independently verifiable.** Its own unit tests prove its behavior, and the main branch stays releasable after it merges. Behavior that is not yet wired in is unreachable or feature-flag-gated, and existing behavior is unchanged.
4. **One layer of the design.** It does not cross a seam in the plan's dependency graph, such as pure computation, service with I/O, configuration, or orchestration and wiring. Several classes may share a slice when they form one unit, such as a computation class and its helper.
5. **No split below meaning.** If a candidate cannot be explained or tested without its neighbor, merge them. If it needs "and" to be described, split it.

Derive slices bottom-up from the plan's dependency graph: leaf computation first, then services, then configuration, then orchestration and wiring last. Tests travel with the code they test. A class shared by several stories belongs to the earliest story in the sequence that needs it; later stories depend on that slice. Integration tests, if the user wants them, are an optional final slice per story and need separate approval, following the project's testing guidance and the test pyramid. Do not propose them by default.

Present the outline by invoking the `show-me` skill: a dependency diagram of the slices plus a table with these columns: slice ID, story ID, one-sentence responsibility, classes and files touched with the responsibility of each, depends on, and which of the five tests justifies the boundary (and why). State which slices could be developed in parallel, even though the user will merge them in order.

Save the outline in the master plan and ask the user to review it. Revise until approved. Do not treat silence as approval.

## 5. Write the slice artifacts

After the outline is approved, create the artifacts in the sibling directory:

```text
to-spec/{STORIES}/
├── index.md                     # merge order, dependency graph, status per slice
├── 01-{STORY}-{slug}.md         # one self-contained artifact per PR slice
└── 02-{STORY}-{slug}.md
```

The numeric prefix is the merge order. Each slice artifact must let a fresh session implement and open the PR from that file alone. Create each artifact from `references/slice-artifact-template.md` and `index.md` from `references/index-template.md`, both in this skill's directory. Fill every section; do not drop or reorder sections. The design, translation rows, and test scenarios in an artifact are cut from the approved master plan and may duplicate it. The plan stays the only place a design change is made; update the plan first, then the affected unmerged artifacts.

Use the same `show-me` guidance for any visual in an artifact. The no-implementation rule from Section 2 applies: names, signatures, and the pseudocode carried in translation rows are allowed; full method bodies are not.

Do not implement any slice, create branches, or open pull requests.

## 6. Hand off and handle revisions

Finish with a concise summary: stories covered, the number of slices per story, the merge order, and links to the master plan, `index.md`, and each artifact. Tell the user that implementing a slice and opening its PR is handled by a separate skill, one artifact at a time, with the user reviewing and merging each PR before the next slice starts.

If the user later reports that review feedback or a merged change affects the plan, revise the master plan, `index.md`, and every unmerged artifact that is affected. Never edit the artifact of a merged slice. Keep identifiers stable.

## Rules

- Stories are the source of truth for expected behavior. Do not silently add, remove, or reinterpret acceptance criteria.
- The spec translates business behavior into code vocabulary; it does not duplicate the stories. Cite story pointers. Names come from the glossary and the naming conventions guide (project profile), new names get the user's review, and stand-in words such as "appropriate" or "relevant" never appear in names, diagrams, scenarios, or artifacts.
- Apply the `writing-agent` skill (and its `unslop` rules) to the spec, the artifacts, and every chat message in this workflow. Number each point in chat replies so the user can reference it.
- Every story-related question must include its story ID and exact procedure, acceptance-test, or section pointer, plus enough context to verify the concern against the source.
- Make review gates explicit. User approval is required after code-flow review, after unit-test scenario review, and after the slice-boundary outline. Artifacts are written only after the outline is approved.
- Slice boundaries are justified by meaning, never by line, file, or class counts. One PR belongs to exactly one story.
- Use high-level names and diagrams for the design, produced through the `show-me` skill. The specification has no full implementations; pseudocode appears only in the translation table.
- Prefer evidence over generic concerns. Do not manufacture ambiguity, missing requirements, or SOLID violations.
- Keep documents in the repository-root `to-spec/` directory and revise the same files throughout the work.
- Number sections, questions, scenarios, slices, and artifacts so the user can reference them directly.
