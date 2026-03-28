# ADR-017 — Control Versioning: Temporal Collapse Prevention

> Controls are versioned to prevent silent compliance errors across framework updates.

**Status:** Accepted
**Date:** 2026-03-22

## Context

Compliance frameworks release new versions. ISO 27001:2022 completely
restructured Annex A — control A.5.1.1 from the 2013 version does not
exist in 2022. A graph without versioning silently returns wrong control
IDs, producing legally incorrect documents that look correct.

## Decision

All `Control` nodes carry a `version` property. When a standard releases
a new version, new nodes are seeded alongside existing ones — never
overwriting. A `SUPERSEDES` relationship connects the new to the old.

All queries for current controls filter by `valid_until IS NULL`.
Seed validation rejects any Control node written without a `version` property.

**Version registry (selected):**

| Framework | Version | Status |
|---|---|---|
| ISO 27001 | 2013 | superseded 2022-10-31 |
| ISO 27001 | 2022 | current |
| OWASP LLM Top 10 | 2025 | current |
| NIST CSF | 2.0 | current |
| BSI IT-Grundschutz | 2023 | current |

## Consequences

**Positive:**
- Graph can answer "which controls apply today for framework X?"
- Document Architect always uses current version controls
- `SUPERSEDES` provides upgrade path visibility
- Historical documents remain correct for their point in time

**Negative:**
- All queries must filter by `valid_until IS NULL` for current controls
- ISO 27001:2013 controls correctly marked as superseded
