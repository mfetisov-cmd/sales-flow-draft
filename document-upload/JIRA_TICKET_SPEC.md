# Store Photos & Company Documents Upload

> **Type**: Story  
> **Project**: SME Origination  
> **Epic**: Sales-Assisted Onboarding Platform  
> **Priority**: High  
> **Labels**: `pos-terminal`, `sales-flow`, `underwriting`, `document-upload`

---

## Summary

As a POS terminal sales agent, I need a screen in the sales-assisted flow to upload store/location photos and the client's Gewerbeanmeldung (GEWA), so that underwriting can verify the merchant's physical presence and legal registration without back-and-forth requests.

---

## Background & Context

### Why this is needed

POS terminal onboarding requires supporting documents for underwriting. The exact requirements vary by business type:

1. **Store / location photos** — Visual proof that the merchant has a physical presence (storefront, signage, interior). Required by Adyen's merchant onboarding guidelines and Vivid's internal underwriting policy.

2. **Gewerbeanmeldung (GEWA)** — The official German business registration document. Confirms the merchant's legal right to operate at the registered address.

3. **Partnership agreement** — For partnerships (GbR, OHG, KG), the agreement defines the legal structure, partners, and ownership shares.

### Document requirements by business type

| Business Type | Store Photos | Gewerbeanmeldung | Partnership Agreement |
|---------------|:------------:|:----------------:|:---------------------:|
| **Non-reg Freelancer** | Optional | Optional | — |
| **Registered Freelancer** | Required | Required | — |
| **Legal Entity** | Required | Required | — |
| **Legal Entity in Formation** | Required | Required | — |
| **Partnership** | Required | Optional | Required |

> **Note**: Non-registered freelancers have the lightest requirements — both sections are optional. Partnerships require the partnership agreement instead of GEWA, as the agreement serves as the primary legal document. All other legal forms require both store photos and GEWA.

### Current pain points

Currently, sales agents collect these documents outside the flow (email, WhatsApp, manual handoff to compliance). This leads to:
- Delays in underwriting (documents arrive days later)
- Lost or incomplete uploads
- No structured way to attach documents to the application
- Compliance ops manually chasing documents
- Manual data entry from documents into the application (error-prone, slow)

> **Follow-up**: LLM-powered document parsing & application pre-fill is planned as a separate ticket. See `LLM_DOCUMENT_EXTRACTION.md`.

### Where it fits in the flow

This screen is part of the **sales-assisted POS terminal onboarding flow**, used by field agents on iPads. It sits between the business details collection step and the final submission/handoff step.

```
[Business Details] → [Document Upload (this ticket)] → [QR/Link Handoff to Client]
```

### Prototype

**Live clickable prototype**: https://sales-flow-draft.vercel.app/document-upload/  
**Source code**: https://github.com/mfetisov-cmd/sales-flow-draft/tree/main/document-upload

---

## User Stories

### US-1: Upload store photos
**As a** POS terminal sales agent  
**I want to** upload photos of the client's store location  
**So that** underwriting can verify the merchant's physical presence without requesting photos separately

**Acceptance Criteria:**
- [ ] Agent can upload one or more images (.jpg, .jpeg, .png, .heic) or PDFs
- [ ] Upload supports both tap-to-browse (opens device gallery/camera) and drag-and-drop
- [ ] Each uploaded file shows a progress indicator during upload
- [ ] Successfully uploaded files show a green checkmark with file name and "Uploaded" status
- [ ] Failed uploads show an error state with a "retry" option
- [ ] Agent can delete individual uploaded files
- [ ] Agent can add more photos after initial upload via "+ Add more photos" link
- [ ] Maximum file size: 10 MB per file
- [ ] For **non-reg freelancers**: section is shown but marked as optional (agent can skip)
- [ ] For **all other business types**: at least one photo required before "Save & Continue"

### US-2: Upload Gewerbeanmeldung (GEWA)
**As a** POS terminal sales agent  
**I want to** upload the client's Gewerbeanmeldung document  
**So that** compliance can verify the business registration without a separate document request

**Acceptance Criteria:**
- [ ] Agent can upload a GEWA document (.pdf, .jpg, .jpeg, .png, .heic)
- [ ] Same upload UX as store photos (progress, success, error, retry, delete)
- [ ] Maximum file size: 10 MB per file
- [ ] Single document upload (no multi-file needed for GEWA section)
- [ ] For **non-reg freelancers**: section is shown but marked as optional
- [ ] For **partnerships**: section is hidden (replaced by US-2b: Partnership Agreement)
- [ ] For **all other business types**: GEWA is required

