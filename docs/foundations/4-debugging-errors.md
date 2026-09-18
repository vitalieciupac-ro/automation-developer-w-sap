# 4. Debugging, Error Handling & Logging

Beginners often think a program is finished when it runs once. Making it behave correctly when something goes wrong is the harder, more valuable skill, and it is what this module covers.

!!! abstract "What you'll be able to do"
    - Debug methodically with breakpoints, stepping, and the Locals/Immediate/Watch panels.
    - Distinguish **application** exceptions from **business rule** exceptions and handle each.
    - Use Try Catch, Throw/Rethrow, Retry Scope, and the Global Exception Handler.
    - Write logs at the right level so a supported robot can be diagnosed.

## Two kinds of failure

| Exception type | Means | Right response |
|----------------|-------|----------------|
| **Application / System** | Something technical broke (app crash, element missing, file locked) | Often retry; log the details |
| **BusinessRuleException** | Data violates a rule (over limit, missing field) | Don't retry, route to a defined path / human |

Retrying a `BusinessRuleException` never helps, the data is the problem, not the timing.

## Debugging toolkit

- **Breakpoint + Step Into / Over / Out**: pause and walk through execution.
- **Locals / Immediate / Watch**: inspect and evaluate variables mid-run.
- **Slow Step & Highlight**: watch what the robot does and which element it targets.

## Handling tools

- **Try Catch**: attempt risky work, catch *specific* exception types, use **Finally** for cleanup.
- **Throw**: raise a `BusinessRuleException` deliberately when a rule is broken.
- **Rethrow**: pass a caught exception up after logging it.
- **Retry Scope**: auto-retry transient failures with a condition.
- **Global Exception Handler**: a project-wide safety net that decides Continue / Retry / Ignore / Abort.

!!! warning "Two anti-patterns to avoid"
    - **Catching `System.Exception` for everything** (the *swallow all*). Catch the specific type you understand.
    - **Empty catch blocks.** A catch with no log is a silent failure. Make it a rule: *every catch logs.*

## Logging, use the ladder

`Trace` (noisy detail) · `Info` (milestones) · `Warn` (recoverable oddity) · `Error` (handled failure) · `Fatal` (cannot continue).

!!! tip "Why logging matters"
    Logs are how the person supporting this later, possibly you, finds out what happened. Use **Add Log Fields** to attach context (like an `InvoiceId`) to every later log.

---

## Try it: Hardening the Fragile Process

!!! example "Scenario"
    You inherit a workflow that crashes on bad input and tells no one why. Make it diagnosable and resilient: handle the technical failure, flag the business rule breach, and log both clearly.

**Packages:** none beyond the default `UiPath.System.Activities` (Try Catch, Throw, and `BusinessRuleException` are all in it).

**What to produce**

- The `FragileProcess` updated with **Try Catch** around the risky work, a `BusinessRuleException` for out-of-range amounts, and **Log Messages** at appropriate levels.
- A run that completes cleanly on good input and logs a meaningful message on each bad input instead of crashing.

**You are given**

- The `FragileProcess` starter project (reads a list of amounts and divides a budget by each).
- Business rule: an amount above `1000000` must raise a `BusinessRuleException`.

**Hints**

- Use **Debug** + a breakpoint first to see exactly where and why it fails.
- Catch the *specific* exception type, not `System.Exception` for everything.
- Every catch must log. Info for milestones, Warn for recoverable oddities, Error for handled failures.

??? success "Solution"
    Open `FragileProcess`. Run it once and note the exception (a divide-by-zero / format error on bad input).

    1. Set a **Breakpoint** on the failing activity. **Debug File**, then **Step Over** and read the offending value in **Locals**.
    2. Wrap the risky activity in a **Try Catch**.
    3. In **Try**: before processing, add an **If** → when `amount > 1000000`, **Throw** `New BusinessRuleException($"Amount {amount} exceeds limit")`.
    4. Add a **Catch** for the specific technical type you saw (e.g. `FormatException`) → **Log Message** (Error) with `exception.Message`.
    5. Add a separate **Catch** for `BusinessRuleException` → **Log Message** (Warn) with the message; continue to the next item.
    6. At the start of each item add **Add Log Fields** with the current amount so every later log carries context.
    7. Add **Log Message** (Info) `"Processing complete"` after the loop.
    8. Re-run on the mixed input. Confirm no crash: good values process, over-limit values log a **Warn**, bad values log an **Error**.

    **Expected result:** the process runs to completion on all inputs; the Output panel shows Info milestones, a Warn for the over-limit amount, and an Error (with message) for the malformed value, no unhandled exception.

[Next: Email & PDF](5-email-pdf.md){ .md-button .md-button--primary }
