# 0003 Feeders never judge, one shared judge

**Status** Accepted

## Context

Multiple discovery agents cover different source families, portals, structured feeds, web sweeps and the email channel. Each could in principle evaluate what it finds and card its own survivors, which is how the first generation worked.

## Options

1. Each discovery agent judges its own finds end to end.
2. Discovery agents only retrieve and kill at the headline, all survivors land in one shared pool, and a single judge evaluates once per record.

## Decision

Option 2. Judgement embedded in feeders duplicates the law into every agent and lets verdicts drift between channels. The split also matches cost reality, headline kills are nearly free and belong at the edge, body-level judgement is expensive and belongs where it runs exactly once. A run confirmed the corollary three times in one sitting, lane and scope typing belongs at the body, never at retrieval, the same headline vertical produced opposite verdicts depending on facts only the body carried.

## Consequences

The pool is the interface, feeders and judge agree on a record contract and nothing else. Adding a source family is a thin adapter, not a new brain. The judge is a single point of consistency and a single point of failure, mitigated by the compiled ruleset and the golden set. Dedup must be solved at the pool, cross-source keys, because feeders no longer see each other.

## Revisit if

Judge throughput becomes the bottleneck, which would argue for sharding the judge while keeping the single law artefact.
