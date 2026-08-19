# CRM Lead Card Create

**Type** Cowork skill  ·  **Trigger** Any instruction to card a lead, from an email thread, a profile, a screenshot or a bare company mention

## What it does

Turns whatever input arrives into a complete, registrable lead card in the CRM. Extracts the contact and company, deduplicates against the whole board, disqualifies structurally hopeless entries before spending effort, researches the legal entity from public registries, builds the card to the canonical block standard defined in [crm-lead-card-standard](../crm-lead-card-standard/README.md), places it in the stage the evidence supports, and records provenance in a creation comment.

## How it runs

The order is deliberate. Dedupe first, same company and same contact means update the existing card, never create, while the same company with a different contact is a parallel opportunity, both cards live and cross-linked. Disqualify second, an entity that can never be registered, foreign with no local company, dissolved, or a test placeholder, goes straight to the closed lane with a reason and consumes no research. Research third, a fixed source ladder from plain search through registry aggregators to the company's own legal notices, and a value is only marked pending after the ladder is exhausted with the searches named. The VAT registration identifier is checksum-validated in code, a well-formed but invalid identifier is recorded as pending, never as the identifier.

The hard gate is the completeness checkpoint. A card is not done until every required field is present and none is pending. If research cannot fill them all, the card moves to an incomplete holding section, open, with the exact missing fields named for a human, never left looking finished in a live lane.

## Guardrails

Legal names, identifiers and addresses are verbatim from source, never invented, and reconstructions are flagged for verification. One creation call per card, never bulk. Stage placement follows evidence, and where the phase is not unambiguous the skill asks first, a wrong stage either cold-pitches a live account or registers an opportunity that had no real interaction.

## Lessons carried

The completeness checkpoint exists because cards that looked finished but carried a missing field reached the registration agent and were held there, an orphan card in a live lane is a defect that surfaces days later in someone else's run. The parallel-opportunity rule exists because treating every same-company match as a duplicate was quietly deleting second doors into good accounts.
