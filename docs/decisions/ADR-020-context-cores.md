# ADR-020 — Context Cores: Versioned Graph State

> Every graph state is versioned as a ContextCore and attached to generated documents.

**Status:** Accepted  
**Date:** 2026-03-23  
**Inspired by:** TrustGraph — Context Core concept

## Context

After every seed run, the graph's knowledge state changes. There is currently
no machine-readable way to answer:
- What version of compliance knowledge is in the graph right now?
- Which frameworks are loaded?
- Was this document generated from current or stale graph knowledge?

This gap is critical when documents are generated from outdated graph state,
or when a compliance finding needs to be attributed to a specific knowledge version.

## Decision

After every successful seed run, a single `ContextCore` node describes the
graph's current loaded state — version, frameworks, seed timestamp, and a
content hash of the configuration.

The Document Architect reads this version before generating any document and
stores it alongside the output. This makes every generated document traceable
to the exact knowledge state that produced it.

Versioning follows semantic versioning (MAJOR.MINOR.PATCH):
- PATCH: properties updated, no schema changes
- MINOR: new framework added, new node label introduced
- MAJOR: existing labels renamed or merged

## Consequences

**Positive:**
- Graph is self-describing about its own version and loaded state
- Every compliance document is traceable to a specific graph version
- CLAUDE.md node counts can be auto-generated rather than maintained manually

**Negative:**
- seed.py must compute a configuration hash after every run
- Document Architect must store core version on every generation
