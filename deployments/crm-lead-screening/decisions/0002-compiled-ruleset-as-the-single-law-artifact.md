# 0002 Compiled ruleset as the single law artifact

**Status** Accepted

## Context

Screening rules accumulate from run reviews, one ruling at a time. Early on they lived in prompts, chat history and scattered notes, and successive runs applied different law depending on what each session happened to read. Two runs applied opposite verdicts to the same class of listing.

## Options

1. Keep rules in the agent prompts and update prompts as rulings land.
2. Source rule files compiled into one generated artifact with a version and a hash, agents obey the artifact and only the artifact.

## Decision

Option 2. Prompts are execution sequence, not law. The artifact is generated from source files by a compilation map, never hand-edited, and carries a version so every run report can state which law it executed. Two hard corollaries. An agent finding live law missing from the artifact stops and reports rather than executing on chat memory, and any mid-run ruling not compiled in the same pass is flagged unshipped and blocks the next deployment.

## Consequences

Every ruling costs a compile pass before it is enforceable, deliberate friction that prevents silent law. Run-to-run consistency became checkable, the report names the artifact version. Drift between the artifact and any second surface carrying rules must be audited, which surfaced real fossils. A golden set of trap cases regression-tests recompiles.

## Revisit if

The artifact grows past what a judging agent can hold in context, which would force sectioned law with an index.
