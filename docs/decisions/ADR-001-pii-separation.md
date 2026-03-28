# ADR-001 — PII Separation: Cloud Never Receives Asset Context

> Cloud services never receive customer-identifiable infrastructure data.

**Status:** Accepted  
**Date:** March 2026

## Context

Lex-Orchestra processes sensitive infrastructure data (file names, API key
structures, database paths, lines of code) while using cloud services for
compliance logic (Neo4j Aura) and document generation (LLM API).

The core promise is data sovereignty. Sending proprietary code details to
the cloud would be a fundamental breach of trust — and would itself be a
compliance violation for many of our target users.

## Decision

**Neo4j (Cloud) and LLM API receive exclusively:**
- UUIDs (internal references with no content relation)
- Anonymised asset types (`"api_key"`, `"postgres"`, `"s3_bucket"`)
- Boolean properties (`encrypted: true/false`)

**Neo4j and LLM API never receive:**
- File names or paths
- Variable or service names from code
- Code snippets or line numbers
- IP addresses, ports, or credentials
- Generated compliance documents

## Consequences

**Positive:**
- Data sovereignty is technically enforced, not merely promised
- GDPR-compliant by design — the compliance tool is itself compliant
- Neo4j remains tenant-agnostic — no customer data in the cloud graph
- LLM structurally cannot infer anything about customer infrastructure

**Negative:**
- AssetTranslator module required for UUID↔asset mapping
- Debugging with UUIDs is more involved than with real names
