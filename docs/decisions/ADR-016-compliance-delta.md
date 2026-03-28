# ADR-016 — Compliance Delta: Commit-Level Change Detection

> Compliance impact is tracked per commit via lightweight delta scans.

**Status:** Draft
**Date:** 2026-03-22

## Context

A full compliance scan (ADR-007) regenerates all documents and is appropriate
for significant infrastructure changes or quarterly reviews. A developer
pushing ten times a day needs a lighter answer:

*"What legal obligations did THIS commit change?"*

## Decision

Introduce `action="delta"` as a lightweight scan mode alongside `action="scan"`.

A compliance delta:
- Runs the Scout on changed files only (not the full repo)
- Compares detected signals against the last full scan state
- Generates `compliance-delta.md` in `legal/delta/` (one file per commit)
- Sends a compact Telegram notification (no file attachments)
- Does NOT regenerate the full document suite or move files to `legal/archive/`

Delta scans supplement full scans — they do not replace them. When a delta
finds a new sub-processor or UseCase risk level change, it recommends a
full scan for document regeneration.

```
legal/
  scan/      ← full scan outputs (ADR-007)
  archive/   ← previous full scans (ADR-007)
  delta/     ← commit-level reports — never deleted
```

The CI/CD trigger fires via webhook to the LangGraph Server API.

## Consequences

**Positive:**
- Lightweight: Scout runs on changed files only
- Per-commit audit trail: when was each obligation introduced?
- CI/CD integration without full document regeneration on every push

**Negative:**
- Requires Pi reachability from CI environment for webhook
- Delta state requires a `scan_state` table in local PostgreSQL
