# ADR-021 — Retrieval Policies: Configurable Compliance Scope

> Compliance scope is defined via graph-based retrieval policies instead of hardcoded queries.

**Status:** Accepted  
**Date:** 2026-03-23  
**Inspired by:** TrustGraph — Retrieval Policies concept

## Context

The Reasoning Node queries Neo4j for controls relevant to a scan result.
Currently, query parameters (which frameworks, minimum confidence, jurisdictions)
are hardcoded in the workflow. This means:

- Changing compliance scope (e.g. adding HIPAA for a US deployment) requires
  code changes
- Different document types use the same implicit retrieval rules
- There is no record of which retrieval rules produced a given document

## Decision

Retrieval rules are stored as `RetrievalPolicy` nodes in Neo4j.
The Reasoning Node reads the relevant policy before constructing its graph
query. Document types link to policies via a `USES_POLICY` relationship.

Named policies (initial set):
- `tom_controls` — controls for ToM generation (GDPR Art. 32 relevant)
- `dpa_services` — services that require a signed DPA (`dpa_url` on `Service` nodes)
- `ai_act_manifest` — AI Act deployer obligations
- `news_scout` — regulatory change detection
- `deadline_watch` — upcoming law deadlines within N days

## Consequences

**Positive:**
- Compliance scope is configurable without code changes
- Different document types use explicitly defined retrieval rules
- Policy used for a document is stored → full audit trail
- Multi-jurisdiction support without code forks

**Negative:**
- Reasoning Node must be refactored to read policy from graph
- Policies must be seeded as part of the graph setup
- Policy misconfiguration can produce incomplete control sets
