# ADR-002 — LangGraph as Orchestration Engine

> All compliance logic runs in a deterministic, stateful LangGraph workflow.

**Status:** Accepted
**Date:** March 2026

## Context

Lex-Orchestra requires a stateful, testable workflow engine where each
compliance step (scan, graph enrichment, reasoning, document generation,
notification) can be developed and tested independently.

The UI layer must be fully decoupled from the business logic — a compromised
or failed UI must not affect the compliance pipeline.

## Decision

LangGraph is the primary workflow engine. All business logic runs as a
LangGraph workflow exposed via the LangGraph Server API.

OpenClaw serves as the UI/interface layer only — it triggers workflows
via HTTP and never directly imports or calls workflow code.

Only LangGraph nodes may mutate graph and document state. This guarantees
traceable, reproducible runs with complete checkpoint history.

## Consequences

**Positive:**
- Deterministic stateful workflow with clear node boundaries
- Each node independently testable in isolation
- Every run is traceable via PostgreSQL checkpointer
- OpenClaw can fail without affecting the compliance pipeline
- LangGraph Server API is the single integration point

**Negative:**
- Adds a network hop (UI → LangGraph Server) compared to direct import
- LangGraph Server must be running for any workflow execution
