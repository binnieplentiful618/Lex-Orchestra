# ADR-008 — Token Management: Local Web-UI Only

> Credentials are entered locally and never transmitted via external channels.

**Status:** Accepted
**Date:** March 2026

## Context

Scanning private repositories requires access tokens. Sending credentials
via Telegram is unacceptable — messages transit external servers and cannot
be recalled.

## Decision

Credentials are entered once via the **local web UI** (reachable only
within the local network), stored in local PostgreSQL, and resolved
automatically for subsequent scans.

**Two strict zones — never mixed:**

```
External (Telegram)              Local only (web UI)
────────────────────             ──────────────────────────────
/scan, /status, /verify          GitHub tokens, configuration
NO credentials ever              Everything sensitive
```

Port 18789 is exposed only within the local network — never to the internet.

## Consequences

**Positive:**
- Telegram stays clean — no credentials ever transmitted externally
- Tokens entered once, reused automatically per GitHub owner/org
- Compliant with ADR-001: credentials never leave local network

**Negative:**
- Requires local network access to configure credentials
- Local web UI must be maintained as a configuration surface
