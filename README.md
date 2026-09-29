# PoliScope AI

**Evidence-grounded AI for computational political discourse research**

PoliScope is a research-engineering project investigating whether an evidence-grounded AI system can support longitudinal political and policy discourse analysis more reliably than vanilla LLM and conventional RAG approaches.

## Initial application case

The first case study focuses on official European Union discourse on artificial intelligence policy, approximately 2019–2026.

## Research comparison

PoliScope will compare progressively stronger systems:

1. Vanilla LLM
2. Basic dense-retrieval RAG
3. Hybrid retrieval + reranking
4. PoliScope: query decomposition + hybrid retrieval + structured evidence extraction + synthesis + claim verification

The goal is not to automate political judgement. The system is designed as research decision support: generated analytical claims should remain traceable to source evidence and subject to human interpretation.

## Current stage

**Sprint 1: research design and corpus audit**

Priorities:
- freeze the research question and scope;
- define corpus inclusion/exclusion criteria;
- audit official EU source availability;
- design the evaluation benchmark;
- define the minimal system architecture.

See `docs/RESEARCH_PROTOCOL.md` and `docs/PROJECT_SPEC.md`.

## Planned structure

```
poliscope-ai/
├── docs/
├── src/poliscope/
├── tests/
├── data/
└── experiments/
```

## Status

Early research prototype. Methods, data sources and architecture will evolve through documented decisions.
