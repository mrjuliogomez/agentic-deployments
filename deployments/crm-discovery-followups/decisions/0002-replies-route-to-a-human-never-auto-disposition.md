# 0002 Replies route to a human, never auto-disposition

**Status** Accepted

## Context

When a lead replies, something must happen to the card. The model can read the reply and classify intent, rejection, interest or redirect, and could in principle close rejected leads automatically.

## Options

1. Auto-disposition on classified intent, rejections closed by the bot.
2. Every reply moves the card to the engaged stage for human triage, the bot only detects and moves.
3. Option 2 plus an intent pre-label to speed the human read.

## Decision

Option 2, with option 3 accepted as a later addition. A false-positive rejection read kills a warm lead invisibly, and the base rate of nuanced replies, polite deferrals, redirects to a colleague, conditional interest, is too high to trust a classifier with a kill decision. Detection and movement are mechanical and safe, disposition is judgement and stays human.

## Consequences

The engaged stage accumulates cards that a human must read, the cost of the safety. The bot never writes a terminal state from a reply, so no won or lost statistics can be attributed to it. The intent pre-label, when added, speeds triage without deciding it.

## Revisit if

Intent classification proves near-perfect on a long labelled sample, and even then only for the unambiguous rejection class.
