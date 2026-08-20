# Calendar Management

**Type** Cowork skill  ·  **Trigger** Any date, meeting or commitment surfacing in conversation, plus a briefing offer ahead of external meetings

## What it does

Watches every working session for calendar-relevant facts. When a date, meeting or deadline appears in a message, email, draft or document, it checks the calendar for that day and its neighbours, tests for duplicates, and offers to create or update the event. Ahead of upcoming external meetings it checks whether a briefing already exists and offers to build one, a structured prep note covering objective, attendees, current state, points to raise, desired outcome, likely objections with responses, and linked documents.

## Flow

```
date or meeting surfaces in conversation
   |
anchor today (live clock) ──> list events, day ± 1
   |
duplicate? ──yes──> stop
   |no
same day, details differ ──> show the diff ──> human decides
   |
propose create ──> human confirms ──> event written
   |
external meeting inside 24h, no briefing ──> offer once
   ──> counterparty file + latest records ──> prep note
```

## How it runs

The current date is always computed live, never recalled, every calendar decision anchors on a verified today. The duplicate test is title overlap on the same calendar day. A same-day event whose time, place or attendees differ is surfaced as a diff and the human decides. Meetings qualify as external when an attendee domain is outside the company or the context names a counterparty, and the briefing offer fires once per session, never repeated. Briefings are built from the counterparty's file in the private context repo, the current master plus the latest session records.

## Guardrails

No event is created, moved or deleted without explicit confirmation, the skill proposes and the human disposes. Service providers are referred to by category descriptors unless an agreement or an explicit approval covers the name. Counterparty classification is by domain and file location, and ambiguity is asked, not guessed.

## Lessons carried

A model's memory of what day it is cannot be trusted, every date is computed from a live clock. Silent calendar edits erode trust faster than missed events, which is why the confirm-first rule has no exceptions.
