# ADR-013 — Preference Notes: Self-Learning Document Personalization

> User preferences are captured at clause level and applied to future documents.

**Status:** Draft
**Date:** 2026-03-22

## Context

Every organisation has its own legal style — preferred liability limitations,
jurisdiction choices, custom clause formulations. Currently, when a user
edits a generated document, that knowledge is lost. The next scan produces
the same generic output.

## Decision

A Preference Notes system captures user preferences at the clause level
and applies them automatically in future scans.

The primary author identifier is the **Telegram `user_id`** — the permanent
numeric ID, not the username (which can change).

Preferences operate at the clause level, not the full document level.
Examples:
- `liability_limitation` — "Cap at 500 EUR per incident"
- `deletion_period_default` — "90 days instead of standard 30"
- `jurisdiction` — preferred court of jurisdiction

Preferences are stored in local PostgreSQL — they never leave the local
network (ADR-001).

**Boundary:** Preferences cannot remove legally required clauses, set
deletion periods below legal minimums, or suppress required AI Act
transparency notices. They apply only within the legally required skeleton.

## Consequences

**Positive:**
- Compounding value: every correction makes the system better for that user
- Fully sovereign: preferences stored locally, never in the cloud

**Negative:**
- MVP requires explicit Telegram commands — not automatic
- Conflicting preferences across multiple team members require resolution
