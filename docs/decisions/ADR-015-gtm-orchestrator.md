# ADR-015 — GTM Orchestrator: Legal Delta to Content Draft

> Verified legal changes are turned into personalised content drafts.

**Status:** Draft
**Date:** 2026-03-22

## Context

ADR-011 implements the defensive side of regulatory change: notifying users
when their compliance documents may be outdated.

The GTM Orchestrator is the offensive side: when a legal change is detected
and verified, Lex-Orchestra already knows exactly how that change affects
this specific tech stack. That personalised delta is more valuable than any
generic legal news summary — and can become thought leadership content before
competitors have even understood the change.

## Decision

When a `NewsEvent` is verified via `/verify`, the GTM Orchestrator
automatically generates a content draft explaining the change from the
perspective of someone who has already handled it in their infrastructure.

The LLM receives only public information and anonymised context:
the news event title and summary, the affected law's state, detected
service names (already public — Stripe, AWS), and use case types.
Never: file names, code, or variable names (ADR-001).

**Output:** A draft delivered via Telegram. The user reviews and publishes
to whichever channel fits — a post, a newsletter, an internal update.
Auto-publishing is intentionally not implemented — legal content requires
human review before publication. This is a permanent design decision,
not a missing feature.

## Consequences

**Positive:**
- Turns compliance maintenance into a competitive advantage
- Draft is personalised to the user's specific tech stack
- No additional infrastructure — uses the same pipeline as ADR-011
- Channel-agnostic: user decides where to publish

**Negative:**
- Draft quality depends on the quality of the NewsEvent summary
- Requires human review before any publication
