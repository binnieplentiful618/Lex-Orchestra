# ADR-012 — DPA URL Registry: Direct Links Per Sub-Processor

> Every sub-processor includes a direct DPA signing link.

**Status:** Accepted
**Date:** 2026-03-22

## Context

When Lex-Orchestra detects a sub-processor and generates a DPA draft, the
most friction-heavy step is finding where to actually sign the DPA. Processor
DPA pages are often buried, unstable, and require a specific signing flow.

## Decision

Every `Service` node that requires a DPA carries a `dpa_url` property
pointing directly to the processor's DPA signing or download page.

The Document Architect includes this URL in every generated DPA.
The Telegram notification surfaces it per detected sub-processor.

A `dpa_url_tier` property distinguishes quality levels:
- `dpa_direct` — direct DPA signing or download page
- `legal_hub` — legal hub, DPA findable within one click
- `privacy_page` — only privacy policy found, no dedicated DPA

The registry is maintained as part of the Legal Brain and updated
when the Legal News Scanner detects a processor has changed their
legal page structure.

## Consequences

**Positive:**
- Removes the most friction-heavy step after DPA generation
- Strong differentiator: most tools stop at "you need a DPA with X"
- DPA URLs are stable — maintenance cost is low

**Negative:**
- URLs become stale if a processor restructures their legal pages
- Registry requires curation — not suitable for community contribution
