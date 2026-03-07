# 🤖 AI-Powered Resume Screening & Candidate Matching System

> An intelligent n8n automation workflow that uses AI to screen job applicants at scale — automatically comparing CVs against job descriptions and updating application statuses in real time.

---

## 📌 Overview

This project automates the resume screening process using **n8n**, **Google Drive**, **Google Sheets**, and an **LLM via OpenRouter**. Instead of manually reviewing hundreds of CVs, the system processes each applicant, fetches their CV and the assigned job description, feeds them to an AI model, and writes the result (`Accepted`, `Not Qualified`, or `Error`) back into the spreadsheet.

**999 applications processed. Zero manual reviews.**

---

## ✨ Features

- 📄 **Automated CV & Job Retrieval** — Downloads candidate CVs and job descriptions directly from Google Drive
- 🧠 **AI-Powered Matching** — Uses a strict LLM prompt via OpenRouter to evaluate candidate fit
- 🔁 **Batch Processing Loop** — Handles large applicant pools with a built-in loop and error resilience
- ✅ **Smart Filtering** — Skips already-processed rows, only evaluating pending applications
- 🛡️ **Error Handling** — Flags missing CVs or job files separately to avoid silent failures
- 📊 **Live Sheet Updates** — Writes results directly back to Google Sheets in real time

---

## 🔧 Tech Stack

| Tool | Role |
|------|------|
| [n8n](https://n8n.io) | Workflow automation engine |
| Google Sheets | Application database (input & output) |
| Google Drive | CV and job description storage |
| OpenRouter | LLM API gateway |
| AI Model (via OpenRouter) | Resume vs. job description evaluation |

---

## 🗂️ Workflow Architecture

```
Start
  └── Load Applications from Google Sheets
        └── Filter (skip already processed)
              └── Loop over each applicant
                    ├── Extract Google Drive IDs from URLs
                    ├── Download CV PDF (Google Drive)
                    ├── Download Job Description PDF (Google Drive)
                    ├── Quick validation checks
                    │     ├── [Missing CV] → Log Error to Sheet
                    │     └── [Missing Job] → Log Error to Sheet
                    ├── Extract text from both PDFs
                    ├── Merge CV + Job text
                    └── AI Evaluation (LLM Chain)
                          └── Write result to Google Sheets
                                → "Accepted" / "Not Qualified" / "Error"
```

---

## 🧠 AI Prompt Logic

The LLM is prompted to act as a **strict recruiter** with clear rules:

- Candidate is marked **`Accepted`** only if the CV clearly demonstrates most required skills and relevant experience
- Returns **`Not Qualified`** if key requirements are missing, partial, or unclear
- Returns **`Error`** only when CV or Job Description content is missing/unreadable
- Does **not** infer or assume skills that aren't explicitly stated in the CV

---

## 📊 Sample Results

| Status | Count |
|--------|-------|
| ✅ Accepted | 42 |
| ❌ Not Qualified | 157 |
| ⚠️ Error | 1 |
| ⏳ Pending | 799 |
| **Total** | **999** |

### Before Processing
| application_id | applicant_name | Status |
|---|---|---|
| 882169 | Donna Perez | *(empty)* |
| 338533 | Betty Lopez | *(empty)* |
| 364742 | Mark Davis | *(empty)* |

### After Processing
| application_id | applicant_name | Status |
|---|---|---|
| 882169 | Donna Perez | Not Qualified |
| 338533 | Betty Lopez | Not Qualified |
| 364742 | Mark Davis | Accepted ✅ |

---

## 🚀 How to Use

### Prerequisites
- [n8n](https://n8n.io) (self-hosted or cloud)
- Google account with access to Google Sheets and Google Drive
- OpenRouter API key

### Setup

1. **Import the workflow**
   - In n8n, go to **Workflows → Import**
   - Upload `AI-Powered_Resume_Screening___Candidate_Matching_System.json`

2. **Configure credentials**
   - Add your **Google Sheets OAuth2** credentials
   - Add your **Google Drive OAuth2** credentials
   - Add your **OpenRouter API** key to the LLM node

3. **Prepare your spreadsheet**
   - Your Google Sheet should have these columns:
     ```
     application_id | applicant_name | Applicant_cv | applicant_email | assigned_job_offer_id | Status
     ```
   - `Applicant_cv` and `assigned_job_offer_id` should be Google Drive shareable links

4. **Update the Sheet ID**
   - In the `Application Spreadsheet Deploy` node, point it to your own Google Sheet

5. **Run the workflow**
   - Click **Execute Workflow** to start processing all pending applicants

---

## 📁 Files in this Repo

| File | Description |
|------|-------------|
| `AI-Powered_Resume_Screening___Candidate_Matching_System.json` | n8n workflow export (import this into n8n) |
| `Applications_Before.xlsx` | Sample input — 999 applicants with empty Status column |
| `Applications_After.xlsx` | Sample output — Status column filled by the AI |

---

## 📝 License

MIT — feel free to use, adapt, and build on this workflow.

---

*Built with n8n + OpenRouter. Designed to save recruiters hours of manual CV screening.*
