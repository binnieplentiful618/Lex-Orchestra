# ADR-014 — Infrastructure Scout: Signal Categories

> Compliance findings are derived from six traceable infrastructure signal categories.

**Status:** Accepted
**Date:** 2026-03-22

## Context

Without a defined signal catalogue, the Infrastructure Scout is a black box.
When a user receives a compliance finding, they should be able to trace
exactly which signal triggered it.

## Decision

The Scout detects compliance-relevant signals in six categories:

**1 — Sub-processor detection**
External services in dependencies, docker-compose, env files, source imports.
Each detection maps to a canonical `Service` node → triggers DPA requirement.

**2 — Security posture**
Cleartext secrets, Docker misconfigurations, overly permissive access policies.
Each finding maps to ISO 27001 controls → triggers ToM findings.

**3 — Deployment signals (EU AI Act)**
Code patterns indicating how AI is used. Maps to `UseCase` nodes (ADR-010)
which carry the correct EU AI Act deployer risk classification.

**4 — Website scan**
Live URL analysis. Detects third-party scripts, trackers, payment widgets
embedded in the live site. Primary data source for the Delta Engine.

**5 — AI threat signals**
Patterns indicating AI-specific security risks: unprotected LLM input
handling, insecure model loading, unvalidated training data. Maps to
AI security controls.

**6 — Platform characteristics**
Determines what type of product is being built. Triggers sector-specific
laws (DSA for intermediary services, DORA for financial entities) that
apply regardless of which specific services are used.

All detections written with `verified: false`. The specific detection
patterns and control mappings are maintained in the Legal Brain.

## Consequences

**Positive:**
- Every compliance finding is traceable to a specific signal and category
- Six-category structure makes the Scout extensible independently
- Platform detection (Category 6) unlocks DSA, DORA, sector-specific compliance

**Negative:**
- Deployment signal detection (Category 3) is heuristic — false positives possible
- Category 6 may require one human confirmation per project scan
