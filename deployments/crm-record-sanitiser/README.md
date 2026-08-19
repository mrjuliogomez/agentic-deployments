# CRM Record Sanitiser

**Status** Live  ·  **Runtime** Cowork  ·  **Trigger** Nightly scheduled run

## What it does

Cleans the prospect-curation stage of the CRM board every night. Defective cards are rewritten to the canonical lean blocks, titles normalised to the board convention, company registry data researched and filled from public sources, empty angle blocks drafted, sectors tagged, confirmed duplicates relocated to a quarantine section, parallel opportunities documented, and anything the agent cannot fix is flagged for the human with the blocker named. It never advances cards, never creates them, never seeds new prospects. Downstream agents depend on its output, the [discovery follow-up agent](../crm-discovery-followups/README.md) and the [opportunity logger](../crm-opportunity-logger/README.md) both read the blocks it standardises.

## Why it exists

Every downstream agent parses cards by structure. One hand-written card with a missing block or an invented email poisons the chain, a follow-up drafted to a wrong address, a registration against the wrong company. The sanitiser makes card hygiene a nightly guarantee instead of a hope, and it enforces the evidence bar, a contact email only counts if a verifiable source backs it, data-broker listings do not qualify.

## Architecture

```
Schedule (nightly)
   |
Fetch prospect-curation cards (CRM)
   |
Per card
   ├── structure check ──> rewrite to canonical blocks
   ├── title normalisation
   ├── registry research (VAT identifier, registered address, public sources)
   ├── angle draft where empty
   ├── sector tag
   ├── duplicate check ──> relocate confirmed duplicates
   └── unfixable ──> flag on card, never guess
   |
Run report
```

## Harness

**The loop** is one nightly pass, per card, strictly non-destructive of meaning, the agent restructures and enriches but never invents. Identity data comes from public registries and the card's own history, and every researched fact lands with its source.

**Stop conditions.** The evidence bar is the hard gate, a card whose contact email cannot be verified from an allowed source is marked blocked and held for the human, and a duplicate is only relocated when confirmed, ambiguity stays put with a flag.

**Human gates.** The agent never advances a card to the working pipeline, promotion is a human act. The blocked queue is worked by the human confirming emails from his own correspondence, one confirmation flips a card to ready.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Runtime | Cowork scheduled task | Research plus judgement per card needs an agent, not a fixed workflow |
| CRM | Asana | The board being sanitised |
| Registry research | Web search over public sources | VAT identifiers and addresses verbatim from source, never inferred |
| Doctrine | Private context repo | The canonical block spec and evidence bar, versioned |

## Failure modes and handling

Invented data. The catastrophic failure for a sanitiser, a plausible wrong email is worse than a blank. Handled by the evidence bar, verbatim-from-source discipline, and flagging over guessing. The deep-clean audit showed most cards blocked precisely because no allowed source carried a verifiable email.

Destroying human context. Rewriting a card can lose a note a human relied on. Handled by rewriting structure only, enrichment appends and consolidates, and the card's history stays whole.

False duplicate relocation. Merging two genuinely different companies hides an opportunity. Handled by relocating only confirmed duplicates, name similarity alone stays flagged on-card.

Downstream coupling. Other agents parse the blocks this agent writes, a format change here breaks them silently. Handled by treating the canonical block spec as a shared contract versioned in the context repo.

## Impact

The curation stage was hand-written and inconsistent, missing blocks, unverifiable emails and duplicate cards, and every downstream agent inherited the mess. A full autonomous deep-clean dispositioned thirty-four cards in one pass with a final audit, one promoted with a verified thread-sourced email, seven parked with reasons, twenty-six blocked on the evidence bar with the blocking fact named per card, and zero duplicates remaining. The nightly pass has kept the stage canonical since. As an estimate, manual sanitisation of the same stage is a multi-hour purge that was in practice never done, the nightly agent amortises it to zero. Not measured are the downstream error rates avoided in the follow-up and registration agents attributable to clean input.
