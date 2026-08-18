# CRM KPI Dashboard

**Status** Live  ·  **Runtime** Cowork  ·  **Trigger** Weekly scheduled run

## What it does

Produces a weekly summary of the top ten tasks owed, read across email and the CRM. One ranked list with owner and age, so the week starts with the ten things that actually matter rather than whatever surfaced last.

## Why it exists

Obligations live in two systems that do not talk, commitments made in email threads and tasks tracked on the board. Neither surface shows the union, and the gap is where owed work hides. The dashboard closes the gap once a week with zero human collection effort.

## Architecture

```
Schedule (weekly)
   |
Read email commitments window
   |
Read open CRM tasks
   |
Merge, dedupe, rank by weight and age
   |
Top-ten summary delivered
```

## Harness

**The loop** is a single weekly pass, read-only against both sources, no writes to either system, the output is a report.

**Context assembly** is the merge itself, matching email-thread commitments to board tasks so the same obligation is not counted twice.

**Stop conditions.** The email window and the open-task set bound each run, and run cost is watched, an early token usage spike paused the agent until its read pattern over the email window was understood, and cost per run is now a monitored stop condition rather than a surprise.

**Human gates.** Read-only by design, the report informs the week, it changes nothing by itself.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Runtime | Cowork scheduled task | Cross-source reading plus ranking judgement |
| Email | Gmail | Where commitments hide |
| CRM | Asana | Where tasks are tracked |

## Failure modes and handling

Token cost blowout. The live failure in this agent's history, a usage spike large enough to pause it. Handled by pausing first and diagnosing before running again, an agent whose cost is not understood does not run, and run economics are now watched per run.

Double counting. The same obligation in a thread and on a card would occupy two of the ten slots. Handled in the merge by matching before ranking.

## Impact

Owed-work review was manual before this, two surfaces read separately and the union never assembled in one place. The agent runs weekly and delivers the ranked ten from both sources, and its one production incident, a token usage spike, was handled by pausing and diagnosing rather than absorbing the cost, which is the discipline the whole fleet follows. As an estimate, it returns a planning hour per week, assuming the manual union would otherwise be assembled, which it was not. Reply and completion behaviour on the surfaced ten is not measured.
