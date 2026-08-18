# 0002 Trailing-window dedup before a vector store

**Status** Accepted

## Context

The pipeline must never publish the same story twice. Candidate items arrive from overlapping feeds and syndicated repeats can resurface days later. Deduplication compares the embedding of each candidate against embeddings of recently published output.

## Options

1. Compare against a trailing window of recent rows in the existing log sheet.
2. Stand up a dedicated vector store with persistent history.
3. Exact-match dedup on source URL only.

## Decision

Option 1 for now, with option 2 scoped as the successor. The trailing window rides on the log sheet that already exists, costs nothing to operate and catches the dominant failure case, which is near-term repeats. Option 3 was rejected as the sole mechanism because syndicated content changes URL while keeping substance.

## Consequences

A repeat arriving after the window has cycled escapes the gate. The spreadsheet backend degrades as rows accumulate, so the window stays small. The window mechanism also depends on the loop processing items one at a time, which constrains batch sizing. The vector store migration removes all three limits when volume justifies it.

## Revisit if

A delayed repeat actually publishes, row volume degrades sheet performance, or batch size needs to rise above one.
