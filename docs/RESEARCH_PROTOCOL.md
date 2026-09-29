# PoliScope Research Protocol v0.1

## Core research question

To what extent can an evidence-grounded AI research system reliably retrieve, synthesise and analyse longitudinal political-policy discourse, and how does its performance compare with vanilla LLM and conventional RAG approaches?

## Application case

Official European Union discourse on artificial intelligence policy, approximately 2019–2026.

## Objectives

### O1 — Retrieval
Evaluate whether different retrieval architectures accurately identify relevant evidence from a heterogeneous political-document corpus.

### O2 — Evidence-grounded generation
Evaluate whether retrieved evidence produces a higher proportion of source-supported analytical claims than unsupported generation.

### O3 — Political discourse analysis
Investigate whether the system can support analysis of changes in themes, actors and framing across time and institutions.

### O4 — AI engineering
Compare multiple system architectures rather than evaluating only one application.

### O5 — Trustworthiness
Make source evidence and unsupported claims visible to the user.

## Working hypotheses

**H1.** Retrieval-augmented systems will produce a higher proportion of evidence-supported claims than a vanilla LLM.

**H2.** Hybrid retrieval with reranking will outperform dense-only retrieval on a heterogeneous political-policy corpus for retrieval metrics defined in the benchmark.

**H3.** Explicit claim verification will reduce unsupported generated claims relative to ordinary RAG.

**H4.** Structured extraction of actors, concepts, institutions and dates will improve longitudinal evidence synthesis.

These are provisional and will be revised after literature review and corpus audit.

## Planned evaluation dimensions

### Retrieval
- Recall@k
- Precision@k where appropriate
- Mean Reciprocal Rank
- nDCG where graded relevance is available

### Generation
- claim-level evidence support
- citation correctness
- citation completeness
- unsupported-claim rate
- answer completeness against benchmark evidence

### Research usefulness
To be defined after prototype design, potentially using a small human evaluation.

## Research boundaries

PoliScope is a decision-support research tool. It does not determine the correct political interpretation of a policy issue. Human interpretation remains necessary.

## Reproducibility

All reported evaluation results must be generated from versioned code, a versioned benchmark and documented model/configuration settings. No manually invented or AI-filled performance values may enter the final report.
