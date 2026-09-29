# Data Sources and Corpus Audit

## Purpose

This document will define and audit the PoliScope v1 corpus before large-scale ingestion begins.

## Candidate source families

1. European Commission
2. European Parliament
3. Council of the European Union

Only official/primary institutional material is in scope for the first corpus version.

## Candidate document types

- legislative proposals
- regulations / official legal texts
- communications
- policy strategies
- committee or parliamentary material
- speeches and official statements
- press releases when substantively relevant
- implementation guidance

## Proposed temporal scope

Approximately 2019–2026.

The exact start/end dates will be fixed after source audit.

## Audit fields

For every source family record:

| Field | Description |
|---|---|
| institution | Publishing institution |
| document_type | Communication, regulation, debate, speech, etc. |
| title | Official title |
| publication_date | Date published |
| source_url | Canonical official URL |
| language | Document language |
| format | HTML, PDF, XML, etc. |
| stable_id | Official identifier where available |
| full_text_access | Yes / partial / no |
| metadata_quality | High / medium / low |
| machine_access | API / downloadable / scrape required |
| inclusion_status | Include / exclude / review |
| notes | Important limitations |

## Inclusion criteria — provisional

A document should:
- originate from an official EU institutional source;
- materially concern AI policy, governance, regulation or implementation;
- fall within the final temporal scope;
- have recoverable text and adequate provenance.

## Exclusion criteria — provisional

Exclude:
- duplicate mirrors;
- pages with no substantive policy content;
- third-party summaries presented outside the official institutional source set;
- documents without sufficient provenance;
- material unrelated to the research domain.

## Next task

Conduct a source audit before implementing automated ingestion.
