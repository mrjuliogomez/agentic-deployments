# 0001 Git repo as the memory store

**Status** Accepted

## Context

Agent sessions across several surfaces need one shared, persistent memory. The memory must survive any single session, be auditable when an agent writes something wrong, and stay readable by its human owner without special tooling.

## Options

1. A hosted database with an API layer.
2. Cloud documents, a drive folder of notes.
3. A private git repository of markdown files, served to agents through a thin MCP server.

## Decision

Option 3. Markdown is the native read and write format of the agents themselves. Git turns every memory write into a commit, which means history, blame and rollback come free, and a bad agent write is a revert rather than a loss. The owner can read the entire memory in any git client with no server running. A database hides its own past, and drive documents version poorly and diff worse.

## Consequences

The store is text only, binaries stay out. Write throughput is one commit per change, irrelevant at agent volume. Concurrency is not solved by the store and had to be solved above it, see decision 0002. The repo's folder convention became a first-class artifact that agents are instructed to obey, structure discipline replaced schema enforcement.

## Revisit if

Memory outgrows what sessions can usefully read, forcing indexed retrieval, or binary assets become unavoidable.
