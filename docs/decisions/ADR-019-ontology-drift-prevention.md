# ADR-019 — Ontology Drift Prevention

> The graph continuously detects and signals ontology drift before it affects outputs.

**Status:** Accepted  
**Date:** 2026-03-23

## Context

A knowledge graph's ontology can silently diverge from the domain it represents.
Queries continue to execute. No error is raised. Results degrade invisibly.

This phenomenon — ontology drift — manifests in three forms:

- **Entity type drift**: new domain concepts that existing node labels cannot represent
- **Relationship drift**: relationships whose semantics have changed
- **Constraint drift**: schema assumptions that no longer hold

For Lex-Orchestra, regulatory change is the primary driver. EU AI Act annexes
expand. Standards release new versions. National law replaces prior law (TMG → DDG).
Neo4j itself raises no alerts for semantic inconsistency.

## Decision

We treat the knowledge graph ontology as a **living system**, not a project deliverable.

Four mechanisms govern ontology lifecycle management:

**1. Continuous health monitoring**  
Graph health checks run automatically after every seed and import operation.
A failing check blocks the commit — same protocol as a failing test.

**2. Explicit drift signaling via the graph itself**  
When a regulatory change is detected, the graph signals drift through its own
structure. No document generation runs on stale knowledge without human review.

**3. Schema versioning — new version alongside, not overwriting**  
When a standard releases a new version, new nodes are seeded alongside existing
ones. A `SUPERSEDES` relationship connects new to old, preserving audit trail.

**4. Change history — two complementary layers**  
Git tracks deliberate schema changes via seed file diffs.
`SchemaChange` nodes track runtime changes (News Scout, scan-triggered writes)
directly in the graph — queryable without external tooling.

## Consequences

**Positive:**
- Drift detected before it affects document output
- Graph is self-describing about its own gaps and staleness
- Change history queryable at both git and graph level
- No generated document silently uses outdated regulatory knowledge

**Negative:**
- Health checks add a mandatory step to the import workflow
- Confidence decay requires human review before documents can be regenerated
- Version tracking increases node count over time
