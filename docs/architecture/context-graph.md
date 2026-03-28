# Lex-Orchestra — Context Graph

Lex-Orchestra builds a **Context Graph** — a system that maps your infrastructure directly to legal requirements.

A Context Graph is not just a knowledge model — it is a situational, temporal system that reflects the current state of a specific project.

> Lex-Orchestra is not a Knowledge Graph.
> It is a **Context Graph** — a living knowledge model that not only stores
> rules but understands what applies to *this* project, *right now*,
> *with what degree of certainty*.

---

![Lex-Orchestra Context Graph — the four layers](../images/architecture-context-graph.svg)

---

## Understanding the Graph: The Subway Network

The best way to understand the compliance graph is not as a spreadsheet or a
list of rules — but as the **network map of a large subway system**.

**Stations** are the individual nodes. They can be:
- Technical components (`postgres`, `stripe`, `aws-s3`)
- Specific paragraphs from a law (`GDPR Art. 28`, `ISO 27001 A.12.3`)
- Use cases (`customer_service_chatbot`, `hr_recruitment_screening`)
- Your project's detected assets (`payment_service uuid-abc123`)

**Lines connecting the stations** are the edges in the graph — they represent
logical and legal dependencies. `Stripe → REQUIRES → DPA`,
`aws-s3 → MAPS_TO → ISO 27001 A.12.3 (Backup)`.

**Running a route** is what happens during a compliance scan:
The Infrastructure Scout identifies a technical entity — say, a Postgres database
running on a local SSD. This entity becomes a new station in the network.
The system then traces the lines: it follows the edges deterministically
from your detected component to the relevant regulatory nodes.

The result is not "this *might* be relevant" (probabilistic LLM guessing),
but a **mathematically precise path**: *because you use component X, you
necessarily trigger requirement Y from ISO A.12.3 on backup concepts —
traceable to an official source, not inferred from statistical patterns.*

This is the difference between a classic legal AI tool — which acts like a
doctor diagnosing by SMS questionnaire, guessing based on statistical patterns —
and Lex-Orchestra, which acts like an MRI scanner: it does not ask about your
architecture, it **measures** it.

```
Traditional LLM tool:   "Your setup might have compliance implications..."
Lex-Orchestra:          "Postgres + personal_data → GDPR Art. 32 + ISO A.12.3
                          DPA required with: Stripe (dpa_url: stripe.com/de/legal/dpa)
                          Confidence: 1.0 — verified 2026-03-21"
```

The law is not a text pasted over the top of your technology.
It becomes a **technical dependency** in your system — like a software library
you import. Deterministic. Traceable. Auditable.

---

## RAG → GraphRAG → Context Graph (Why this matters)

Most legal AI tools stop at step one. Lex-Orchestra is at step three.
Understanding the difference explains exactly *why* determinism matters in compliance.

### Step 1 — Standard RAG (Retrieval-Augmented Generation)

The dominant approach since 2023. Documents are converted into numerical vectors
(embeddings) and stored in a database. A question finds the most "similar" text chunks,
which are passed to an LLM as context.

**Works well for:** Summaries, "what does this law say?", document search.

**Fails at:**
- **Negative queries:** "Which contracts do NOT have audit rights?" — vectors cannot
  represent the *absence* of a relationship. The query returns contracts *mentioning*
  audit rights, the exact opposite of what was asked.
- **AND logic:** "Find contracts with Revenue Sharing AND Non-Compete" — similarity
  search cannot guarantee both conditions exist on the same document.
- **Boolean logic:** `NOT`, `AND`, `ONLY IF` — standard similarity does not understand
  logical operators.
- **Counting and aggregation:** You cannot reliably count across a set of vectors.

The core problem: **vector search finds similarity, but legal compliance needs structure.**
An LLM that is "85% confident" about a legal requirement is not an acceptable audit answer.

```
Standard RAG:   "Your setup might have compliance implications..."
                (probabilistic, no source, no trace, cannot be audited)
```

### Step 2 — GraphRAG

GraphRAG (Knowledge Graph + RAG) adds structure. Documents are not stored as text
chunks but as explicit relationships:

```
Contract → CONTAINS → Clause → OF_TYPE → "Audit Rights"
```

