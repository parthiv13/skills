# Testing

## Test execution

- Run tests with the project's test command from the "Project profile" section of `AGENTS.md`.
- Run the narrowest test target that covers the change, not the whole suite, unless the change is broad.
- If a test run fails because of an environment or configuration problem, diagnose and fix it, or surface the blocker. Do not work around it with an ad-hoc command.
- Run each test command after a change that could affect the result. After a clean run, do not repeat the same command unless the code has changed since.

## Writing good tests

- **Test state, not interactions.** Assert the outcome of an operation, not which methods were called internally. Call-sequence tests break on refactoring even when behavior is unchanged.
- **DAMP over DRY.** Duplication is acceptable when it makes each test independently understandable.
- **Isolate the unit.** In a unit test, every collaborator of the class under test is a test double. Pure values and helpers stay real. Above unit level, prefer real implementations.
- **Use the simplest double that works**, in this order:
  1. Real implementation: highest confidence, catches real bugs.
  2. Fake: in-memory version of a dependency, such as a fake database.
  3. Stub: returns canned data, no behavior.
  4. Mock (interaction): verifies method calls. Use sparingly.
- **Keep doubles strict.** "Test state, not interactions" decides whether to assert a call. These rules decide how strictly:
  1. A stub answers only the arguments the test expects. A stub that returns the same value for any input hides a call with the wrong argument.
  2. Assert a call on a double only when the call is the observable outcome, such as a write to an I/O service. Then assert its exact arguments. Do not use an any-matcher or a bare "was called" assertion.
  3. Bind every double to the real collaborator's signature with the language's spec or autospec feature, so a signature change breaks the test.
- Test only your code, not framework or third-party behavior.
- Keep tests deterministic. No dependence on timing or test order.
- Each test sets up and tears down its own state, so tests pass individually and together.

## Test organization

- Keep tests for a production unit in its matching test file.
- Group tests for each distinct method or cohesive feature together. Put the different cases for that method or feature in separate test functions or methods, not one function per case.
- For a maximum count or cap of `x`, test inputs with `x-1`, `x`, and `x+1` eligible items. Assert the selected items and any relevant side effects at each boundary. If a boundary case cannot occur for the behavior under test, state why.

## Rationalizations

| Rationalization              | Reality                                                                  |
| ---------------------------- | ------------------------------------------------------------------------ |
| "This is too simple to test" | Simple code gets complicated. The test documents the expected behavior.  |
| "It's just a prototype"      | Prototypes become production code. Tests from day one prevent test debt. |

## Red flags

- Writing code without a corresponding test.
- "All tests pass" when no tests were actually run.
- Test names that don't describe the expected behavior.
- Skipping tests to make the suite pass.
