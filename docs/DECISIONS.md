# Architecture and Research Decision Log

This file records consequential decisions so that implementation choices remain explainable and reviewable.

## ADR-001 — Start with one policy domain

**Decision:** PoliScope v1 will focus on EU artificial-intelligence policy discourse rather than general political discourse.

**Reason:** A bounded domain permits defensible corpus construction, manual evaluation and meaningful longitudinal analysis within the project timeline.

## ADR-002 — Use primary institutional sources first

**Decision:** The initial corpus will prioritise official EU institutional documents.

**Reason:** Primary sources reduce provenance ambiguity and allow evidence-grounded evaluation before introducing media or secondary commentary.

## ADR-003 — Benchmark before advanced agents

**Decision:** Evaluation design precedes complex agentic orchestration.

**Reason:** Without a benchmark, additional orchestration cannot be shown to improve research reliability.

## ADR-004 — Knowledge graph is not MVP-critical

**Decision:** Knowledge-graph construction is deferred unless retrieval/analysis results justify it.

**Reason:** It adds substantial engineering scope and is not required to test the main research question.

## ADR-005 — Provider-independent LLM layer

**Decision:** Model providers should be wrapped behind a common interface.

**Reason:** The project is intended to compare system architectures without coupling findings to one proprietary API.

## ADR-006 — Prefer documented machine-access routes over web scraping

**Decision:** Use EUR-Lex/Publications Office CELLAR as the primary machine-access backbone for formal EU publications and the European Parliament Open Data Portal for Parliament records. Use the Council public register through a controlled, provenance-preserving acquisition path when no equivalent documented full-text API is available.

**Reason:** Stable identifiers, documented interfaces and reproducible retrieval are methodologically preferable to fragile scraping. An undocumented endpoint that happens to work is not considered a stable data source.

## ADR-007 — Preserve document genre as an analytical variable

**Decision:** Formal legislation/policy texts, committee or negotiating positions, and institutional press communications must be tagged as separate document genres. Communications material may supplement the corpus but must not be silently pooled with legal/policy documents in longitudinal framing analyses.

**Reason:** Genre itself affects language, framing and rhetorical style. Mixing press releases with regulations or formal policy texts could create apparent temporal or institutional differences that are actually genre effects.
