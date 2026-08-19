# Partner Onboarding Form Filler

**Status** Live  ·  **Runtime** Cowork  ·  **Trigger** On demand, one email address as input

## What it does

Turns a single input, the prospect's email address, into a complete onboarding packet for the service provider's intake process. It locates the email thread, extracts every contact's details verbatim from signature blocks, finds or updates the lead's CRM card across the whole board, pulls the meeting minutes from the recorded-meetings drive, pre-fills the provider's data-intake PDF with everything already known, writes the accompanying email in the house register as a draft, and logs the minutes and full transcript to the card. The human reviews, attaches and sends.

## Why it exists

The intake document is the bridge between a won meeting and the provider's proposal, and assembling it by hand meant re-reading the thread, hunting the minutes, retyping contact data and rebuilding the same PDF every time. Worse, hand assembly leaks errors into exactly the artefact the provider prices from. The agent compresses the whole assembly to one instruction and makes the packet's data provenance verbatim-from-source.

## Architecture

```
Input, one email address
   |
Locate thread (Gmail) ──> extract contacts verbatim from signatures
   |
Find CRM card, full-board scan, update never duplicate
   |
Due date set (fixed offset)
   |
Fetch meeting minutes (Drive) ──> confirmed facts pre-filled
   |
Build intake PDF (code-rendered, fillable fields)
   |
Email draft in house register ──> minutes + transcript logged to card
   |
Human reviews, attaches, sends
```

## Harness

**The loop** is a single orchestrated pass with the email address as the only required input, everything else is derived.

**Context assembly** is layered, thread first for people and state, signature blocks for verbatim contact data, the meeting minutes for confirmed facts, and the provider's document spec from the private context repo for the PDF layout.

**Tool bindings** are Gmail, the CRM REST API, the drive holding meeting records, the context repo for the builder spec, and a code-rendered PDF pipeline.

**Stop conditions.** No thread found means stop and ask, some conversations live in unconnected mailboxes and guessing is forbidden. Registry identifiers are verbatim from source or absent.

**Human gates.** The email never sends itself, it is a draft, and attachments are added by hand at send time. One card per lead is a hard rule, the agent always updates the furthest-stage card, and a duplicate created in error is consolidated and deleted.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Runtime | Cowork skill | Multi-tool orchestration with judgement at each step |
| Email | Gmail | Thread history and the outbound draft |
| CRM | Asana | Card find-or-update across the full board |
| Meeting records | Google Drive | Minutes and transcripts from recorded meetings |
| Document build | Code-rendered PDF | Deterministic layout, fillable fields, brand by code |
| Document spec | Private context repo | The intake layout versioned outside the session |

## Failure modes and handling

Duplicate cards. The CRM's text search is unreliable and once caused a duplicate that had to be consolidated by hand. Handled by scanning every section of the board by name and by known abbreviation before any create, update always beats create.

Invented data in the packet. A wrong registry identifier prices the wrong company. Handled by verbatim-from-source discipline, identifiers and contact data come from the thread, the registry or the minutes, never from inference.

Wrong-language or off-register copy. The accompanying email is in the prospect's language with the provider referred to generically, the naming discipline lives in the copy rules fetched from the context repo.

Stale card state. The card may already sit in a later stage than the thread suggests. Handled by furthest-stage-wins, the agent enriches the existing card and never moves it backwards.

## Impact

Packet assembly by hand cost an hour or more per prospect, thread re-reading, contact retyping, minutes hunting and per-prospect PDF rebuilding, with error risk in the priced artefact. Live on real prospects, worked cases produced the filled PDF, the enriched card with minutes and transcript logged, correct due dates and a register-true draft, including one case where the full-board scan caught an existing late-stage card that text search had missed. The estimated return is about an hour per prospect, and one avoided duplicate card or mispriced identifier per batch is worth more than the time. Provider-side turnaround uplift and completion rates of returned forms are not measured.