### US-2b: Upload Partnership Agreement
**As a** POS terminal sales agent onboarding a **partnership** (GbR, OHG, KG)  
**I want to** upload the client's partnership agreement  
**So that** compliance can verify the partnership structure and ownership without a separate document request

**Acceptance Criteria:**
- [ ] Section is shown **only for partnership** business type (replaces the GEWA section)
- [ ] Agent can upload the agreement (.pdf, .jpg, .jpeg, .png, .heic)
- [ ] Same upload UX as other sections (progress, success, error, retry, delete)
- [ ] Maximum file size: 10 MB per file
- [ ] Partnership agreement is **required** — agent cannot proceed without it

### US-3: Add optional comments
**As a** POS terminal sales agent  
**I want to** add an optional comment to each upload section  
**So that** I can provide context to compliance (e.g., "photos taken on location visit", "document is in German")

**Acceptance Criteria:**
- [ ] Each section (Store Photos + GEWA) has its own "+ Add an optional comment on the file" toggle
- [ ] Clicking the toggle reveals a text area
- [ ] Comment is optional — agent can proceed without it
- [ ] Comment is attached to the respective section (not to individual files)

### US-4: Forward documents to compliance
**As a** system  
**When** the sales agent saves uploaded documents  
**I want** each document to be sent to compliance with the correct document type  
**So that** compliance ops can review them as part of the underwriting process

**Acceptance Criteria:**
- [ ] On "Save & Continue", uploaded files are forwarded to the compliance system
- [ ] Each file is tagged with the correct **document type** so compliance knows what they're reviewing:

| Upload Section | Document Type (compliance system) | Notes |
|----------------|----------------------------------|-------|
| Store / location photos | TBD — *new document type to be created* | May need a new type like `STORE_PHOTOS` or `MERCHANT_LOCATION_EVIDENCE` |
| Gewerbeanmeldung | `BUSINESS_REGISTRATION_CERTIFICATE` | Existing type |
| Partnership agreement | TBD — *check if existing type covers this, or create new* | Possibly `PARTNERSHIP_AGREEMENT` or an existing legal document type |

- [ ] Documents are linked to the `LEGAL_ENTITY` so compliance can find them in their review tooling
- [ ] Optional comments (if provided) are attached as metadata to the document submission
- [ ] Compliance agents can see the uploaded documents in their existing review interface (backoffice / ops tooling)

> **Action needed**: Confirm with compliance ops team which document types exist today and whether new types need to be registered in the system.

### US-5: Save & Continue
**As a** POS terminal sales agent  
**I want to** save the uploaded documents and continue to the next step  
**So that** the documents are attached to the application and I can proceed with the flow

**Acceptance Criteria:**
- [ ] "Save & Continue" validation depends on business type:
  - **Non-reg freelancer**: Button always enabled (all uploads optional)
  - **Partnership**: Disabled until store photos (≥1) AND partnership agreement are uploaded
  - **All other types**: Disabled until store photos (≥1) AND GEWA are uploaded
- [ ] On tap, button shows a loading state (spinner)
- [ ] On success, button briefly shows "Saved!" with a checkmark, then navigates to the next step
- [ ] On failure, show an error toast with a retry option
- [ ] Back button (←) returns to the previous step without losing uploaded files

---

## Functional Requirements

### File handling

| Requirement | Detail |
|-------------|--------|
| Supported formats | `.jpg`, `.jpeg`, `.png`, `.heic`, `.pdf` |
| Max file size | 10 MB per file |
| Max files per section | No hard limit (reasonable UX: suggest up to 10 for store photos) |
| Upload method | Device gallery picker, camera (on iPad), drag & drop |
| Storage | Files attached to the POS terminal application record |
| Compression | Optional: client-side image compression before upload for large photos |

### Upload states

| State | Visual | Behavior |
|-------|--------|----------|
| **Idle** | Dashed border upload zone with upload icon, "Drag and drop here or click to upload" | Tap opens file picker |
| **Uploading** | Purple spinner + progress bar + "Uploading... X%" | Non-blocking — agent can continue adding more files |
| **Success** | Green checkmark circle + file name + "Uploaded" | File persisted on backend |
| **Error** | Red X circle + file name + "Upload failed — retry" | Tap "retry" re-initiates upload |

### Navigation

| Element | Behavior |
|---------|----------|
| **← Back button** | Returns to previous step (Business Details). Draft uploads should be preserved if possible. |
| **Save & Continue** | Submits all uploaded files + optional comments. Validation rules depend on business type (see US-4). |

---

## Design Specifications

