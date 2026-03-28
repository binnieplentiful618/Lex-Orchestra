# ADR-010 — UseCase Nodes: EU AI Act Deployer Risk

> Compliance distinguishes between provider and deployer risk via UseCase nodes.

**Status:** Accepted
**Date:** 2026-03-21

## Context

The EU AI Act distinguishes between provider risk (the company that builds
an AI model) and deployer risk (the company that uses an AI model in a product).

Without UseCase nodes, Lex-Orchestra incorrectly assigns provider risk to
deployers: a startup using the Claude API for a customer service chatbot
receives GPAI documentation obligations — which apply to Anthropic, not to them.

## Decision

`UseCase` nodes in the graph represent deployment scenarios from the
deployer perspective with their correct EU AI Act risk classification.

The Infrastructure Scout detects deployment signals from code and maps
them to UseCase nodes. The Document Architect uses the deployer UseCase
risk level when generating the AI Act Manifest.

**Risk level categories (from the EU AI Act):**
- Unacceptable — prohibited systems (Art. 5)
- High — Annex III systems (biometrics, HR, credit, education)
- Limited — transparency obligations only (Art. 50 — chatbots, generated content)
- Minimal — all other AI use cases

The AI Act Manifest separates both perspectives: provider risk (from detected
Service nodes) and deployer risk (from detected UseCase nodes).

## Consequences

**Positive:**
- Correct compliance output: deployers receive their own obligations
- AI Act Manifest separates provider and deployer perspective
- Foundation for high-risk AI alerts when sensitive use cases are detected

**Negative:**
- UseCase detection from code patterns is heuristic — false positives possible
- Scanner-detected UseCases written with `verified: false` until user confirms
