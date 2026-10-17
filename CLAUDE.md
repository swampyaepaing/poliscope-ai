# CLAUDE.md — PoliScope AI collaboration rules

This repository is a research-engineering project, not a generic RAG demo. Before making changes, read:

1. `docs/PROJECT_SPEC.md`
2. `docs/RESEARCH_PROTOCOL.md`
3. `docs/DECISIONS.md`
4. `docs/DATA_SOURCES.md`

## Project objective

PoliScope investigates whether evidence-grounded AI systems can support longitudinal political-policy discourse research more reliably than vanilla LLM and conventional RAG approaches. The first domain is official EU artificial-intelligence policy discourse.

## Rules for Claude

### 1. Do not silently redesign the project
Do not introduce a new framework, vector database, agent architecture, data source, benchmark metric, or research question without explicitly proposing the change and explaining why it is necessary.

### 2. Prefer bounded tasks
Implement only the requested module or experiment. Do not refactor unrelated files or build extra features unless asked.

### 3. Research integrity
- Never invent citations, source metadata, document counts, benchmark labels, or evaluation scores.
- Never fill missing experimental results with plausible values.
- Distinguish observed evidence from assumptions.
- Do not claim causality from descriptive or predictive analyses.

### 4. Source provenance
Every ingested political document must preserve enough metadata to recover its official source. Do not strip stable identifiers, canonical URLs, institution, document date, language, document type, or version/revision information.

### 5. Evaluation before complexity
Do not add agentic orchestration, knowledge graphs, or complex frameworks merely because they are available. A feature must help test a documented research question or improve a measured outcome.

### 6. Framework restraint
Do not add LangChain, LlamaIndex, or another orchestration framework by default. Prefer transparent Python components until repeated orchestration complexity justifies a framework.

### 7. Code quality
- Target Python 3.12 unless the repository changes this decision.
- Use typed interfaces where useful.
- Prefer small, testable functions.
- Add tests for parsing, normalization, identifiers, retrieval logic, and other deterministic behaviour.
- Fail loudly on malformed source data rather than silently discarding provenance.

### 8. Changes should be explainable
For a non-trivial change, report:
- files changed;
- design choice made;
- assumptions;
- tests run;
- known limitations;
- any proposed follow-up work.

## Current phase

Sprint 1 prioritizes corpus validation, literature grounding, evaluation design, document/chunk schemas, and a minimal Python scaffold. Do not begin building the final RAG application unless the relevant Sprint 1 design tasks are complete.

## When asked to investigate an external data source

Return evidence, not guesses. For each source report:
- official documentation URL;
- access method;
- authentication requirement;
- available formats;
- stable identifiers;
- sample request/response shape;
- pagination/rate-limit information if documented;
- whether full text or only metadata is returned;
- any uncertainty that still requires testing.

Do not treat an undocumented endpoint as a stable API solely because a browser request happens to work.
