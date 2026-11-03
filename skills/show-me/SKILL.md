---
name: show-me
description: Help the user understand the current topic visually with concise diagrams, code-shape sketches, and focused HTML artifacts.
disable-model-invocation: false
---

Help the user understand the current topic of conversation visually. Skip the preamble and keep prose brief. Pick the smallest view that makes the key point clear.

The reader knows this repository well and does not take LLM output on trust. Every name in a sketch must be something they can search for and navigate to. Prefer phrases that map directly to code over descriptive prose.

- Show logic or an algorithm as pseudocode written in the repository's own language, using the real class, method, function, and variable names. Write conditions as real expressions, and write actions as the actual call that would run. Do not use English placeholders such as `if content is unchanged` or `write new content`. Elide irrelevant code with `...` and add a `# path/to/file` comment so the location is clear. Verify names by reading or searching the code first; never present an invented name as existing. When the topic is hypothetical or proposed (new code, a design option), inventing names is fine, but follow the repository's naming conventions and mark them as proposed, for example with `# new`.

The examples below use Python syntax for illustration only. Write the repository's language.

```python
# src/orders/repository.py
class OrderRepository:
    def save(self, order: Order) -> SaveResult:
        if self._hash(order.content) == self._cached_hash:
            return self._cached_result
        self._writer.write(order)
        return self._build_result(order)
```

- Show runtime control flow as a call tree using real function and method names, with the owning file or class where it helps navigation:

```text
CheckoutService.submit_checkout
  CheckoutService.create_order
    OrderRepository.save
    PaymentGateway.charge
  CheckoutRouter.navigate_to_confirmation
```

- Show how classes or modules are composed (what owns, injects, or depends on what) as a structure tree, using real class names and their file paths:

```text
CheckoutService (src/checkout/service.py)       # orchestrates checkout
├── OrderRepository (src/orders/repository.py)  # persists orders
└── PaymentGateway (src/payments/gateway.py)    # charges payments
```

- Show file responsibility or a broad refactor as a shallow file tree:

```text
src/
├── api/        # parses incoming requests
├── checkout/   # owns checkout flow
└── payments/   # calls the payment provider
```

- Whenever more than one file or class is shown, state the responsibility of each one, either as a `#` comment in the tree or as a one-line note beside it. Never list files or classes by name alone.

- Show component interaction, control flow, or data flow with Mermaid. Use real class, function, and module names for participants, nodes, and messages (for example the actual method being called), not generic labels:

```mermaid
sequenceDiagram
    participant Caller
    participant CheckoutService
    participant PaymentGateway
    Caller->>CheckoutService: create_order(request)
    CheckoutService->>PaymentGateway: charge(order)
    PaymentGateway-->>CheckoutService: ChargeResult
    CheckoutService-->>Caller: Order
```

- Use `diff` when the point is what changes and the surrounding shape already exists. Match the diff shape to the topic.

For a file-layout change:

```diff
 src/
 ├── api/
+│   └── webhooks.py      # receives payment events
 ├── checkout/
-└── payments.py
+└── payments/
+    ├── gateway.py       # calls the payment provider
+    └── events.py        # parses webhook payloads
```

For a call-tree or call-stack change:

```diff
 CheckoutService.submit_checkout
   CheckoutService.create_order
     OrderRepository.save
+    DiscountService.apply_discount
     PaymentGateway.charge
   CheckoutRouter.navigate_to_confirmation
+    EventStream.subscribe
```

For a state or control-flow change:

```diff
 def save(self, order: Order) -> SaveResult:
-    self._writer.write(order)
+    if self._hash(order.content) == self._cached_hash:
+        return self._cached_result
+    self._writer.write(order)
+    self._cached_hash = self._hash(order.content)
     return self._build_result(order)
```

- Show the whole block when most of it is new, when omitted context would hide ownership or order, or when the user needs a copyable target shape:

```python
# src/pricing/discount.py
def percent_off(code: str) -> int:
    digits = code.removeprefix("SAVE")
    return int(digits)
```

- For a flow, layout, state comparison, or concept too dense for Mermaid, write one focused HTML file: a diagram, an infographic, or a short slide deck, whichever fits the point. Use real labels and data (actual class, method, and file names), and support desktop and mobile. Then open it for the user with the platform's opener, for example on macOS:

```bash
open path/to/show-me-{description}.html
```

### guidance

Place each visual next to the short text it supports. Keep only the calls, files, classes, states, and boundaries needed to answer the user's current question or the options to resolve the current discussion point. Use the repository's language for code sketches, and never swap real identifiers for descriptive wording.

You may use one of these, you may use several, it is unlikely you will use all of them. Use your judgement and don't overwhelm the user.
