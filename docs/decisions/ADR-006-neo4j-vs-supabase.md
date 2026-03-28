# ADR-006 — Two Data Stores: Neo4j vs. Supabase

> Neo4j stores legal knowledge; Supabase stores project state and outputs.

**Status:** Accepted
**Date:** March 2026

## Context

Lex-Orchestra requires two distinct types of storage: deterministic legal
knowledge (graph-structured, reusable) and runtime state (project-specific,
mutable, PII-bearing). Combining both in one store creates the wrong tool
for at least one of the jobs.

## Decision

**Neo4j = Knowledge** — laws, controls, service mappings, relationships.
Deterministic regulatory logic. Receives anonymised UUIDs only (ADR-001).

**Supabase = State + Output** — scout findings, generated documents,
compliance status, LangGraph checkpoints. Project-specific, lives locally.

**Decision rule:**
- Does the data represent what the law requires? → Neo4j
- Does the data represent what your project does or has generated? → Supabase

## Consequences

**Positive:**
- Neo4j stays tenant-agnostic — no customer data, safe in cloud
- Supabase provides native LangGraph checkpoint support
- Clear mental model: graph = law, Postgres = state

**Negative:**
- Two data stores require consistent coordination
- Every document render requires a graph query + Supabase lookup
