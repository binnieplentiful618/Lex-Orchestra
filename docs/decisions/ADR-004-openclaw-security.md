# ADR-004 — OpenClaw Security: Read-Only, No Credentials

> The UI is read-only — no database or LLM credentials — and can only trigger workflows.

**Status:** Accepted
**Date:** March 2026

## Context

OpenClaw is the web UI and Telegram interface — the most exposed component
in the stack. A compromised OpenClaw must not be able to damage system state
or expose credentials.

## Decision

OpenClaw is read-only and trigger-only. It holds no database credentials.

| Action | OpenClaw | LangGraph | Permitted? |
|---|---|---|---|
| Neo4j read | ✅ | ✅ | Yes |
| Neo4j write | ❌ | ✅ | LangGraph only |
| PostgreSQL read | ✅ | ✅ | Yes |
| PostgreSQL write | ❌ | ✅ | LangGraph only |
| Trigger scan | ✅ via API | — | Yes, delegated |
| Auto-approve document | ❌ | ❌ | Never |

OpenClaw holds only one credential: the LangGraph Server URL.
No Neo4j credentials, no database passwords, no LLM API keys.

OpenClaw is the sole Telegram interface. The LangGraph Server runs
API-only — no Telegram token, no bot polling.

## Consequences

**Positive:**
- Compromised UI cannot write to graph or database
- Minimal credential surface — only LangGraph Server URL exposed
- Clear security boundary: UI triggers, LangGraph executes

**Negative:**
- All state changes require a round-trip through the LangGraph API
