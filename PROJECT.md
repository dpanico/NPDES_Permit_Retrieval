# NPDES Permit Retrieval and Summary Project

## Goal

Build a workflow that can retrieve Illinois EPA Document Explorer permit records by permit or bureau identifier, download available permit PDFs from DocuWare where possible, extract searchable text, and summarize permit requirements.

## Initial Target

- Search site: https://webapps.illinois.gov/EPA/DocumentExplorer/Attributes
- Test permit ID: `IL0021130`
- Facility found: `Bloomingdale, Village of`
- Facility record ID: `170002077366`
- Available document category: `NPDES Permit`
- Document count: `1`
- Total pages: `19`
- Originating bureau: `Bureau of Water`
- DocuWare result metadata observed:
  - Bureau ID: `W0430100001`
  - Site name: `BLOOMINGDALE REEVES WRF`
  - Item date: `07/18/2019`
  - Permit ID: `IL0021130`
  - Comment: `MOD 12/27/2018`

## Proposed Workflow

1. Search Illinois EPA Document Explorer by permit or bureau ID.
2. Resolve the facility record and document category page.
3. Identify available document categories, counts, page totals, and originating bureau.
4. Open or automate the DocuWare document result.
5. Download the permit as PDF when the viewer exposes the download action.
6. Extract embedded PDF text.
7. Run OCR when the PDF is scanned or text extraction is incomplete.
8. Search and classify relevant sections.
9. Generate a structured permit summary with page references.

## Desired Summary Output

The summary should cover:

- Effluent limits by outfall.
- Effluent monitoring requirements, including parameter, limit, units, sample frequency, and sample type.
- Influent monitoring requirements, if present.
- Other outfall monitoring requirements.
- Special conditions.
- Compliance schedules or reporting requirements, if present.
- Source page references for each extracted requirement.

## Suggested Structured Data Model

Each extracted monitoring row should preserve traceability:

```json
{
  "permit_id": "IL0021130",
  "facility": "BLOOMINGDALE REEVES WRF",
  "outfall": "001",
  "section": "effluent_limits",
  "parameter": "BOD5",
  "limit_type": "monthly_average",
  "limit_value": null,
  "units": null,
  "monitoring_frequency": null,
  "sample_type": null,
  "page": null,
  "source_text": null
}
```

## Technical Notes

- The Illinois EPA Explorer document page is simple HTML and exposes category-level document metadata.
- The actual document viewer is DocuWare and uses temporary integration/auth URLs rather than a stable public PDF URL.
- Manual download appears supported by the site through the DocuWare viewer's PDF download or print options.
- Automated download will likely require either browser automation or DocuWare Platform API-style calls after resolving the document result.
- OCR should be treated as a first-class fallback because many permit PDFs may be scanned images.

## Open Questions

- Can the DocuWare PDF download be automated reliably without credentials beyond the temporary public integration link?
- What OCR engine should we use locally or in deployment?
- How strict should table extraction be for permit limits versus generating narrative summaries?
- Should extracted summaries be stored as JSON, Markdown reports, or both?
