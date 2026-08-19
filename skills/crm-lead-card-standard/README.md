# CRM Lead Card Standard

**Type** Cowork skill, canonical reference  ·  **Trigger** Every create, update or sanitise touch on a lead card, by human or agent

## What it does

Defines the single card standard the whole agent fleet reads and writes. One card per lead. Identity lives in the card notes as fixed plain-text blocks, contact, company, sector, angle. Pipeline position lives in the board stage. History lives in append-only comments carrying both a human line and a machine-readable touch ledger. The standard is board-agnostic and load-bearing, the [follow-up agent](../../deployments/crm-discovery-followups/README.md), the [opportunity logger](../../deployments/crm-opportunity-logger/README.md) and the [record sanitiser](../../deployments/crm-record-sanitiser/README.md) all parse cards by this contract.

## How it runs

The first contact line is the deterministic recipient, position carries the meaning and the outbound agent emails exactly that person, so recipient correctness is a hard gate. Won-stage economics travel on a single machine-parseable value line holding the net figure, its source references and its update date. Conversation state is never asserted from search previews, the full thread is read for every contact on the card before any claim about who said what last. Duplicates are defined narrowly, same company and same first contact, anything else with the same company is a parallel opportunity kept live and cross-linked. Stage moves follow furthest-phase-wins, and a card never sits in the automated cold-stage lane while a live human conversation is running, the automated agent would fire on top of it.

## Guardrails

Comments are append-only, a wrong ledger line is superseded by a new comment, never edited. Spent social-proof references are recorded only in the comment ledger so the outbound agent can rotate them spent-aware. A send gate bars any automated outbound from a card with a malformed recipient, a missing identifier or an empty angle. The counterparty network is never copied or named in cold or pre-meeting mail, a hard stop.

## Lessons carried

Nearly every rule here is a named incident hardened into law. Truncated search previews caused two false claims about conversation state in one day, hence the full-thread rule. A cloned card silently inherited the wrong product and sat on the wrong board, hence the clone-defect correction rule. A live conversation sitting in the automated lane nearly collided an agent follow-up with a human thread, hence the stage bar. The standard grows only by rulings, and copies of it never live inside agent prompts, agents get pointers, because copied rules drift.
