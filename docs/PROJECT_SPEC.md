# PoliScope Project Specification v0.1

## Product idea

PoliScope is an evidence-grounded AI research assistant for political and policy discourse analysis.

Its first use case is longitudinal analysis of official European Union artificial-intelligence policy discourse.

## Intended users

- political-science researchers
- social-science students
- policy researchers
- computational social scientists

## Core user story

As a researcher, I want to ask a longitudinal or comparative political-policy question and receive an answer whose analytical claims are traceable to relevant primary-source evidence.

## MVP capabilities

1. ingest selected official EU documents;
2. preserve document metadata and provenance;
3. retrieve relevant passages for a research question;
4. generate evidence-grounded synthesis;
5. expose citations/evidence used by the system;
6. evaluate retrieval and claim support against a human-created benchmark.

## Explicit non-goals

The MVP will not:
- determine which political position is correct;
- predict elections or political outcomes;
- infer individual political preferences;
- analyse all EU policy areas;
- support every European language;
- build a comprehensive knowledge graph;
- operate as a fully autonomous research agent.

## Initial comparison systems

### System 0 — Vanilla LLM
Question → LLM → answer

### System 1 — Basic RAG
Question → dense retrieval → top-k passages → LLM → cited answer

### System 2 — Hybrid RAG
Question → BM25 + dense retrieval → fusion → reranking → LLM

### System 3 — PoliScope
Question → decomposition → hybrid retrieval → reranking → structured evidence extraction → synthesis → claim verification

## Engineering principles

- provider-independent LLM interface;
- reproducible ingestion;
- strong metadata and provenance;
- evaluation before feature expansion;
- modular components with testable interfaces;
- documented architecture decisions;
- no production claim without measured evidence.
