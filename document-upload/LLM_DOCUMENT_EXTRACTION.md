# LLM Document Parsing & Application Pre-fill

> **Type**: Story  
> **Project**: SME Origination  
> **Epic**: Sales-Assisted Onboarding Platform  
> **Priority**: Medium  
> **Labels**: `llm`, `document-extraction`, `sales-flow`, `pre-fill`, `automation`  
> **Blocked by**: Store Photos & Company Documents Upload (parent ticket)

---

## Summary

As a POS terminal sales agent, I want the uploaded Gewerbeanmeldung or partnership agreement to be automatically parsed by an LLM, so that key fields are pre-filled into the client's application and I don't have to re-enter data manually.

---

## Background & Context

### Why this matters

When a sales agent uploads a GEWA or partnership agreement (see parent ticket: *Store Photos & Company Documents Upload*), the document contains most of the information needed to fill the application: business name, legal form, registered address, owner/partner details, NACE code, etc.

Today, agents read the document and manually type this data into the application fields. This is:
- Slow (adds 5-10 min per application)
- Error-prone (typos in names, addresses, registration numbers)
- Redundant (the data is already in the document)

### How it works

```
Agent uploads GEWA / partnership agreement
        ↓
Document sent to LLM extraction service
        ↓
LLM reads document → extracts structured fields
        ↓
Extracted data pre-fills application fields
        ↓
Agent reviews, confirms or corrects → continues
```

### Prerequisite

This feature depends on the document upload infrastructure being live (parent ticket). The upload screen must be deployed and functional before extraction can be wired in.

---

## User Story

### US-1: LLM document extraction & application pre-fill

**As a** POS terminal sales agent  
**I want** the uploaded GEWA or partnership agreement to be automatically read and key fields pre-filled into the application  
**So that** I don't have to manually re-enter information that's already in the document

**Acceptance Criteria:**
- [ ] After successful upload of GEWA or partnership agreement, extraction is triggered automatically
- [ ] Extraction runs in the background — agent sees a "Processing document..." indicator
- [ ] On success: pre-filled fields are shown to the agent for review/confirmation
- [ ] On failure or low confidence: agent fills fields manually (graceful fallback — no blocker)
- [ ] Agent can always override any pre-filled value
- [ ] Extraction does NOT block the upload flow — agent can continue while processing happens

---

## Fields to Extract (TBD)

> **Note**: Exact field mapping to application data model to be defined during implementation.

| Document | Possible fields to extract | Maps to (TBD) |
|----------|---------------------------|----------------|
| **Gewerbeanmeldung** | Business name, legal form, registration date, registered address, business activity (NACE code), owner name | Application fields TBD |
| **Partnership agreement** | Partnership name, partners (names, roles, ownership %), registered address, date of formation, business purpose | Application fields TBD |

---

## Technical Approach (TBD)

All items below are to be defined during implementation:

- [ ] **LLM provider and model selection** — e.g., Claude (Anthropic), GPT-4o (OpenAI), or other. Needs evaluation for accuracy on German legal documents.
- [ ] **Prompt engineering** — Reliable extraction from German Gewerbeanmeldung and partnership agreements. Documents may be scanned PDFs, photos, or digital PDFs.
- [ ] **Structured output format** — JSON schema for extracted fields.
- [ ] **Mapping: extracted fields → application data model** — Which extracted values map to which application form fields.
- [ ] **Confidence scoring** — Flag low-confidence extractions for agent review (e.g., blurry scan, handwritten fields).
- [ ] **Fallback behavior** — If extraction fails or confidence is low, agent fills fields manually (current flow, no regression).
- [ ] **Latency budget** — How long can the agent wait? Should extraction be async (agent proceeds, pre-fill populates in background)?
- [ ] **Cost estimation** — Per-document LLM API cost at expected volume.

---

## Open Questions

1. **Which LLM model?** — Claude vs GPT-4o vs other. Needs accuracy evaluation on German legal documents.
2. **Latency budget?** — How long can the agent wait for extraction? Should it be async (agent proceeds, pre-fill happens in background)?
3. **Scanned vs digital PDFs** — Do we need OCR as a pre-processing step, or can the LLM handle scanned/photo documents directly?
4. **Confidence threshold** — What confidence level triggers manual review vs auto-fill?
5. **Audit trail** — Do we need to log what was extracted vs what the agent submitted (for compliance)?
6. **Volume** — Expected number of documents per day/month for cost estimation.

---

## Dependencies

| Dependency | Team | Status |
|------------|------|--------|
| Document upload infrastructure (parent ticket) | Frontend / Backend | TBD |
| LLM API access (provider TBD) | Platform / AI | TBD |
| Application field mapping definition | Product / Backend | TBD |
| Pre-fill UI (review & confirm screen) | Frontend | TBD |

---

## Definition of Done

- [ ] GEWA upload triggers LLM extraction automatically
- [ ] Partnership agreement upload triggers LLM extraction automatically
- [ ] Extracted fields pre-fill the application form
- [ ] Agent can review and override any pre-filled value
- [ ] Graceful fallback when extraction fails (manual entry, no blocker)
- [ ] "Processing document..." indicator shown during extraction
- [ ] Tested with real German Gewerbeanmeldung documents (digital + scanned)
- [ ] Tested with real partnership agreements
- [ ] Extraction accuracy meets agreed threshold (TBD)
