# 0001 Capability model over keyword matching

**Status** Accepted

## Context

The first architecture matched listings on titles and keywords and auto-processed survivors. It produced volume without quality, near-miss listings that a human would kill on the first paragraph, and it missed strong matches whose titles used different vocabulary. The system was re-founded around the question a headhunter actually asks, can this principal defend this mandate in the room.

## Options

1. Keep keyword matching, tune the term lists harder.
2. Build a capability model, an interview-corrected inventory of what the principal has actually done, with a direction axis distinguishing bought, sold, supported and owned, and judge listings against it.

## Decision

Option 2. The decisive case was a high-scoring listing that passed every text gate and died on one probe question, the experience it demanded existed on the principal's record but on the wrong side of the table. No keyword system can see direction. The model was built bottom-up from the evidence library and corrected row by row in a structured interview, including retiring claims that did not survive it.

## Consequences

Judging costs a model call per record instead of a string match, priced in by killing cheaply at the headline first. The model is a maintained artifact with version discipline and a single human writer. Retrieval vocabulary now follows the model rather than intuition, and the owner rules which defensible capabilities are actually hunted. The interview that built the model also hardened the evidence library itself.

## Revisit if

The capability inventory changes materially, or judging cost per record stops being justified by card quality.
