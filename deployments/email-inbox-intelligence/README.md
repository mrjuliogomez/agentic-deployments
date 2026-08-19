# Email Inbox Intelligence

**Status** Live  ·  **Runtime** Cowork  ·  **Trigger** Daily scheduled run

**Relationship.** Shares infrastructure with [crm-lead-screening](../crm-lead-screening/README.md), feeding the email channel into the same record pool and ruleset.

## What it does

Triages every incoming email by content and acts on it, four lanes.

Meeting proposals. Checks the calendar, researches the counterparty's background, writes a short prep note, adds the event, and leaves a reply in the drafts folder for review. Reschedule replies are picked up on later runs and the calendar is updated.

Tasks. Creates the task in the CRM with owner, due date, source documents and notes attached, so work starts without further research.

Project follow-ups and reports. Updates the corresponding peripherals, CRM items, spreadsheets or project specifications, so project state stays current without a reading session.

Irrelevant mail. Filed out of the inbox.

## Why it exists

The failure mode without it is not missed email, it is misallocated attention. Four hundred to six hundred messages standing in the inbox meant every working session started with digging, and the digging displaced the priority tasks. Delays compounded quietly. The agent moves the digging out of the human day entirely and leaves an inbox that contains only what needs a human decision.

## Architecture

```
Schedule (daily)
   |
Fetch inbox window
   |
Classify by content (four lanes)
   |
   ├── MEETING ──> calendar check ──> background research
   |               ──> prep note ──> event added ──> reply in drafts
   |               reschedule replies ──> calendar updated
   ├── TASK ──> CRM task (owner, due date, sources, notes)
   ├── PROJECT ──> update CRM items / spreadsheets / specifications
   └── IRRELEVANT ──> filed out of the inbox
   |
Run report
```

## Harness

**The loop** is one daily pass over the inbox window, each message classified once and routed to exactly one lane.

**Context assembly** is per lane. The meeting lane pulls calendar state and researched counterparty background before writing a word. The task lane gathers the source documents the task will need so the assignee starts with everything attached. The project lane reads the current state of the peripheral it is about to update.

**Tool bindings** are Gmail, the calendar, the CRM, spreadsheets and web research, all through the agent's standard tool surface.

**Stop conditions.** The inbox window bounds each run, and anything the classifier cannot place with confidence stays in the inbox for the human, filing waits for confidence.

**Human gates.** Every outbound reply is a draft, the human sends. Filing and peripheral updates run autonomously, they are reversible. The prep note and the attached sources are decision support, the decisions stay human.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Runtime | Cowork scheduled task | The agent surface with native reach into every tool below |
| Email | Gmail | Where the inbox is |
| Calendar | Google Calendar | Event writes and conflict checks in one place |
| CRM | Asana | Tasks land where the work is managed |
| Documents | Spreadsheets and project files | Peripherals updated in place, no parallel copies |
| Research | Web search | Counterparty background for prep notes |

## Failure modes and handling

Misclassification filing something that mattered. The costliest possible error. Handled by an asymmetric confidence rule, low-confidence mail stays in the inbox for the human, only confident calls act.

Reply sent on a stale thread. Handled by keeping every reply a draft, the human is the currency check at send time.

Reschedules missed after the event was created. Handled by treating reschedule replies as a first-class lane on later runs, the calendar entry follows the thread wherever it moves after creation.

Task cards created without context. A task with no sources just moves the digging downstream. Handled by attaching source documents and notes at creation, the card is complete or it is not created.

## Impact

The baseline was four hundred to six hundred emails standing in the inbox, every session opening with triage and priority work displaced by digging, with delays compounding because focus went to the pile instead of the tasks. The standing inbox went from hundreds to single digits and stays there run over run, meetings arrive with a prep note, an event and a ready draft, and tasks arrive with sources attached. As an estimate, one to two hours of daily triage and context-gathering are returned, assuming the baseline pile would otherwise be worked at all, historically it was deferred, which is the compounding failure the agent removes. Response latency to counterparties, calendar accuracy over time and the downstream completion rate of tasks the agent creates are not measured.
