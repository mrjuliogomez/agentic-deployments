# 0001 Drafts behind a human gate

**Status** Accepted

## Context

The bot writes outbound email to real prospects under a real name. A wrong register, a stale fact or a mistimed touch costs relationship capital that a small market does not give back. The workflow is technically capable of sending autonomously.

## Options

1. Autonomous send with guardrails, banned-copy checks and language rules enforced in the pipeline.
2. Every outbound lands in the drafts folder, a human reviews and sends.

## Decision

Option 2. Guardrails catch the failures already imagined, the drafts folder catches the ones not yet imagined. The marginal cost of the gate is one batch review a day, the marginal cost of one bad autonomous send to a warm lead is unbounded. The gate also generates a correction loop, every human edit to a draft is signal for tightening the writing rules.

## Consequences

Send latency depends on the human, a draft can sit unsent. Daily throughput is bounded by review capacity, which the ten-card cap already respects. The pipeline stays one flip away from autonomous send for any lane that later earns it.

## Revisit if

Draft edit rates fall to near zero over a sustained period, or a lane emerges where timing beats review, such as time-zoned sends.
