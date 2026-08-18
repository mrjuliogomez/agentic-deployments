# 0001 Series chain over parallel convergence

**Status** Accepted

## Context

The pipeline needs three channel versions of each essay and one image shared across them. The natural topology is three parallel write branches converging on a merge. In production the convergence node could not read the sibling branches by name, the platform resolves node references only along the single branch it walked, and every merge-based attempt failed with reference errors or silently carried the wrong items.

## Options

1. Keep parallel branches and merge nodes, work around the reference failures.
2. Chain the three write stages in series so every node sits on one walked branch, and fan the shared image binary across the items with a dedicated code node.

## Decision

Option 2. Three failed variants of option 1 proved the platform law rather than a fixable bug. The series chain makes every node-name reference legal, and a fan node attaches the image binary to all three channel items after the content converges.

## Consequences

The pipeline is longer end to end because nothing runs concurrently, which is irrelevant at one run per week. The topology is easier to reason about, one line, no merge semantics. Every future stage must join the chain rather than branch off it, and the fan node is a standing pattern for any shared binary.

## Revisit if

The platform gains reliable cross-branch references, or run duration starts to matter.
