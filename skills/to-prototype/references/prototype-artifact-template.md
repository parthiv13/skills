# PROTO-{slug}: {short title}

## 1. Goal

**1.1 Goal:** {the goal in the user's terms}

**1.2 Source material:** {path or link, or "None"}

**1.3 Prototype branch:** {branch name}

**1.4 Base commit:** {sha the branch started from}

## 2. Accepted expectations

> Current target only. Change this section only after the user approves a change, and add a history entry in the same edit.

**2.1** {expectation}

## 3. Expectation history

> Append-only. Never edit or delete an entry.

| ID | Change | Caused by |
| --- | --- | --- |
| 3.1 | Initial target: {what was set} | User goal |
| 3.2 | {from} → {to} | Trial {number} |

## 4. Trial log

> One entry per trial, in order. Never rewrite an earlier entry. Correct a mistake with a note in a later entry.

### Trial {number}: {short title}

**Type:** feedback | iteration

**Change made:** {what was added, extended or rewritten. Say which.}

**Signal or hypothesis:** {for feedback, the named signal, such as a failing check, metric or user comment. For iteration, the idea being explored.}

**Test scenarios:**

| ID | Class or area | Behavior to verify |
| --- | --- | --- |
| 1 | {existing class or function} | {input condition and observable result} |

**Commit:** {sha, or "None"}

**Coder status:** pending | pass | fail | blocked

**Result:** {what happened, in observed terms. For iteration, do not claim it helped.}

**Effect on expectations:** {"None", or the proposed change and whether the user approved it}

## 5. Assessment

**Status:** open | done

**5.1 Observations:** {what was seen, with the trial that showed it}

**5.2 Inferences:** {what follows from the observations, marked as inference}

**5.3 Open questions:** {what the trials did not settle}

**5.4 Reuse decision:** findings only | findings and prototype code | undecided

**5.5 Code paths to reuse:** {paths on the prototype branch, or "None"}
