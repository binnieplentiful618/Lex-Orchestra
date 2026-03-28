# ADR-005 — Graph Write Permissions: Three-Level Model

> Graph writes follow a three-level model with explicit verification control.

**Status:** Accepted
**Date:** March 2026

## Context

The knowledge graph must remain stable (compliance logic must not change
unnoticed) while also growing autonomously as new services and regulations
are discovered.

## Decision

Three write tiers with explicit permissions:

| Level | Who | What | When |
|---|---|---|---|
| 1 — Seed | `seed.py` | Static foundation: Laws, Controls, Services | Once / on schema change |
| 2 — News Scout | `news_scout.py` | New regulations, rulings from RSS feeds | Automatic, periodic |
| 3 — LangGraph | `main.py` scanner | New services from repo scans | On-demand, per scan |

Levels 2 and 3 write with `verified: false`. The user reviews and confirms
via Telegram before unverified nodes affect document generation.
Only `verified: true` nodes flow into compliance output.

## Consequences

**Positive:**
- Graph grows autonomously without manual seed adjustments
- Human retains control via the verified flag
- Every node carries a `source` property — full provenance

**Negative:**
- Verification adds a step before new services appear in documents
