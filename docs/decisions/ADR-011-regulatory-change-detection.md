# ADR-011 — Regulatory Change Detection

> Regulatory changes are detected automatically and reduce confidence until verified.

**Status:** Accepted
**Date:** 2026-03-21

## Context

Regulatory knowledge is not static. Laws are amended. Standards are versioned.
National implementations differ from EU directives. Any compliance system that
cannot detect regulatory changes will generate outdated documents without warning.

## Decision

A three-part regulatory change detection system:

**1. Legal News Scanner**
RSS/feed monitoring from official sources (EUR-Lex, BfDI, BSI) and
court ruling databases. Writes `NewsEvent` nodes with `verified: false`.

**2. Confidence Decay**
When a `NewsEvent -[:AMENDS]→ Law` relationship is created, the target
Law node's confidence drops from `1.0` to `0.7`. This signals that
re-verification is needed without deleting any existing knowledge.

**3. Staleness Notification**
When a Law node's confidence drops, affected generated documents are
flagged and the user is notified via Telegram. Document generation
on stale knowledge is blocked until the user runs `/verify`.

**Verification restores confidence:**
The user reviews the source and confirms via `/verify`, returning
the Law node's confidence to `1.0` and clearing the staleness flag.

**Principle:** Never delete, always version. Detection is automated;
verification is always human.

## Consequences

**Positive:**
- Users notified when compliance documents may be outdated
- Graph self-describes its own reliability through confidence scores
- Every legal change traceable: NewsEvent → Law → affected Documents

**Negative:**
- RSS feeds require maintenance as sources change formats
- False positives possible — not every publication affects the user's stack
