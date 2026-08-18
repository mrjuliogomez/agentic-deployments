# 0002 Per-channel write calls over a shared write

**Status** Accepted

## Context

Each essay needs three versions, a long blog body, a mid-length LinkedIn post and a shorter X post, all in the same voice but with different caps, furniture and formatting rules. The first build asked one model call to produce all three in a single structured response.

## Options

1. One model call returns all three versions in a marked-up block, parsed downstream.
2. Three model calls, one per channel, each with the channel contract hardcoded.

## Decision

Option 2. One call could not hold three distinct output contracts at once, the versions bled into each other, caps were missed and the parse was fragile. Per-channel calls give each version its own full instruction budget and make the output contract per call trivial.

## Consequences

Three model calls per run instead of one, a cost accepted at weekly cadence. Channel rules live in three places and must be edited in step, the shared voice core is duplicated by design. Parsing simplifies to one body per call with no marker splitting.

## Revisit if

Model instruction-following improves enough that one call reliably holds three contracts, or run cost starts to matter at a higher cadence.