Now the impossible query becomes trivial:
```cypher
MATCH (c:Contract)
WHERE NOT EXISTS {
  MATCH (c)-[:CONTAINS]->(:Clause)-[:OF_TYPE]->(ct:ClauseType)
  WHERE ct.name = 'Audit Rights'
}
RETURN c.name
```
Result: mathematically precise, 100% recall, fully traceable.

**GraphRAG is correct about *what* is in the documents.**
But it is still static — it stores facts, not context.

`Stripe → REQUIRES → DPA` — true for everyone, always, everywhere.

It does not know: *Does this DPA requirement apply to YOUR project? Are you in the EU?
Did you already sign it? Is the regulation still current? What is the confidence level?*

### Step 3 — Context Graph (what Lex-Orchestra builds)

A Context Graph is GraphRAG made *situational and temporal*.

It does not just store that `Stripe requires a DPA`.
It stores that **this specific project**, **scanned on this date**, **in this jurisdiction**,
**with this confidence level**, requires a DPA — and here is the DPA signing URL.

| Dimension | Standard RAG | GraphRAG | Context Graph |
|---|---|---|---|
| Data structure | Flat vectors | Graph relationships | Graph + temporal + project state |
| Query logic | Semantic similarity | Structural patterns | Structural + boolean + temporal |
| Negative queries | ❌ fails | ✅ works | ✅ works |
| Personal to this project | ❌ generic | ❌ generic | ✅ scanner adds project nodes |
| Time-aware | ❌ static | ❌ static | ✅ `applies_from`, `confidence`, `last_verified` |
| Self-describing gaps | ❌ no | ❌ no | ✅ `KnowledgeSource {status: "missing"}` |
| Regulatory change detection | ❌ no | ❌ no | ✅ `NewsEvent -[:AMENDS]→ Law` |
| Auditability | ❌ black box | ⚠️ partial | ✅ every finding fully traceable |

```
GraphRAG:       "Stripe → REQUIRES → DPA"
                (correct, but generic — applies to everyone)

Context Graph:  "Project uuid-abc, scanned 2026-03-22, jurisdiction DE:
                 Stripe detected (confidence: 1.0, verified) → REQUIRES → DPA
                 DPA: https://stripe.com/de/legal/dpa
                 GDPR Art. 28 applies_from: 2018-05-25, confidence: 1.0
                 Previous scan: same finding (stable)"
```

The Context Graph is not just smarter retrieval. It is a living mirror of
one specific project's legal reality at one specific point in time.

