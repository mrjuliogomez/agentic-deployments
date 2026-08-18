# 0003 Audit field redesign over log migration

**Status** Accepted

## Context

Logging nodes began rejecting writes with a generic bad-request error. Isolation testing surfaced the real cause, the logging backend enforces a hard character limit per cell and the raw research payload exceeded it. The standing rule from decision 0001 pointed to migrating the log nodes to raw HTTP calls.

## Options

1. Migrate the log nodes to HTTP calls against the backend REST API.
2. Keep the native log nodes and redesign the audit field to store a compact writing brief instead of the raw research payload.

## Decision

Option 2. Once the root cause was understood the native node was no longer hiding anything, and the compact brief is a better audit record than a raw payload dump. The brief holds what the write stage actually consumed, which is what an audit needs to reconstruct a post.

## Consequences

The raw research payload is not retained anywhere. If a post needs forensic reconstruction beyond the brief, that data is gone. The native log nodes remain an exception to the standing HTTP rule and are documented as such. The character ceiling is now a known constraint enforced by the brief's size discipline rather than by the backend's rejection.

## Revisit if

Forensic need for full research payloads emerges, or the log migrates to the vector store backend anyway under decision 0002.
