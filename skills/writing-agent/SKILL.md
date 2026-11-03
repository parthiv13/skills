---
name: writing-agent
description: "Use whenever an agent writes, edits, reviews, or explains repository documentation, plans, design specs, READMEs, technical proposals, or code flows. Apply unslop and write for an expert reader using verified code references."
---

# Writing agent

Use this skill whenever an agent writes, edits, reviews, or explains repository documentation. Apply it with task-specific workflows such as `to-spec`. The task-specific workflow controls its required format and approval gates. This skill controls how the writing is researched, structured, and presented.

## Workflow

1. **Establish scope and evidence.** Read [unslop](../unslop/SKILL.md) before drafting. Read applicable task instructions, project guidance, and the source material needed for this document. Identify the reader, the decision or action the document supports, and which claims need repository evidence. Treat the user as the repository's architecture authority. Do not explain basics they already know or override explicit design choices with generic preferences.

   Done when the artifact's scope, governing workflow, relevant sources, and reader decision are identified.

2. **Choose the information hierarchy.** Separate procedural steps from reference before writing. Put content at the highest level where it is useful, and disclose details that only some paths need.

   2.1. **In-file Steps.** Use for actions that must be performed. Express ordering only where a real dependency or sequence exists.

   2.2. **In-file Reference.** Use for definitions, rules, and facts needed across the document. A flat peer set is appropriate when the rules have no hierarchy.

   2.3. **Disclosed Reference.** Put branch-specific detail in a linked file and load it when that branch applies. Keep shared guidance inline. Keep decisions requiring user judgment visible in the document under review; pointers should expose supporting detail, not hide decisions.

   2.4. **Co-location.** Keep a concept's definition, rules, and caveats together. Do not scatter one concept across the document or duplicate it in multiple sources of truth.

   2.5. **Loads and Splits.** Always-loaded material spends agent context. Additional files and pointers spend the reader's navigation effort. Use a pointer when it removes irrelevant context without making the reader hunt for something needed to review or decide. Split by branch when only that branch needs the detail. Split a sequence only across a real handoff or invocation boundary. Do not split merely to make a file shorter.

   Done when each item has a clear place and any pointer earns its navigation cost.

3. **Draft for an expert reader.** Use repository terminology and exact class names, method names, signatures, types, configuration keys, inputs, outputs, and dependencies. Explain how calls and data move between owners. Use precise English to connect the technical details, not to replace them with vague summaries.

   3.1. **Existing versus Proposed.** Inspect the implementation before describing existing behavior. Distinguish verified code from assumptions and proposed changes. A proposal may introduce classes, methods, signatures, and dependencies. Label proposed elements as proposed, and ground their responsibilities and connections in requirements and existing architecture. Never describe proposed elements as existing code.

   3.2. **Changed Call Path.** Begin at the nearest existing method whose behavior changes. Show the changed branch, its owners and dependencies, and any existing alternative that matters. Omit unchanged handlers and composition wiring unless they explain a changed dependency.

   3.3. **Diagrams and Source Links.** Use a focused Mermaid flowchart when it makes branches, dependencies, or data movement easier to verify. Put actual class and method names in its nodes. Link to relevant source symbols with workspace-relative Markdown links and verified line anchors where available. If the line is uncertain, link to the file without an anchor. A diagram supplements specific names and links; it does not replace them.

   3.4. **Proposed Class Inventory.** When a design introduces classes, list every proposed class separately from existing classes. Mark each as **Service**, **Utility**, or **Computation**. For a Service, state its stateless service dependencies and external resource, if any. For a Utility, state its role. For a Computation, list its constructor-supplied domain values and configuration; list service dependencies only when the design has a concrete reason.

   3.5. **Configuration and Questions.** Describe configuration decisions in business terms and name the actual key. Mention its accessor only if the accessor changes. Raise only questions that can affect behavior, implementation, or acceptance. Cite the exact requirement or source pointer, state what it specifies, and identify what remains undecided. Do not silently choose for the user.

   Done when technical claims are evidence-backed or explicitly labeled as proposals, and the flow and dependencies can be checked against the named symbols.

4. **Make feedback addressable.** Use stable hierarchical identifiers for points that may need review: `1`, `1.1`, `1.1.1`. Preserve the parent path and visual nesting. These identifiers locate content; they do not imply execution order. Do not flatten a nested outline or restart child identifiers when that makes feedback ambiguous. Preserve a better-fitting format such as a table or diagram when appropriate, while keeping useful numbered references.

   For a substantial plan or approval proposal before coding, create or update one Markdown file under the repository-root `planning/` directory and link it in chat. Keep simple plans of 1-5 lines in chat. Update an existing plan in place. Use a meaningful heading such as `Plan`; do not explain the numbering in the heading or add commentary about numbering. Keep the chat handoff concise and state what the user needs to review. Do not paste the whole file into chat.

   Done when each review point has a stable reference and the user can find the artifact and understand the requested decision.

5. **Prune and verify.** Keep one authoritative source for each meaning. Point to configuration or commands instead of copying facts the reader or agent can cheaply look up. Document the rationale, unwritten convention, or gotcha the environment does not reveal. Check each line for relevance and for whether it changes agent behavior. Delete no-op instructions. If their effect is uncertain, test them on a representative task. Run the unslop self-audit. Verify source links, anchors, diagrams, and completion criteria before presenting the document.

   Done when the document is relevant, internally consistent, its references resolve, and every procedural step has a clear, checkable completion criterion.

## Step completion

Every procedural step in a document ends with a completion criterion that is both checkable and exhaustive for that step. Sharpen a vague criterion before splitting the sequence. Split only when the criterion remains irreducibly vague and premature completion is evident. Hiding later steps works only across a real handoff or invocation boundary, not when the work remains in the same context.