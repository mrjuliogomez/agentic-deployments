# 0003 Card ledger as cadence memory

**Status** Accepted

## Context

The cadence engine needs to know how many touches each lead has received and when the next is due. That state has to live somewhere the agent can read and write daily, survive workflow redeploys, and stay legible to the human working the same board.

## Options

1. An external store, a database or sheet keyed by card.
2. The card itself, a structured ledger comment per touch plus the card's due date, parsed back by the agent on each run.

## Decision

Option 2. The board is already the operating surface, so state written anywhere else eventually disagrees with what the human sees. A ledger comment is simultaneously machine state and human-readable history, and the due date doubles as the scheduler. No second system, no sync problem, no hidden state.

## Consequences

The touch counter depends on comment format discipline, a malformed ledger line breaks the count for that card. Comment and due date must be written in the same pass or the two halves of the state drift. Parsing comments costs a fetch per card, accepted at the daily cap. Any human can read a card and know exactly what the agent has done and will do next.

## Revisit if

Card volume outgrows per-card comment fetches, or a second agent needs concurrent write access to the same ledger.
