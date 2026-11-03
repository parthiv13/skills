---
name: to-prd
description: "Turn rough requirements, research notes, algorithm drafts, or an artifact into a reviewed story. Use when drafting a story from informal source material."
argument-hint: "Paste the source material or provide an artifact path."
disable-model-invocation: true
---

# Turn Source Material Into a Story

Use this collaborative workflow when the user provides a rough requirement, research handoff, or algorithmic proposal and wants it reviewed, organized, or made into a story. Keep the user informed at each phase. Never mark the draft approved in the same step as presenting it.

## Phases

### 1. Understand and review the source

Read the source and identify its intended problem, outcome, procedure, constraints, and acceptance conditions. Keep explicit facts separate from assumptions and open questions. Preserve the author's procedural structure and intent; improve grammar without silently changing algorithmic behavior, ordering, conditions, or outcomes. Check that **What?** agrees with the result produced by the **Procedure**.

When the user provides an artifact, read it as source material and preserve the distinction between observed facts, hypotheses, and unresolved questions. No existing story ID is needed: the story is saved as a Markdown file in `to-prd/`.

When the user provides related stories, read their text from what the user pastes or from their files in `to-prd/`. Compare scope, behavior, terminology, dependencies, and acceptance conditions. Report any conflicts, gaps, or mismatches to the user with enough context to understand the impact; do not silently resolve them. If the stories align, say so explicitly. If an item's content cannot be accessed, ask the user to provide it or state what could not be verified.

Show the user a concise understanding of the request and any material ambiguity, contradiction, or suggested improvement. Explain why each concern could affect implementation or acceptance. Ask focused questions when a decision is needed, and wait for the user's response before treating an interpretation as settled. Do not draft around unresolved scope-changing assumptions.

### 2. Refine and get story review

Once the source is sufficiently clear, draft the story using the format below. Include **Procedure** when there is a meaningful multi-step algorithm or workflow; omit it for small stories whose acceptance tests adequately specify behavior. Write **ATs** as observable, testable conditions. Cover stated outcomes and important stated edge cases without inventing product behavior or prescribing unnecessary implementation details.

Save the complete refined story in one Markdown file and ask the user to review that file. In chat, give a concise summary and link to the file instead of reproducing the full story. Keep optional algorithm improvements labeled as suggestions, not silently incorporated changes. Update the same file in place after each feedback round; do not create versioned copies. Do not mark the story approved at this phase.

### 3. Finalize after approval

Once the user explicitly approves the refined story, mark the existing Markdown file as approved. Approval of the story is authorization to finalize it; do not require a separate request. Do not add story points or other estimates unless requested. Do not create a separate final copy. Report the file path.

Treat a request to "make a story" as authorization to prepare and review the story, not as approval to skip the review phase. If the user explicitly asks to skip review, confirm that change before marking the file approved.

## Draft Artifacts

Keep one evolving story file at `to-prd/<slug>.md`, where `<slug>` is a short kebab-case name for the story. Put review questions and feedback in that file. Update it in place for every revision; do not create draft directories, review-note files, versioned copies, or a separate final copy.

Format story lists as ordered lists and avoid unordered bullets in the story body. Markdown does not recognize alphabetic markers such as `a.` as ordered-list syntax. Use valid numeric markers for both main and nested steps; do not add alphabetic labels. Do not include story points unless the user explicitly requests them.

## Story Format

**Summary**
A concise, outcome-oriented title.

**What?**
Explain the problem being solved or functionality being added, why it matters, and relevant decision context or constraints. Do not add rationale that is absent from the source; mark missing context as a question when it matters.

**Procedure** _(include when needed)_
A numbered sequence of steps, retaining the source's logical order. Clarify wording while preserving behavior. Keep unresolved points explicit rather than choosing an interpretation.

**ATs (Acceptance Tests)**
An ordered list of independently verifiable conditions that demonstrate the story is complete and acceptable.

## Review Notes

When issues need discussion, show them separately from the proposed story under **Questions / Concerns**. For each concern, state the conflicting or missing information and why it affects implementation or acceptance. Label optional algorithm improvements as suggestions. Do not bury unresolved decisions in the story or present assumptions as confirmed requirements.

Keep the result direct and domain-appropriate. Do not force a generic user-story sentence if the source is clearer without one.
