# Architecture Decision Records

All major technical decisions in Lex-Orchestra are documented as
Architecture Decision Records (ADRs) before implementation.

This is the internal design philosophy: **write the decision first, then build.**
ADRs serve as the roadmap — detailed enough to implement from, honest about trade-offs.

---

## Status legend

| Symbol | Meaning |
|---|---|
| ✅ | Implemented |
| 🔲 | Accepted, implementation planned |

---

## ADR Index

One-liners match the blockquote directly under each ADR title — keep both in sync when editing.

| ADR | Title | One-liner | Status |
|-----|-------|-----------|--------|
| [ADR-001](ADR-001-pii-separation.md) | PII separation | Cloud services never receive customer-identifiable infrastructure data. | ✅ |
| [ADR-002](ADR-002-langgraph-engine.md) | LangGraph engine | All compliance logic runs in a deterministic, stateful LangGraph workflow. | ✅ |
| [ADR-003](ADR-003-merge-over-create.md) | MERGE over CREATE | The graph is idempotent — all writes use MERGE, never CREATE. | ✅ |
| [ADR-004](ADR-004-openclaw-security.md) | OpenClaw security | The UI is read-only — no database or LLM credentials — and can only trigger workflows. | ✅ |
| [ADR-005](ADR-005-graph-write-levels.md) | Graph write tiers | Graph writes follow a three-level model with explicit verification control. | ✅ |
| [ADR-006](ADR-006-neo4j-vs-supabase.md) | Two data stores | Neo4j stores legal knowledge; Supabase stores project state and outputs. | ✅ |
| [ADR-007](ADR-007-doc-delivery.md) | Document delivery | Compliance documents are delivered automatically after every scan. | ✅ |
| [ADR-008](ADR-008-token-management.md) | Token management | Credentials are entered locally and never transmitted via external channels. | ✅ |
| [ADR-009](ADR-009-seed-layers.md) | Seed layers | Regulatory frameworks are loaded as jurisdiction-specific seed layers. | ✅ |
| [ADR-010](ADR-010-usecase-nodes.md) | UseCase nodes | Compliance distinguishes between provider and deployer risk via UseCase nodes. | ✅ |
| [ADR-011](ADR-011-regulatory-change-detection.md) | Change detection | Regulatory changes are detected automatically and reduce confidence until verified. | 🔲 |
| [ADR-012](ADR-012-dpa-url-registry.md) | DPA registry | Every sub-processor includes a direct DPA signing link. | ✅ |
| [ADR-013](ADR-013-preference-notes.md) | Preference notes | User preferences are captured at clause level and applied to future documents. | 🔲 |
| [ADR-014](ADR-014-scout-signals.md) | Scout signals | Compliance findings are derived from six traceable infrastructure signal categories. | ✅ |
| [ADR-015](ADR-015-gtm-orchestrator.md) | GTM orchestrator | Verified legal changes are turned into personalised content drafts. | 🔲 |
| [ADR-016](ADR-016-compliance-delta.md) | Compliance delta | Compliance impact is tracked per commit via lightweight delta scans. | 🔲 |
| [ADR-017](ADR-017-control-versioning.md) | Control versioning | Controls are versioned to prevent silent compliance errors across framework updates. | ✅ |
| [ADR-018](ADR-018-document-validator.md) | Document validator | Generated documents are validated against graph-defined completeness rules before delivery. | ✅ |
| [ADR-019](ADR-019-ontology-drift-prevention.md) | Ontology drift | The graph continuously detects and signals ontology drift before it affects outputs. | 🔲 |
| [ADR-020](ADR-020-context-cores.md) | Context cores | Every graph state is versioned as a ContextCore and attached to generated documents. | 🔲 |
| [ADR-021](ADR-021-retrieval-policies.md) | Retrieval policies | Compliance scope is defined via graph-based retrieval policies instead of hardcoded queries. | 🔲 |
