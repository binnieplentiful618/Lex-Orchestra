# Data Sovereignty — What stays where

**No customer-specific data ever leaves your system** — enforced by architecture, not policy.

**This is not a policy — it is architecture.** The LLM structurally has no access to customer-specific data.

> **"Your infrastructure stays your infrastructure."**
> No external service ever sees what runs in your stack,
> which keys you use, or which documents were generated.

## The three zones

```
ZONE 1 — YOUR NETWORK (Pi / VPS)                      [private]
  Supabase (self-hosted)
  ├── Scout findings: "Stripe in package.json"
  ├── Generated documents: ToM, DPA, SCC (Markdown)
  ├── Compliance status per project
  ├── Agent memory: learned preferences
  └── LangGraph checkpoints: workflow state
  → NEVER leaves the network

ZONE 2 — CLOUD BRAIN (Neo4j Aura)             [abstract knowledge]
  Knowledge graph
  ├── Laws: GDPR Art. 28, EU AI Act, NIS2, ISO 27001, BSI
  ├── Services: "Stripe" as a generic service type
  ├── UseCase nodes: deployer risk classification
  ├── DocumentTypes: DPA, SCC, ToM
  └── Relationships: REQUIRES, BASED_ON, IMPLEMENTS, CLASSIFIED_BY
  → Contains NOT ONE customer-specific data point

ZONE 3 — LLM API (Claude / Anthropic)          [language layer]
  Input:  "Service: Stripe (USA). Requires: DPA, SCC. GDPR Art. 28."
  Output: Finished DPA contract text
  → Sees ONLY abstract structural knowledge from Neo4j
  → NEVER sees: customer, key, file, configuration, domain
```

## What the LLM sees — and what it does not

```
❌  LLM does NOT see:
    - That this is my-project.io
    - Which API key is active
    - Where a service is integrated (checkout.js, line 42)
    - IP addresses, ports, database paths
    - Finished compliance documents

✅  LLM sees ONLY:
    - "Service type: payment (USA)"
    - "Requires: DPA, SCC"
    - "Legal basis: GDPR Art. 28, Art. 46"
    - "UseCase indicator: customer_service_chatbot → Limited risk"
```

## The UUID-Only Pattern

The mechanism that enforces this separation is the UUID-Only Pattern.
Every sensitive entity the Scout detects is immediately translated into
an anonymised UUID before anything leaves the local network:

```
Scout finds:           Local PostgreSQL:          Neo4j Cloud:
"STRIPE_SECRET_KEY"  → uuid: abc-123          →  {id: "abc-123",
 in checkout.js:42     name: "stripe_key"         type: "api_key",
                       file: "checkout.js"         encrypted: false}
                       line: 42

Neo4j returns:       → UUID lookup            → Document Architect:
Controls for           PostgreSQL fetches          assembles DPA/ToM
"abc-123"              full context               with real details
```

The LLM reasons about `abc-123`. The document is assembled locally with
real names. The LLM never sees the real names.

**The threat model this defeats:**

- **Infrastructure leak:** A complete graph dump gives an attacker only
  anonymous UUIDs — no file names, no service names, no real structure.
- **Prompt injection:** An attacker cannot extract PII through clever prompts
  because it physically does not exist in the graph's context.
- **The logging trap:** PII never ends up in LLM provider error logs because
  it is never sent. Standard RAG systems silently violate GDPR every time an
  error is logged with real customer context in the payload.

## Data table

| Data category | Location | Leaves network? |
|---|---|---|
| Source code | Local (Pi / VPS) | ❌ Never |
| Secrets / API keys (values) | Not stored | ❌ Only presence detected |
| Tech entities (anonymised) | Neo4j Cloud | ✔ Generic types only |
| Regulatory logic (laws) | Neo4j Cloud | ✔ Public knowledge |
| Project state | Supabase local | ❌ Never |
| Generated documents | Local + Telegram | ❌ On user device |

## GDPR-compliant by design

| GDPR principle | Article | Implementation |
|---|---|---|
| Accountability | Art. 5 Para. 2 | Every decision traceable to a graph node |
| Data minimisation | Art. 5 Para. 1 lit. c | LLM receives only what it needs |
| Purpose limitation | Art. 5 Para. 1 lit. b | Customer data used locally for compliance only |
| Integrity & confidentiality | Art. 5 Para. 1 lit. f | Infrastructure details never leave the network |
| No third-country transfer | Art. 44 ff. | Neo4j: no customer reference. LLM: no customer reference |

## Source verification (graph seeds)

All regulatory content written into the graph must come from a verified source:

- `confidence: 1.0` = read directly from official PDF or EUR-Lex
- `confidence: 0.9` = preliminary, `note_unverified` property set
- `confidence: 0.5` = training knowledge only — **never written to the graph**

```cypher
MATCH (n)
WHERE n.note_unverified IS NOT NULL
RETURN labels(n)[0] AS type, n.confidence, n.note_unverified
```

---

## Further reading

- [context-graph.md](context-graph.md) — The Context Graph: RAG → GraphRAG → Context Graph (Why this matters)
- [ADR-001](../decisions/ADR-001-pii-separation.md) — PII separation, technical spec
- [Context Graph Manifesto](https://trustgraph.ai/news/context-graph-manifesto/) — TrustGraph, Dec 2025

  *Special thanks to [Mark Adams](https://x.com/cybermaggedon) and [Daniel Davis](https://x.com/TrustSpooky) for the manifesto — it helped frame how we describe context, auditability, and situation-aware systems.*

- [Privacy by Architecture: Why Your Knowledge Graph Should Only Store UUIDs](https://medium.com/@thomasrehmer/privacy-by-architecture-why-your-knowledge-graph-should-only-store-uuids-a26fb375c908) — Thomas Rehmer, Feb 2026
