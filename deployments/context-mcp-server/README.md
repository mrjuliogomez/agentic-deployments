# Context MCP Server

**Status** Live  ·  **Runtime** Railway, Node.js  ·  **Trigger** On demand, tool calls from agent sessions

## What it does

A Model Context Protocol (MCP) server that gives every agent session shared, persistent memory over a private git-backed context portfolio, plus routed write access to this public repo. Four tools, list the portfolio, read a file, create or update a file, delete a file. Reads return the file's current blob hash alongside its content. Writes are guarded, a stale-baseline write is refused, a write that would shrink a file past a threshold is refused, and a force flag exists for deliberate overrides. Paths carrying a routed prefix target a second repository through the same guards.

## Why it exists

Agents on different surfaces, chat, desktop, scheduled tasks, all need the same memory, and that memory needs history. A database gives shared state but hides its own past. A git repo gives both, every write is a commit, every mistake is recoverable, and the human can read the whole memory in any git client. The guards exist because two concurrent sessions once overwrote each other silently, and a memory that can lose a day of context without telling anyone is worse than no memory.

## Architecture

```
Agent session (any surface)
   |
MCP over streamable HTTP
   |
Node.js server (Express + MCP SDK)
   |  stateless, one server instance per request
   |
path router ──> private context repo (default)
           └──> public portfolio repo (routed prefix)
   |
GitHub Contents API (token in server env, never in any session)
```

## Harness

The loop is stateless request and response, one tool call at a time, a fresh server instance per request so no session leaks into another.

Context assembly is the point of the server rather than a step in it, sessions read the portfolio at start and write back at close per the repo's own conventions.

Tool bindings are the GitHub REST API with the token held as a server environment variable. No credential ever crosses a chat surface, sessions authenticate to the server, the server authenticates to GitHub.

Stop conditions are the write guards. A write carrying a stale baseline hash is refused with the current hash named. A write shrinking an existing file past the threshold is refused. Both refusals return instructions, re-read, merge, retry.

Human gates. The force flag is the only override and its use is deliberate by construction. Content routed to the public repository is published by agents only on explicit instruction.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Protocol | MCP over streamable HTTP | Works from every agent surface with one connector |
| Server | Node.js, Express, MCP SDK | Small enough to audit in one sitting |
| Hosting | Railway | Auto-deploys from the repo it serves, no infrastructure to run |
| Store | GitHub repos via Contents API | History, rollback and human readability for free |
| Auth | Fine-grained token in server env | Scoped to named repos, never touches a session |

## Failure modes and handling

Concurrent sessions clobbering each other. The founding incident, two sessions wrote the same file and the second silently destroyed the first's work. Handled with optimistic concurrency, reads report a baseline hash, writes carrying a stale one are refused, recorded in decisions 0002.

Catastrophic shrink writes. A session that read a stale or partial copy can overwrite a long file with a fragment. Handled by refusing any write that shrinks a file past a threshold unless forced, which protects even callers that pass no baseline.

Deploy lag serving stale reads. The server's local checkout lags a fresh commit, so content and hash could disagree. Handled by reading from the GitHub API as the source of truth so content and hash arrive as a matched pair, local disk is only a fallback.

Provider errors masked by wrappers. Handled by passing the raw GitHub error status and body through to the caller, the same doctrine the [editorial-publishing](../editorial-publishing/README.md) pipeline arrived at independently.

## Decisions

| # | Title |
|---|---|
| [0001](decisions/0001-git-repo-as-the-memory-store.md) | Git repo as the memory store |
| [0002](decisions/0002-optimistic-concurrency-over-locking.md) | Optimistic concurrency over locking |

## Impact

Context lived per conversation before this, every session started by re-explaining the business and anything learned in one session was invisible to the next. Every agent in the fleet now reads and writes the same portfolio through this server, all five guard behaviours are verified live, baseline-hash read, stale refusal, shrink refusal, correct-baseline success and force override, and the clobber class of data loss has not recurred since the guard shipped. As an estimate, ten to twenty minutes of context re-establishment are saved per session across every session run since launch, assuming the alternative was manual re-briefing, which is what actually happened before it existed. The token cost of portfolio reads against the cost of lost context, and the read latency added versus a local store, are not measured.
