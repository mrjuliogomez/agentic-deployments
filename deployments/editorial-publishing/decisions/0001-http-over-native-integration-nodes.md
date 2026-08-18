# 0001 HTTP requests over native integration nodes

**Status** Accepted

## Context

Three production failures on three different channels traced to the platform's native integration nodes. In each case the node failed or masked the real provider error behind a generic message, costing weeks of blocked posting and hours of debugging. The worst case burned six wrong hypotheses before an isolation test surfaced the actual provider error in one step.

## Options

1. Keep native nodes and improve error handling around them.
2. Migrate every integration to a raw HTTP request node against the provider REST API.
3. Migrate only the nodes that have already failed.

## Decision

Option 2 as the standing rule. Any new integration starts as an HTTP node. Any remaining native node is a migration target. One pragmatic exception was later granted for the logging backend after its root cause was fully understood, see decision 0003.

## Consequences

HTTP nodes surface the real provider error on first failure, which alone justified the migration. The cost is hand-building request payloads and authentication per provider, and losing the convenience features of native nodes. Provider-specific quirks such as escaping rules and versioned APIs become the pipeline's responsibility.

## Revisit if

The platform starts exposing raw provider errors through its native nodes, or a provider deprecates the REST surface the HTTP nodes depend on.
