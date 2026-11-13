# Design guide

Patterns that have made plans or code worse. Read it before proposing a design. Reviewers check plans and diffs against it.

Add a rule when you catch a pattern that would apply beyond that one change. Keep it to one or two sentences: what to avoid, how to tell, and what to do instead. Add a row to the rationalizations table when an excuse for breaking a rule shows up.

1. **No responsibility creep on existing classes.** For each existing class the plan changes, state its responsibility before and after. If "after" needs "and", move the new behavior to a new class or to the class that already owns it.
2. **No convoluted logic.** If a method needs nested conditions, a flag parameter that switches its behavior, or a condition of more than two terms to describe, give each case its own method or name each condition as a predicate. If the story itself is that complex, ask the user instead.

## Rationalizations

| Rationalization | Reality |
| --- | --- |
| "It's only one more method on an existing class." | Every bit of creep is one method. State the responsibility before and after. |
| "A new class is overkill for this." | Size isn't the test; responsibility is. |
| "A flag parameter keeps it in one place." | It puts two behaviors in one place. The caller already knows which case it wants. |
| "The story is complex, so the logic has to be." | Then ask the user. Encoding it silently freezes the complexity into the plan. |
| "The existing code already does it this way." | Match its granularity, not its defects. Raise the defect as a question. |
| "A later slice can clean it up." | Later slices implement the plan exactly. Fix the plan now. |
