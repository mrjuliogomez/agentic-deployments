# CRM KPI Dashboard

**Status** Paused  ·  **Runtime** Cowork  ·  **Trigger** Weekly scheduled run

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

**Stop conditions and the pause.** The agent is built and ran, and is paused on a token usage spike under investigation, the read pattern over the email window is the suspect. It stays paused until the spike is diagnosed rather than run expensively.

**Human gates.** Read-only by design, the report informs the week, it changes nothing by itself.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Runtime | Cowork scheduled task | Cross-source reading plus ranking judgement |
| Email | Gmail | Where commitments hide |
| CRM | Asana | Where tasks are tracked |

## Failure modes and handling

Token cost blowout. The live failure, a usage spike large enough to pause the agent. Under investigation, and the agent stays paused until the read pattern is redesigned, stated here because an agent whose cost is not understood does not run.

Double counting. The same obligation in a thread and on a card would occupy two of the ten slots. Handled in the merge by matching before ranking.

## Impact

Owed-work review was manual before this, two surfaces read separately and the union never assembled in one place. The agent was built and ran end to end, producing the ranked ten from both sources. Run economics are the open question, the token usage spike that paused the agent is under investigation, and until cost per run is understood and acceptable it stays paused. The estimated return once live is a planning hour per week, assuming the manual union would otherwise be assembled, which it was not.
