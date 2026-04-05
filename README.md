# N8N Automated PDF Form Filling Workflow

![Highlevel](images/highlevel.png)

## Overview

This N8N workflow automates the process of filling multiple PDF forms using data from Google Sheets and PDF.co API. What previously took hours of manual data entry now runs automatically — filling every form for every person in minutes, with zero human intervention.

---

## How It Saves Time

### Manual Process (Before)
- Open Google Sheet → copy person's data
- Open PDF form → paste data field by field
- Save filled PDF → rename it → upload to Google Drive
- Repeat for every form × every person

For **20 people × 20 forms = 400 manual operations** — easily a full day's work.

### Automated Process (After)
- Trigger the workflow once
- All 400 PDFs are filled, named, and uploaded automatically
- Total time: **minutes, not hours**

### Time Savings Breakdown

| Task | Manual | Automated |
|---|---|---|
| Fill 1 form for 1 person | ~5 mins | ~3 seconds |
| Fill 20 forms for 1 person | ~100 mins | ~60 seconds |
| Fill 20 forms for 20 people | ~2,000 mins | ~20 minutes |
| Human errors | Frequent | Zero |

---

## Real-World Use Cases

### 1. HR & Onboarding
New employees need to fill out offer letters, tax forms, benefits enrollment, emergency contact forms, and NDAs all at once. HR teams typically spend days manually filling these out for each new hire. This workflow pulls employee data from a Google Sheet and fills all onboarding documents in one run — reducing onboarding paperwork from days to minutes.

### 2. Healthcare & Patient Management
Clinics and hospitals require patients to complete intake forms, consent forms, insurance forms, and medical history documents. With this workflow, patient data entered once in a spreadsheet automatically populates all required forms before appointments, eliminating duplicate data entry by staff.

### 3. Legal & Contract Management
Law firms and legal departments deal with high volumes of contracts, affidavits, court filings, and agreements that share common fields like names, addresses, and dates. This workflow fills all related documents simultaneously from a single client record.

### 4. Education & Student Administration
Schools and universities process enrollment forms, scholarship applications, financial aid documents, and parental consent forms for hundreds of students. This workflow handles bulk processing across all forms for all students in a single execution.

### 5. Real Estate
Real estate agencies process lease agreements, property disclosure forms, mortgage applications, and inspection reports for each client. The workflow auto-fills all transaction documents the moment a deal is initiated.

### 6. Government & Compliance
Government agencies and compliance teams deal with regulatory filings, license applications, and audit documents that require the same data repeated across multiple forms. This workflow ensures consistency and eliminates transcription errors across all submissions.

### 7. Insurance
Insurance companies process claims, policy documents, beneficiary forms, and coverage agreements. This workflow fills all policy-related documents for each client automatically when a new policy is created.

---

## High-Level Challenges & How This Workflow Solves Them

### Challenge 1: Different Forms Have Different Fields
**Problem:** Each PDF form has a unique set of field names. Form A may need `full_name` while Form B uses `Name`. Sending wrong field names causes forms to be blank or errors to occur.

**Solution:** The Field Mapping tab in Google Sheets acts as a translation layer — mapping each PDF's internal field names to the corresponding Google Sheet column. The Code node dynamically builds the correct payload for each form, ensuring only relevant fields are sent.

---

### Challenge 2: Data Consistency and Human Error
**Problem:** Manual form filling leads to typos, copy-paste errors, missing fields, and inconsistent formatting across documents — especially when the same data appears on multiple forms.

**Solution:** Data is entered once in Google Sheets and flows automatically into every form. The Code node reads values programmatically, eliminating transcription errors entirely.

---

## Workflow Architecture

```
Google Sheets (People Data)
        ↓
Split in Batches (1 person at a time)
        ↓
Google Sheets (Field Mapping Tab)
        ↓
Code Node (Build API payload per form)
        ↓
HTTP Request → PDF.co (Fill PDF)
        ↓
HTTP Request → Download filled PDF
        ↓
Google Drive (Upload filled PDF)
```

---

## Prerequisites

- N8N (self-hosted)
- Google Sheets with people data and field mapping tab
- PDF.co account (free tier: 200 credits/month)
- Google Drive output folder
- PDFs uploaded to PDF.co file storage

---

## Field Mapping Tab Structure

| Form Token | PDF Field Name | Sheet Column |
|---|---|---|
| filetoken://abc123 | full_name | Name |
| filetoken://abc123 | email_address | Email |
| filetoken://xyz789 | taxpayer_name | Name |
| filetoken://xyz789 | phone | Phone Number |

Each row maps one PDF field to one Google Sheet column. Add as many rows as needed for each form.

---

## Notes

- PDF.co output URLs expire after 1 hour — the Download and Upload nodes must run immediately after the fill step
- Free plan supports up to 200 API credits/month — each form fill costs <> credit
- The workflow handles missing fields gracefully — if a sheet column has no value, that field is skipped