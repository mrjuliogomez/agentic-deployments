# CRM Opportunity Logger

**Status** Live  ·  **Runtime** n8n Cloud  ·  **Trigger** Nightly scheduled run

## What it does

Registers qualified opportunities from the CRM board into the Partner network's portal. Each night it polls the engaged stage of the board, skips every card already carrying the logged marker, and submits each remaining opportunity through the portal's quote form via a headless browser. On a confirmed record it posts an audit comment to the card and sets the follow-up due date. Cards never move stage, registration is a side effect, not a pipeline event.

## Why it exists

Registering an opportunity by hand took ten to twenty minutes of typing one form field at a time, per opportunity, from data scattered across threads and cards. Now it takes no human time at all, and the work compounds instead of evaporating. Every registration leaves a record, the deduplication ledger grows by itself, the card gets its audit comment and due date, and the research done to fill the form feeds straight back into the CRM, so everyone working the board gains from each registration. The agent makes registration automatic, idempotent and auditable.

## Architecture

```
Schedule (nightly)
   |
Fetch engaged-stage cards (CRM)
   |
Skip cards carrying the logged marker (no cost spent)
   |
Card parse ── VAT identifier checksum validation
   |
Headless browser ──> portal quote form ──> submit
   |
confirmed record? ──no──> hold, comment for review
   |yes
Audit comment on card ──> due date set ──> ledger append
```

## Harness

**The loop** is a nightly batch over one board stage, idempotence first, the marker check runs before any model or browser cost is spent, so a done card costs nothing and can never re-register.

**Deduplication** works in three layers. First the card marker, on a confirmed registration the agent writes an audit comment to the card, and on every later run that marker is checked before anything else, so re-registration from the same card is impossible. Second the ledger, every confirmed registration appends a row to a board-wide registration ledger keyed by the company's VAT registration identifier. The key is the company, not the card, so the same company reached through a different card, a different board section or a renamed lead still collides with its existing ledger row. Names drift, identifiers do not. Third the checksum, VAT registration identifiers are checksum-validated in code before submission, so a mistyped identifier can neither register against the wrong company nor corrupt the ledger key. The automatic ledger gate inside the nightly run is the standing open item, today the marker and the checksum run in-line and the ledger is reconciled against the portal by hand, stated here rather than hidden.

**Context assembly** is the card itself, the canonical contact and company blocks maintained upstream by the [record sanitiser](../crm-record-sanitiser/README.md), including the VAT registration identifier the form requires.

**Tool bindings** are the CRM REST API over HTTP and a headless browser service driving the portal form. The portal has no API, so the form was reverse-engineered field by field, including the checksum the portal validates client-side, which the agent validates itself before ever opening a browser.

**Stop conditions.** No confirmed record, no card write. The success signal is the portal's confirmation redirect and it is treated as a signal rather than a receipt. Unparseable or checksum-failing cards are held with a comment instead of submitted wrong.

**Human gates.** The ledger is reconciled by hand against the portal, and portal-side cleanup of test or duplicate records goes through the human because the portal offers no partner-side deletion.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Orchestration | n8n Cloud | Shared platform across the agent fleet |
| CRM | Asana via REST over HTTP | The board is the single working surface |
| Form submission | Browserless headless browser | The portal has no API, the form is the API |
| Validation | VAT identifier checksum in code | Reject bad identifiers before they reach the portal |
| Ledger | Markdown in the private context repo | VAT-keyed registration history, human-readable |

## Failure modes and handling

Double registration. The worst outcome, it reads as carelessness to the network. Handled by the three dedup layers above, marker, VAT-keyed ledger, checksum.

Registering junk. A mistyped VAT registration identifier fails at the portal or, worse, registers the wrong company. Handled by validating the checksum in code before submission and holding failures for the human.

Phantom success. The portal's confirmation page is generic. Handled by treating the redirect as a signal, writing the audit trail only after it, and reconciling the ledger against the portal by hand.

Retry loops wallpapering cards. A held card once collected three identical hold comments in five minutes. Comment dedup on the retry path is a known open item, stated here rather than hidden.

## Impact

Registration went from ten to twenty minutes of hand-typing per opportunity to zero human time. The agent was validated end to end on live opportunities, real registrations confirmed in the portal with audit comments and due dates written back, and no double registration has occurred on marked cards. The ledger holds the full registration history, over one hundred and thirty records keyed by VAT registration identifier, and the research gathered per registration enriches the CRM card rather than dying in the form. What is not measured is the commercial value of registration priority and the rate of portal-side conflicts avoided.
