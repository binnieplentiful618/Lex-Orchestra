# ADR-003 — MERGE over CREATE: Idempotent Graph

> The graph is idempotent — all writes use MERGE, never CREATE.

**Status:** Accepted  
**Date:** 2026

## Context

The knowledge graph is seeded from official regulatory sources and updated
by the Legal News Scanner and Infrastructure Scout. These operations run
repeatedly — after every import, on every rescan, on schedule.

Without a clear write strategy, repeated runs produce duplicate nodes,
inconsistent state, and silent data corruption.

## Decision

All Cypher write operations use `MERGE` instead of `CREATE` — always.

`MERGE` finds a matching node or relationship if it exists, and creates it
only if it does not. Combined with `ON CREATE SET` and `ON MATCH SET`,
this makes every write operation idempotent: safe to run any number of times
with the same result.

```cypher
-- Idempotent seed pattern
MERGE (c:Control {framework: "ISO_27001", id: "A.8.1"})
ON CREATE SET c.title = "Inventory of Assets", c.confidence = 1.0
ON MATCH SET  c.last_verified = date("2026-03-23")
```

`CREATE` is never used in seed files, migration scripts, or agent writes.

## Consequences

**Positive:**
- Seed runs are safe to repeat — no duplicate nodes
- News Scout and Scanner writes are idempotent
- Recovery from partial failures is straightforward — just re-run
- Graph integrity is maintained without manual cleanup

**Negative:**
- MERGE requires careful key selection — wrong keys create phantom duplicates
- ON MATCH SET logic must be explicit — unintended property overwrites are possible
