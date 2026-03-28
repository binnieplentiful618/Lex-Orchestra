# ADR-009 — Seed Layer Architecture

> Regulatory frameworks are loaded as jurisdiction-specific seed layers.

**Status:** Accepted  
**Date:** 2026

## Context

Regulatory frameworks are not universal. GDPR applies in the EU.
CCPA applies in California. HIPAA applies to healthcare in the US.
A compliance agent that treats all regulations as globally applicable
produces incorrect results for most users.

## Decision

The knowledge graph is structured as independent seed layers loaded
based on jurisdiction configuration:

```
00_global/         ISO 27001 · OWASP · NIST   → jurisdiction-independent (worldwide)
10_jurisdiction/
  eu/              GDPR · EU AI Act · NIS2 · CRA         → EU market law
  de/              DDG · TTDSG · BGB · PAngV · BSI IT-Grundschutz  → Germany (statute + DE authority standard)
  us/              FTC · CCPA                            → US (federal / state as layered)
20_frameworks/     BSI C5 · HIPAA · SOC2 · PCI DSS      → opt-in non-law control sources
```

BSI publications (e.g. IT-Grundschutz, C5, AIC4) are jurisdiction-bound (DE/EU) and must not be treated as global layers.

**Layer semantics:** `00_global` = jurisdiction-independent baselines; `10_jurisdiction` = law and binding national/EU rules; `20_frameworks` = non-law control sources (certifications, catalogues) loaded by context.

Each deployment loads only the layers relevant to its jurisdiction.
The system is global-first — not EU-first.

## Consequences

**Positive:**
- Correct compliance scope per deployment — no false positives from irrelevant jurisdictions
- New jurisdictions added without touching existing layers
- Community contributions can add jurisdiction layers independently

**Negative:**
- Seed configuration must be maintained per deployment
- Cross-jurisdiction queries require explicit layer specification
