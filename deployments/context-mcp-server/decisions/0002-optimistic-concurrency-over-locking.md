# 0002 Optimistic concurrency over locking

**Status** Accepted

## Context

Two agent sessions ran concurrently and wrote the same file, the second write silently destroyed the first. Last-write-wins is the GitHub Contents API default, so the server had to add its own protection.

## Options

1. Pessimistic locking, a session takes a lock on a file before writing.
2. Optimistic concurrency, reads report the file's current hash, writes pass it back as a baseline and are refused if the live hash has moved.
3. Merge on conflict, attempt automatic three-way merges of concurrent edits.

## Decision

Option 2, plus an always-on shrink guard as a second net. Locks need lifecycle management that stateless one-shot tool calls cannot carry, an abandoned lock from a dead session blocks everyone. Automatic merging of prose written by two language models is a way to manufacture nonsense with confidence. Optimistic refusal is honest, the refused session re-reads, merges with its own judgement and retries. The shrink guard covers callers that pass no baseline at all, any write cutting a file by more than the threshold is refused unless forced.

## Consequences

The baseline is opt-in per call, so discipline lives partly in the calling agents' instructions. A refused write costs a re-read and a retry, which is the correct cost. The shrink guard occasionally refuses a legitimate large cut, which is what the force flag is for, and forced writes are the audit trail of every deliberate override. All five behaviours were verified live before the version shipped.

## Revisit if

Concurrent sessions become the norm rather than the exception, or refusal rates show the opt-in baseline is not being passed.
