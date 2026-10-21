# Minimal document and chunk data model v0.1

Design for [Issue #4](https://github.com/swampyaepaing/poliscope-ai/issues/4), following [DATA_SOURCES.md](DATA_SOURCES.md). This is a storage-neutral contract for a reviewed manifest, extracted documents and retrieval chunks. It adds no parser, database or retrieval implementation.

## Units and conventions

A **work** is a distinct policy document, independent of language and format. A **document** is one immutable language/version/extraction of that work. A **manifestation** is a retrieved file or page (HTML, PDF, XML or mirror). A **chunk** is a verbatim span of a document's preserved extracted text. Metadata-only and failed acquisitions remain document records without retrieval chunks.

Tables use **R** for required non-null, **N** for required key with nullable value, **O** for optional, and **C** for conditionally required. Arrays are present even when empty; null means unknown, while [] means no values recorded, not a verified absence. Unknown required metadata uses a nullable field and a reason in metadata_notes, never a guessed value. All strings are UTF-8. Dates are ISO 8601 calendar dates; timestamps are RFC 3339 with timezone. Preserve source spelling in identifiers and labels.

## Document schema

| Field | Type / presence | Definition |
|---|---|---|
| schema_version | string, R | Contract version, initially "0.1". |
| document_id | string, R | Opaque globally unique immutable record ID; see identity rules. |
| canonical_work_id | string, R | Stable internal ID grouping versions, translations and manifestations of this work. |
| title | string, R | Official title, without silently replacing it with a website heading. |
| institution | string[], R | Institutional authorities whose position/status the text represents: european_commission, european_parliament, council_of_the_eu; [] if unresolved or outside scope. Joint acts list both co-legislators once. European Council is a separate value european_council, never Council-of-EU shorthand. |
| authors | Agent[], R | Explicit authors, including organisations, committees or people. Do not derive authorship from the host. |
| publisher | Agent, N | Publishing body, separately from author and institutional authority; host recorded per manifestation. |
| document_type | string, R | proposal, communication, white_paper, impact_assessment, impact_assessment_summary, guidance, resolution, legislative_position, committee_report, committee_opinion, conclusions, general_approach, negotiating_mandate, legal_act, corrigendum, press_release, other or unknown. Preserve finer source labels in metadata. |
| institutional_status | string, R | proposal, adopted_position, enacted_act, final_report, final_opinion, final_guidance, institutional_communication, expert_output, draft or unknown. "Official" does not establish legal force. |
| official_ids | Identifier[], R | Namespaced document identifiers; [] permitted for URL-only sources. |
| procedure_ids | Identifier[], R | Separate many-document procedural groupings, e.g. eu_procedure:2021/0106(COD). Never document keys. |
| language | string, R | BCP 47 language tag for this text; en for v1 eligible records; und for unresolved metadata-only records. No silent translation. |
| version | object, R | {kind, source_label, version_date, part_id}; kind is original, revision, correction, consolidation or unknown; remaining keys nullable. Preserve full REV/COR/ADD, PE and annex markers. |
| document_date | date, N | Official issue date on the document; distinct from publication. |
| adoption_date | date, N | Official adoption/agreement date, only with supporting evidence. |
| publication_date | date, N | Publication of this version by the publisher. |
| first_public_date | date, N | Earliest evidenced public availability, not automatically publication or issue date. |
| event_date | date, N | Related meeting/vote/announcement event. Do not substitute for document_date. |
| updated_date | date, N | Explicit web/source update date, not acquisition time or a new historical year. |
| retrieved_at | timestamp, N | Last acquisition attempt represented by this record; required non-null after an attempt. Individual files retain their own times. |
| cutoff_date | date, R | Corpus release freeze; initial recommendation 2026-10-01. |
| source_url | URI, R | Official discovery/record URL, even if acquisition fails. |
| canonical_url | URI, N | Preferred official document locator, when established. |
| alternative_urls | URI[], R | Other known locators; not evidence of successful acquisition. |
| manifestations | Manifestation[], R | Acquired or attempted file/page representations, defined below. |
| raw_text | string, N | Exact preserved extracted substantive text, not a landing-page summary, source bytes or later cleaned retrieval text. Non-empty for eligible documents. |
| text_provenance | object, N | Required when raw_text exists: {manifestation_id, extractor, extractor_version, extraction_profile, text_hash}. Profile describes Unicode, whitespace, page separators and OCR handling; text_hash is SHA-256 of exact UTF-8 raw_text. |
| relations | Relation[], R | Directed work/version links with evidence, defined below. |
| full_text_access | enum, R | public_full, partial_redacted, metadata_only, request_required, retrieval_failed or unknown. Public availability and visible markings are independent. |
| access_marking | string, N | Printed label, e.g. LIMITE; do not erase because a URL is public. |
| extraction_status | enum, R | not_attempted, succeeded, partial or failed. |
| inclusion_status | enum, R | include, review, exclude or defer. |
| exclusion_reason | string, C | Required for exclude/defer; review rationale goes in metadata_notes. |
| corpus_stratum | enum/null, N | formal_core, joint_legal_core or communications_supplement; null for unclassified records. |
| citation | object, R | {display_title, preferred_url, publication_reference}; title required; URL/reference nullable. Use the immutable record's metadata, not a live lookup. |
| metadata_notes | string[], R | Missing-data explanations, conflicts and audit limitations. |
| metadata | object, O | Source-specific audit fields such as metadata_quality, machine_access and original source labels. Must not hide core fields here. |

**Agent:** {name: string, kind: institution|committee|person|organisation|unknown, id: string|null}. Name and kind required; use stable official/internal IDs only when established. Authors may include committees or speakers, but quotations inside a press release do not make the speaker its author. Publisher must never be used as a fallback author.

**Identifier:** {namespace: string, value: string, uri: URI|null}. Namespace/value required; uniqueness uses the pair, not value alone. Initial namespaces: celex, eli, cellar_work, commission_com, commission_swd, commission_c, ep_ta, ep_doceo, ep_eli, ep_report, ep_pe, ep_press, council_document, commission_press and eu_procedure. New namespaces may be added with a documented issuing system. Examples: celex:32024R1689, commission_com:COM(2021)206 final, ep_ta:P8_TA(2019)0081, council_document:ST-14954-2022-INIT. ELI/ELI-EP values are complete official URIs. Do not invent CELEX/CELLAR IDs or collapse INIT, REV, COR and ADD.

**Manifestation:** {manifestation_id, downloaded_url, format, host, retrieved_at, retrieval_status, content_hash, storage_uri}. First six fields required: format is MIME type; host is hostname; retrieval_status is succeeded or failed. On success, content_hash (sha256:hex of original bytes) and durable storage_uri are required. On failure they are null; any returned error/verification page is not a successful policy-text download. Record attempted URL and time even when no file was obtained. Raw text provenance identifies exactly one successful manifestation; retaining other formats does not create additional policy documents.

**Relation:** {type, target, evidence_url, note}. Type and target required; evidence_url and note nullable, with at least one meaningful evidence explanation/locator. Target is either {document_id} for an exact version, {canonical_work_id} for a work-level link, or {official_id: Identifier} for an unresolved target. The last form is not a fabricated internal ID. Types:

| Type | Direction and meaning |
|---|---|
| parent_document | Child annex/part → parent. Independent substantive annexes may be records; navigation pages are not policy texts. |
| supersedes | New text → explicitly replaced text. Use only with evidence of replacement. |
| corrects | Corrigendum/correction → affected version; preserve both. |
| derived_from | Text → source text, including a Council circulation copy of a Commission proposal or an explicitly derived consolidation. Does not imply adoption or replacement. |
| related_to | Text → related policy stage/work where a stronger relation is unsupported; proposal and final act remain distinct works. |
| embeds | Container → independently represented opinion/report. Record known duplicate passage spans in chunk overlap metadata. |

## Identity, version and eligibility rules

1. Assign opaque internal UUIDs to work/document/manifestation IDs. Official IDs support reconciliation but cannot safely encode every source's identity. Maintain an explicit reviewed alias mapping; title similarity or a procedure match is insufficient to merge works.
2. Group format mirrors only after official-ID reconciliation and content corroboration. Byte hashes differ across HTML/PDF; a matching hash proves identical bytes, while a mismatch alone proves no policy difference. Do not merge stages or independent summaries/annexes by text similarity.
3. A document is immutable after chunks/citations reference it. A changed official version, language, substantive text or extraction output creates a new document_id, retaining canonical_work_id where it remains the same work. Distinguish extraction changes in text_provenance from official changes in version; parser reruns do not manufacture an official revision or supersedes relation.
4. Corrected text and the separately issued corrigendum are distinct records. A separate amending act is a separate work linked to the affected act; consolidation is a snapshot, deferred as independent discourse in v1. Never overwrite the original.
5. A repeated identical acquisition can reuse the same document/text while appending acquisition provenance. A source label without a version date remains nullable; do not use retrieved_at as version_date. A changed URL-only page gets a new record when its preserved content changes.
6. Select by evidenced document_date/adoption_date under DATA_SOURCES.md. Resolve conflicts explicitly; unknown original dates require review. Do not use updated_date or retrieval time to make a document eligible. Keep the initial 2019-01-01–2026-10-01 boundary and partial-2026 interpretation.
7. Retrieval eligibility requires inclusion_status=include, English substantive raw_text, extraction_status=succeeded, full_text_access=public_full, successful retained source bytes and a resolved official selection date within the freeze. Partial/redacted/failed/metadata-only records retain coverage metadata but have no retrieval chunks. Final status, authority and scope must also satisfy the audit.
8. One joint act belongs to both co-legislators but is counted once. Formal and communications strata stay explicit. Use connected work/version/embedded-overlap groups when splitting evaluation data to reduce leakage.

## Chunk schema

| Field | Type / presence | Definition |
|---|---|---|
| schema_version | string, R | "0.1". |
| chunk_id | string, R | Stable unique ID for document_id + chunking_profile + chunk_index; deterministic UUIDv5 over that tuple is recommended. |
| document_id | string, R | Foreign key to exact immutable source document, never just work/procedure. |
| chunking_profile | string, R | Versioned configuration identifier covering segmentation, size and overlap. Changed boundaries require a new profile/IDs. |
| chunk_index | integer, R | Zero-based order within that document/profile; unique with the preceding fields. |
| text | string, R | Exact raw_text[start_char:end_char], non-empty. No inserted headings or paraphrases. |
| start_char, end_char | integer, R | Zero-based Unicode code-point offsets into raw_text, start inclusive/end exclusive. Not UTF-8 bytes, UTF-16 units, PDF offsets or token counts. |
| section_title | string, N | Source heading, null if absent/unknown; never an inferred heading presented as original. |
| section_path | string[], R | Ordered source heading hierarchy, [] when unavailable. |
| locator | object, R | {article, paragraph, annex, page_start, page_end, anchor}; all nullable. Pages are one-based physical PDF pages; printed labels may be preserved in metadata. Anchor is verified source fragment only. |
| token_offsets | object, O | {start, end, tokenizer, tokenizer_version}; all required together, end exclusive, measured over whole raw_text. Never claim comparability between tokenizers. Character offsets remain authoritative. |
| overlaps | object[], R | [] or {document_id, start_char, end_char, relation}; target spans refer to exact other document raw_text. relation is embeds or duplicate_passage. Ordinary within-document chunk overlap follows offsets/profile. |
| attribution | object[], O | Exact speaker/quoted-author span annotations if evidenced: {start_char, end_char, agent, evidence_url}; offsets are document-relative. No inferred speaker attribution. |
| citation | object, R | {document_id, source_manifestation_id, locator}; pins exact document, extraction source and same locator as above. Bibliographic metadata is resolved from that immutable document. |

Do not create chunks for excluded or unextracted records. Validate foreign keys, unique indices, nonnegative offsets, end_char <= code-point length(raw_text), and exact text equality. Page/section locators are optional because formats differ; character offsets are always required once text is chunked. Non-contiguous spans require separate chunks.

Citation rendering uses document title, authors/authority as appropriate, document type/status, namespaced identifier or publication reference, distinct labelled date(s), version/language, official URL and chunk locator. Retrieval date may be included as access date, never publication date. Keep chunk_id/document_id in stored claim-to-evidence references. Do not fabricate page numbers, URL anchors or bibliography fields. Offsets remain an internal fallback when no official locator exists.

## Three illustrative document records

These compact records instantiate the complete required document shape via shared defaults below. They are **metadata-only design fixtures**, derived solely from the audit's S4, S17/S19/S20 and S21 descriptions, not newly downloaded full texts or certified ingestion results. source_url/canonical_url are recorded official locators, not successful download claims. Examples deliberately contain no raw-text quotes, hashes, invented retrieval timestamps or chunk records.

For each row, apply these defaults, then its overrides: schema_version="0.1"; authors=[]; publisher=null; official_ids=[]; procedure_ids=[]; language="en"; version={kind:"original",source_label:null,version_date:null,part_id:null}; all seven date/time fields document_date, adoption_date, publication_date, first_public_date, event_date, updated_date and retrieved_at=null; cutoff_date="2026-10-01"; alternative_urls=[]; manifestations=[]; raw_text=null; text_provenance=null; relations=[]; full_text_access="metadata_only"; access_marking=null; extraction_status="not_attempted"; inclusion_status="review"; metadata_notes=["Metadata fixture only; full-text acquisition and eligibility validation pending. Unknown dates, author and publisher details require source verification."]; citation={display_title:title,preferred_url:canonical_url,publication_reference:null}. Identifier objects below abbreviate {namespace,value,uri:null}; Agent abbreviations expand to {name,kind:"institution",id:null}. IDs are reserved illustrative UUIDs, not real assigned corpus IDs.

### 1. Commission AI Act proposal (S4)

- document_id: 00000000-0000-4000-8000-000000000101
- canonical_work_id: 00000000-0000-4000-8000-000000000001
- title: Proposal for a Regulation laying down harmonised rules on artificial intelligence
- institution: [european_commission]
- authors: [European Commission]
- document_type: proposal; institutional_status: proposal; corpus_stratum: formal_core
- official_ids: [celex:52021PC0206, commission_com:COM(2021)206 final]
- procedure_ids: [eu_procedure:2021/0106(COD)]
- version.source_label: COM(2021)206 final
- source_url = canonical_url: https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:52021PC0206
- relations: [{type:"related_to", target:{official_id:{namespace:"celex",value:"32024R1689",uri:null}}, evidence_url:null, note:"Audit identifies proposal and enacted act as separate stages of the AI Act initiative; no supersession claim."}]

### 2. Council general-approach document (S17, S19, S20)

- document_id: 00000000-0000-4000-8000-000000000102
- canonical_work_id: 00000000-0000-4000-8000-000000000002
- title: Artificial Intelligence Act: general approach
- institution: [council_of_the_eu]
- authors: []; publisher: null (circulation author and publishing body must be checked in the actual file; institutional authority is not an author fallback)
- document_type: general_approach; institutional_status: adopted_position; corpus_stratum: formal_core
- official_ids: [council_document:ST-14954-2022-INIT]
- procedure_ids: [eu_procedure:2021/0106(COD)]
- version.source_label: ST-14954-2022-INIT
- document_date: 2022-11-25; adoption_date: 2022-12-06; event_date: 2022-12-06
- source_url = canonical_url: https://data.consilium.europa.eu/doc/document/ST-14954-2022-INIT/en/pdf
- access_marking: LIMITE
- metadata_notes: ["Metadata fixture; no acquisition performed here. Audit S17 reports publicly readable text despite LIMITE marking. S19/S20 establish the December 6 general-approach event; November 25 remains the document date. Title is a descriptive audit label pending exact official-title verification."]
- metadata: {date_evidence:{document_date:"DATA_SOURCES.md evidence register S17",adoption_date:"https://www.consilium.europa.eu/en/press/press-releases/2022/12/06/artificial-intelligence-act-council-calls-for-promoting-safe-ai-that-respects-fundamental-rights/",event_date:"https://www.consilium.europa.eu/en/meetings/tte/2022/12/06/"}}

This record stays in review until exact title, authorship and full-text acquisition are checked.

### 3. Joint AI Act (S21)

- document_id: 00000000-0000-4000-8000-000000000103
- canonical_work_id: 00000000-0000-4000-8000-000000000003
- title: Regulation (EU) 2024/1689 (Artificial Intelligence Act)
- institution: [european_parliament, council_of_the_eu]
- authors: [European Parliament, Council of the European Union]
- document_type: legal_act; institutional_status: enacted_act; corpus_stratum: joint_legal_core
- official_ids: [celex:32024R1689, {namespace:"eli",value:"https://eur-lex.europa.eu/eli/reg/2024/1689/oj",uri:"https://eur-lex.europa.eu/eli/reg/2024/1689/oj"}]
- publication_date: 2024-07-12
- source_url = canonical_url: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
- citation.publication_reference: OJ L, 2024/1689, 12 July 2024

This is one work with joint authority, not two institution-specific copies. The English extraction, publisher and remaining dates still need verification.

## Acceptance boundary

Before parser implementation, this design fixes identity granularity, nullable metadata, immutable text, date meanings, source-byte retention, authority/authorship separation, explicit relations and reproducible chunk citation. Later parsers may populate source-specific metadata or add documented identifier/type vocabulary values without changing those core meanings. Embeddings, parser code, database migrations, claim verification and a comprehensive legal knowledge graph are outside this issue.