- **Layout**: iPad-native, max-width 720px centered, white card on light gray background
- **Typography**: System font (-apple-system), title 32px bold, section labels 13px uppercase gray
- **Upload zone**: Dashed border (#D1D5DB), 16px border-radius, centered content, tall padding (64px vertical)
- **Accent color**: Vivid purple (#8B5CF6) for hover states, action links, progress bars
- **Success color**: Green (#10B981) for checkmarks and success states
- **Error color**: Red (#EF4444) for error states
- **Bottom bar**: Round back button (52px circle, gray) + full-width purple pill CTA button (50px border-radius)
- **Follows existing Vivid sales flow patterns** — consistent with QR screen, business details screen

See prototype for reference: https://sales-flow-draft.vercel.app/document-upload/

---

## Technical Considerations

### Backend requirements
- [ ] API endpoint to upload files (multipart/form-data) linked to a POS terminal application
- [ ] File storage (S3 or equivalent) with reference saved to application record
- [ ] Support for associating metadata: section type (`store_photos` / `gewa` / `partnership_agreement`), optional comment text, document type for compliance
- [ ] File validation: type check, size limit (10MB), virus scan

### Frontend requirements
- [ ] iPad WebView compatible (Safari/WebKit)
- [ ] Camera access permission handling (for direct photo capture)
- [ ] Client-side file type and size validation before upload
- [ ] Resumable uploads or retry logic for unreliable field connectivity
- [ ] Offline-first consideration: queue uploads if connection drops, sync when reconnected

### Integration points
- [ ] Link uploaded documents to `APPLICATION_SME` or `LEGAL_ENTITY` in the data model
- [ ] Forward uploaded documents to compliance system with correct document type tag (see US-4)
- [ ] Uploaded documents visible in compliance ops tooling (backoffice) for underwriting review
- [ ] Document status reflected in CTA system (e.g., CTA for "review store photos" auto-created after upload)
- [ ] New document types registered in compliance system if needed (TBD — coordinate with compliance ops)

---

## Out of Scope

- **LLM document parsing & application pre-fill** — follow-up ticket (see `LLM_DOCUMENT_EXTRACTION.md`)
- Photo quality validation (blur detection, lighting check)
- Document expiry tracking
- Multi-language document support (GEWA is always German)
- Editing/cropping photos within the flow

---

## Open Questions

1. ~~**Should store photos be required or optional?**~~ → Resolved: depends on business type (see requirements matrix above).
2. **GEWA for non-German markets?** — Equivalent documents exist (e.g., Extrait K-bis in France). Should we make the document section configurable per market?
3. **File retention policy** — How long do we keep uploaded documents? GDPR implications?
4. **Offline upload queue** — Field agents may have spotty connectivity. Do we need offline-first upload with sync?
5. **Document types for compliance** — Which document types exist in the compliance system today? Do we need to register new types for store photos, GEWA, and partnership agreements? → Check with compliance ops.
6. **Partnership sub-types** — Do GbR, OHG, and KG all have the same document requirements, or do some need additional documents?

---

## Dependencies

| Dependency | Team | Status |
|------------|------|--------|
| File upload API endpoint | Backend | TBD |
| S3 storage setup for application documents | Platform / Infra | TBD |
| Compliance ops tooling to review uploaded docs | Compliance Ops | TBD |
| CTA auto-creation for document review | Backend / Compliance | TBD |
| iPad WebView camera permissions | Mobile | TBD |
| Business type passed from previous step (for conditional logic) | Frontend / Backend | TBD |
| Document types registered in compliance system | Compliance Ops / Backend | TBD |
| Compliance backoffice supports viewing new document types | Compliance Ops | TBD |

---

## Definition of Done

- [ ] Screen adapts to business type: shows correct sections with correct required/optional labels
- [ ] Store photos upload works on iPad (gallery + camera)
- [ ] GEWA upload works on iPad (for non-partnership types)
- [ ] Partnership agreement upload works on iPad (for partnerships only)
- [ ] Progress, success, and error states render correctly
- [ ] Optional comments can be added per section
- [ ] "Save & Continue" validation follows business-type rules
- [ ] On save, documents are forwarded to compliance with correct document type tags
- [ ] Documents are linked to the application / legal entity
- [ ] Uploaded documents visible in compliance ops review interface (backoffice)
- [ ] Tested on iPad Safari / WebView with real file sizes
- [ ] Edge cases handled: large files rejected with message, network errors show retry
- [ ] Non-reg freelancer can skip all uploads and proceed

---

## Follow-up Ticket

**LLM Document Parsing & Application Pre-fill** — see `LLM_DOCUMENT_EXTRACTION.md`  
Automatically extract fields from uploaded GEWA / partnership agreements and pre-fill the application. Blocked by this ticket.
