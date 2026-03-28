# ADR-007 — Legal Document Delivery

> Compliance documents are delivered automatically after every scan.

**Status:** Accepted
**Date:** March 2026

## Context

After a compliance scan, Lex-Orchestra generates legal draft documents
(DPA, ToM, SCC, AI Act Manifest). These need to reach the user immediately
and be preserved across scans.

## Decision

Documents are delivered automatically after every scan — no pre-delivery
approval step required.

**Two simultaneous delivery channels:**
1. Git commit + push into the scanned repo under `legal/scan/`
2. Telegram file attachments — each document sent directly in the chat

On every new scan, existing documents in `legal/scan/` are moved to
`legal/archive/` before new documents are written. Nothing is ever deleted.

```
user-repo/
  legal/
    scan/       ← latest scan only
    archive/    ← all previous scans, never deleted
```

## Rationale

Users cannot meaningfully approve documents they have not yet seen —
a pre-delivery approval step is a UX anti-pattern. Documents carry a
"Draft — legal review required" disclaimer. The user retains full
control because documents land in their own repository.

## Consequences

**Positive:**
- Zero friction: scan produces immediate, downloadable output
- Full history: `legal/archive/` preserves every previous scan
- Git diff between runs shows exactly what changed between scans

**Negative:**
- Documents delivered before user review — disclaimer is mandatory
- Requires write access to the scanned repository