> For the broader theoretical framing of Context Graphs and why this term
> is gaining traction in the industry, see the
> [Context Graph Manifesto](https://trustgraph.ai/news/context-graph-manifesto/)
> (TrustGraph, December 2025).

---

## What is the difference between Knowledge Graph and Context Graph?

A **Knowledge Graph** stores facts:
`Stripe → REQUIRES → DPA`

A **Context Graph** knows the context of those facts:
- *Why* does it apply? (Legal basis as a node, not a string)
- *Since when* does it apply? (Temporal properties on every Law node)
- *For whom exactly*? (ProjectAsset nodes from the scanner, UUID-based)
- *How reliable is the knowledge*? (confidence property, verified flag)
- *What is still missing*? (KnowledgeSource nodes with status: "missing")
- *For which jurisdiction*? (jurisdictions property on every Law node)

The core difference: a Knowledge Graph is a handbook.
A Context Graph is a mirror — personalised to the real infrastructure,
the real jurisdiction, the real point in time.

---

## The four layers of the Context Graph

### Layer 1 — Seed Layer Architecture (ADR-009) ✅

```
Service → REQUIRES → DocumentType → BASED_ON → Law
```

Deterministic. Global-first, not EU-first.

The graph is structured as independent seed layers (ADR-009).
EU is one jurisdiction among many — not the universal default:

```
00_global/       ISO 27001, OWASP, NIST    → applies worldwide
10_eu_primary/   GDPR, AI Act, CRA         → applies in the EU
10_de/           DDG, TTDSG, BGB           → applies in Germany
10_us/           FTC, CCPA                 → applies in the US
20_frameworks/   HIPAA, SOC2, PCI DSS      → opt-in, industry-specific
```

Each deployment loads only the layers relevant to its jurisdiction.
Details: [ADR-009](../decisions/ADR-009-seed-layers.md).

---

### Layer 2 — Temporal Graph + Confidence ✅

Every `Law` node has a time dimension — all Law nodes fully populated,
verified against official PDFs or EUR-Lex:

```cypher
(:Law {
  name: "CRA", article: "14",
  valid_from:    date("2024-12-10"),
  applies_from:  date("2026-09-11"),
  last_verified: date("2026-03-21"),
  confidence:    1.0,
  jurisdictions: ["EU"],
  source:        "EUR-Lex OJ:L_202402847"
})
```

What this enables:

```python
def get_upcoming_deadlines(self, days: int = 90) -> list[dict]:
    """Return laws whose applies_from is within N days from today."""
```

Output: `"CRA Art. 14 applies in 174 days — vulnerability reporting obligations"`

The graph knows not just *what* applies — but *when* and *for whom*.

**Confidence scale:**

| Value | Meaning |
|---|---|
| `1.0` | Primary law — read directly from official PDF or EUR-Lex |
| `0.9` | Established secondary source — `note_unverified` set |
| `0.7` | Partially superseded or uncertain (e.g. TMG replaced by DDG) |
| `0.5` | Training knowledge only — **not allowed in any layer file** |

---

### Layer 3 — Context Graph: Scanner + UseCase Nodes (ADR-001, ADR-010)

Two components make the graph specific to *this* project:

**Scanner as input layer (ADR-001):**

Without scanner: generic graph, applies to everyone.
With scanner: project-specific graph, applies to *this* project.

```cypher
MERGE (p:Project {uuid: "uuid5(repo_url)"})
MERGE (a:ProjectAsset {uuid: "asset-uuid", type: "payment_service"})
MERGE (p)-[:HAS_ASSET {scanned_at: datetime()}]->(a)
MERGE (a)-[:MAPS_TO]->(s:Service {name: "Stripe"})
```

Neo4j never receives real file names or code — only UUIDs and
anonymised asset types. The mapping UUID → real service name
lives in Supabase (local).

**UseCase nodes — deployer risk classification (ADR-010):**

Provider risk (OpenAI = GPAI) is not the same as deployer risk.
A user building a chatbot with Claude has `Limited` risk (Art. 50),
not GPAI — that is Anthropic's concern.

```cypher
(:UseCase {
  type: "customer_service_chatbot",
  risk_level: "Limited",
  eu_ai_act_article: "50",
  reason: "Art. 50 — users must be informed they interact with AI"
})

(:UseCase {
  type: "hr_recruitment_screening",
  risk_level: "High",
  annex_iii_nr: "4",
  reason: "Annex III Nr. 4 — employment and personnel management"
})
```

What Layer 3 enables:
- **Rescan = delta**: new service → new edge → new `compliance_task`
- **Project-specific queries**: which obligations apply to *this* project?
- **Correct AI Act output**: deployer obligations, not provider obligations

---

### Layer 4 — Self-Describing Context Graph (ADR-011)

The graph knows what it does not know — and detects when knowledge becomes stale.

```cypher
MERGE (k:KnowledgeSource {name: "ISO 42001"})
SET k.status = "missing", k.priority = "high",
    k.reason = "AI Management System — relevant for EU AI Act Art. 9"
```

Legal News Scanner writes `NewsEvent` nodes (ADR-011):

```cypher
MERGE (n:NewsEvent {source: "EUR-Lex", url: "...", date: date("2026-04-15")})
MERGE (n)-[:AMENDS]->(l:Law {name: "EU AI Act"})
SET n.verified = false
```

When a `NewsEvent -[:AMENDS]→ Law` is created:
- Law node confidence drops: `1.0 → 0.7`
- Affected generated documents get `staleness_flag: true`
- User notified via Telegram: *"EU AI Act changed — your docs may be outdated"*

After human verification via `/verify`:
- `confidence` returns to `1.0`, `note_unverified` cleared
- Documents can be regenerated with `/scan`

---

## Extension, not refactoring

All four layers are **additive**. No existing node or relationship needs to change.

| What | Type | Status |
|---|---|---|
| `applies_from`, `confidence`, `jurisdictions` on Law nodes | Extension | ✅ done |
| Seed layer architecture (ADR-009) | Refactoring | ✅ done |
| UseCase nodes — deployer risk (ADR-010) | Extension | ✅ done |
| `KnowledgeSource` nodes | Extension | 🔲 next |
| `ProjectAsset` nodes (Scanner output) | Extension | 🔲 v1 |
| `NewsEvent` nodes + confidence decay (ADR-011) | Extension | 🔲 v1 |

---

## Context Graph as prerequisite for the self-learning orchestrator

An agent can only learn if it has a personalised, temporal context.
A static knowledge graph gives it no basis for change — no deltas,
no development, no user feedback.

```
Rescan → new ProjectAsset edge
         ↓
         delta to last scan detectable
         ↓
         compliance_task generated
         ↓
         user confirms or rejects via Telegram /approve
         ↓
         feedback signal: what was relevant, what was not?
         ↓
         Preference node per user_id (Telegram permanent ID)
         ↓
         next scan weights findings by learned preferences
```

Without Layer 3 (ProjectAsset nodes from scanner) there is no delta.
Without delta there is no learning signal.
Without a learning signal the orchestrator remains static.

---

## Where we are in the GraphRAG+ progression

| Stage | Description | Lex-Orchestra |
|---|---|---|
| 3 — GraphRAG | Graph relationships replace text chunks | ✅ Neo4j property graph, Cypher traversal |
| 4 — Ontology RAG | Structured ontologies for precision retrieval | ✅ Law/Control/UseCase as typed node hierarchy |
| 5 — Specialised retrieval | Temporal, accuracy-sensitive, anomaly retrieval | ✅ `applies_from`, `confidence`, deadline queries |
| 6 — Self-describing stores | Graph carries metadata about its own structure | ✅ `KnowledgeSource {status: "missing"}`, confidence decay |
| 7 — Dynamic retrieval | LLM derives retrieval logic from schema at runtime | 🔲 planned |
| 8 — Closing the loop | System reingests outputs to adjust future retrieval | 🔲 planned |

Stages 3–6 are implemented. Stages 7–8 are the explicit next evolution.

**One architectural constraint shapes everything:** data sovereignty.
No user data, code, or infrastructure details leave the local network.
The graph receives only anonymised UUIDs and asset types (ADR-001).
This makes the system deployable in compliance-sensitive environments
where sending proprietary code to an external service is not an option.

This is not a limitation of ambition — it is a deliberate design choice.
The Context Graph is useful precisely *because* it can reason about sensitive
infrastructure without ever seeing it.

---

## Summary

```
Layer 1 — Seed Layer Architecture    Handbook    deterministic, global + jurisdictions
Layer 2 — Temporal Graph             Calendar    what applies, when, for whom, how certain
Layer 3 — Context Graph              Mirror      what applies to THIS project
Layer 4 — Self-Describing            Compass     what is missing or stale
                                                          ↓
                                           Self-Learning Orchestrator
                                      (feedback via /approve + Preference nodes)
```

---

## Further reading

- [data-sovereignty.md](data-sovereignty.md) — What stays where
- [Context Graph Manifesto](https://trustgraph.ai/news/context-graph-manifesto/) — TrustGraph, Dec 2025
- [GraphRAG for Legal AI: Why Knowledge Graphs Beat Vector Search](https://medium.com/@thomasrehmer/graphrag-for-legal-ai-why-knowledge-graphs-beat-vector-search-01436abfe095) — Thomas Rehmer, Dec 2025
- [Claude + Neo4j with MCP: Turning Your Knowledge Graph into an AI Interface](https://medium.com/@thomasrehmer/claude-neo4j-with-mcp-turning-your-knowledge-graph-into-an-ai-interface-910ca04e9942) — Thomas Rehmer, Jan 2026
- [Privacy by Architecture: Why Your Knowledge Graph Should Only Store UUIDs](https://medium.com/@thomasrehmer/privacy-by-architecture-why-your-knowledge-graph-should-only-store-uuids-a26fb375c908) — Thomas Rehmer, Feb 2026
