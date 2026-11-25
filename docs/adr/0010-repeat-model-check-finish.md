# 0010. Repeat model: Check and Finish

- Status: Accepted
- Date: 2026-11-25

## Context

OmniFocus repeats forever. The author needs to end a repeating task "for good"
and have its dependents become available. Two real cases disagree on due
dates: "Draft report" (repeat daily, finish within a week) keeps a fixed
deadline; "Pay bill" (due every 15th) moves its deadline each time.

## Decision

- A repeating task has two completion actions: **Check** (log an occurrence,
  advance dates) and **Finish** (complete for good, unblock dependents).
  Dependents stay blocked until Finish.
- Next dates are computed **from completion** or **from schedule**.
- A repeating task's due date targets the **occurrence** (default; advances on
  Check) or the **series** (fixed deadline for Finish). The model allows both.
- Rules are stored as RFC 5545 **RRULE**. The v1 UI offers presets and
  nth-weekday rules; the engine accepts any valid rule.
- Every Check is kept as history.

## Consequences

- "Check" and "Finish" are part of the domain language.
- Rules like "first Monday after the second Sunday" work via RRULE
  (`BYDAY=MO;BYMONTHDAY=9..15`) before the UI supports them.
- The occurrence-due default is to be revisited after real use.
