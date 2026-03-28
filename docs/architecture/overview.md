# Architecture Overview

## The SKA Principle: Sense → Know → Act

Every scan follows a simple pipeline: detect infrastructure, map it to law, generate output.

```
[ SENSE ]               [ KNOW ]                    [ ACT ]
Git repo        →    Neo4j Graph (Cloud)    →    DPA / ToM / AI Act Manifest
Live URL        →    (deterministic         →    Delivered to your team
News / RSS      →     logic engine)         →    Compliance task
User / UI       →
                       ↕
               [ORCHESTRATION — LOCAL]
               LangGraph Server (local)
               Supabase state (Docker)
```

---

## Two Data Stores — Clear Roles

![Neo4j vs Supabase — two brains](../images/architecture-two-brains.svg)

| Store | Role | Contains |
|---|---|---|
| **Neo4j Aura** (Cloud) | Knowledge | Laws, controls, service mappings — anonymised UUIDs only |
| **Supabase** (Local Docker) | State + output | Scout findings, documents, UUID↔asset mapping |

**Rule of thumb:** Neo4j knows what the law requires. Supabase knows what your project does.

---

## Scout analysis pipeline — 3 layers

The Scout runs three analysis layers locally before anything leaves the
network. No PII, no source code, no secrets ever reach an external API.

![Scout analysis pipeline — 3 layers](../images/architecture-scout-layers.svg)

| Layer | Tool | What it detects |
|---|---|---|
| 1 — Regex | Pure Python | Service names, secrets, Docker misconfigs, LLM API calls, system-prompt presence |
| 2 — Presidio | microsoft/presidio | Names, emails, IBANs, phone numbers → `<PERSON>`, `<EMAIL>` etc. System-prompt content is never forwarded. |
| 3 — Phi-4-mini | Ollama (local) | System-prompt role, decision logic (approveLoan → High Risk), GDPR Art. 9 categories, autonomy degree |

Only after all three layers complete does anonymised output leave the local host —
as UUIDs and abstract asset types to Neo4j and the Anthropic API.

---

## From /scan to Download

![Scan flow — from trigger to delivery](../images/architecture-scan-flow.svg)

---

## The 5-Node LangGraph Pipeline

![5-node LangGraph pipeline](../images/architecture-5-nodes.svg)

---

## Container Architecture

```
You (Telegram / Web UI)
     ↓
OpenClaw                      ← sole UI layer, read-only
     ↓
LangGraph Server (local)      ← orchestration, no Telegram token
     ↓
Neo4j (cloud, UUIDs only) + Supabase (local) + legal/drafts/
```

---

## Core Differentiator: Deterministic vs Probabilistic

| Other tools | Lex-Orchestra |
|---|---|
| LLM "guesses" compliance | Context Graph decides deterministically |
| Probabilistic answers | Traceable to graph nodes + edges |
| No auditability | Every decision traceable to official source |
| Code uploaded to cloud | Nothing leaves the local network |
| Static documents | Generated dynamically from real infrastructure |

---

## Further Reading

- [context-graph.md](context-graph.md) — The Context Graph: RAG → GraphRAG → Context Graph (Why this matters)
- [data-sovereignty.md](data-sovereignty.md) — What stays where (ADR-001)
- [../decisions/](../decisions/) — Architecture Decision Records
