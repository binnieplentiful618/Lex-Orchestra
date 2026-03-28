# ADR-018 — Document Validator: Completeness Check Node

> Generated documents are validated against graph-defined completeness rules before delivery.

**Status:** Accepted
**Date:** 2026-03-22

## Context

The Document Architect generates compliance documents but has no quality
feedback loop. Documents can be generated with missing required sections
or empty mandatory fields — users only discover this when they open the file.

The graph already carries `required_sections` and `required_project_config_fields`
on every `DocumentType` node. This is the machine-readable specification.
A validator can check against it deterministically — no LLM needed.

## Decision

A **Document Validator** runs as Node 4b between Document Architect (Node 4)
and Notify (Node 5).

Node 4b checks each generated document against the graph specification:
1. Are all `required_sections` present?
2. Are all `required_project_config_fields` non-empty and not placeholder text?

Returns a `completeness_score` (0.0–1.0) and a list of gaps per document.

**Phase 1:** Linear routing — validator always passes to Notify, which
includes the gaps summary in the Telegram message.

**Phase 2:** Conditional routing — low scores trigger a config request
with a timeout before documents are released as-is with a gaps appendix.

**Output tone:** Operational, not cautionary.
"🔶 DPA 73% complete — 2 fields missing" — not "⚠️ seek legal advice."

## Consequences

**Positive:**
- Users get actionable feedback instead of discovering gaps manually
- Graph specification is machine-checked on every run
- Phase 1 adds ~100ms — no LLM call required

**Negative:**
- Phase 2 requires timeout handling for async complexity
- Validator checks completeness, not legal correctness — that remains human
