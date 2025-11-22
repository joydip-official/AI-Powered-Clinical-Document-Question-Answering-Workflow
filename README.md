# AI-Powered Clinical Document Question-Answering Workflow

**Technologies:** n8n • Azure Cognitive Vision • Azure Cognitive Search • Microsoft SQL Server

## Overview

This project automates the extraction, interpretation, and question-answering of clinical documents using a fully orchestrated workflow built in n8n.

Given a patient ID, document ID, file name, and a clinician's question, the system:

- Retrieves the correct PDF from secure storage
- Extracts content via Azure Cognitive Vision
- Retrieves context via Azure Cognitive Search
- Uses an AI Agent to generate a medically accurate answer
- Writes the result back into Microsoft SQL Server

This replaces manual chart review with a scalable, auditable automation pipeline.

---

## Architecture

### Input
- Patient ID
- Document ID
- Document filename
- Question to be answered

### Processing
- SQL metadata retrieval
- Secure document download
- OCR and content extraction (Azure Cognitive Vision)
- Context retrieval (Azure Cognitive Search)
- AI reasoning using Azure OpenAI
- Data merging and transformation
- SQL insert/update operations

### Output
- Final answer stored in SQL
- Processing job marked complete
- Execution fully logged and auditable

---

## Workflow Diagram

<img width="1833" height="724" alt="Screenshot 2025-11-22 020008" src="https://github.com/user-attachments/assets/9959beb0-8b07-4e07-9063-e45dbbfc274d" />


---

## Detailed Workflow Description

### 1. Trigger
A scheduled trigger initiates the workflow regularly to detect newly added clinician questions in SQL.

### 2. SQL Metadata Retrieval
Two SQL nodes retrieve:
- Patient details
- Document details
- Storage path
- Original clinician question

This determines which file must be processed.

### 3. Secure File Retrieval
- An **HTTP Request** node downloads the PDF from secure storage
- A **Download** node converts the binary file for OCR processing

### 4. OCR Using Azure Cognitive Vision
Azure Cognitive Vision performs:
- Full-text extraction
- Layout analysis
- Key/value extraction
- Table detection

The output is well-structured text suitable for analysis and search.

### 5. Context Retrieval Using Azure Cognitive Search
Azure Cognitive Search retrieves the most relevant text fragments related to the question.

This improves accuracy and reduces noise before sending data to the AI agent.

### 6. AI Agent Processing
The Azure OpenAI agent receives:
- OCR text
- Search-retrieved fragments
- Patient and document metadata
- The clinician's question

The model generates a structured clinical answer.

### 7. Data Merge and Post-Processing
A **JavaScript** node merges:
- SQL metadata
- AI output
- File metadata

The final JSON is formatted according to the SQL schema.

### 8. SQL Insert and Status Update
Two SQL nodes:
- Insert the generated answer
- Update the processing status (e.g., `processed = true`)

This ensures traceability and data integrity.

---

## Example Output

```json
{
  "patient_id": "12345",
  "doc_id": "67890",
  "question": "What is the final pathology impression?",
  "answer": "High-grade non–muscle invasive bladder cancer. No muscularis propria invasion identified.",
  "source_file": "pathology_report_2024_01_15.pdf",
  "confidence": "0.92"
}
```

---

## Business Impact

- ✅ Reduces manual document review time
- ✅ Faster clinician access to actionable information
- ✅ Improved consistency and accuracy
- ✅ Fully auditable workflow runs
- ✅ Scalable for high-volume clinical environments

---

## Technology Stack

| Component | Purpose |
|-----------|---------|
| **n8n** | Workflow orchestration |
| **Azure Cognitive Vision** | OCR and extraction |
| **Azure Cognitive Search** | Context retrieval |
| **Azure OpenAI** | AI reasoning |
| **Microsoft SQL Server** | Metadata and output storage |
| **HTTP API** | Secure file access |
| **JavaScript** | Data transformation |

---

## Security Considerations

- ✅ All data transfers use HTTPS
- ✅ No documents are stored within n8n beyond execution scope
- ✅ SQL access is least-privilege and scoped
- ✅ Designed to support clinical data governance and compliance

---

## Setup Instructions

### Prerequisites
- n8n instance (self-hosted or cloud)
- Azure Cognitive Services account
- Azure OpenAI access
- Microsoft SQL Server
- Secure document storage endpoint

### Configuration Steps
1. Import the n8n workflow JSON
2. Configure Azure Cognitive Vision credentials
3. Configure Azure Cognitive Search credentials
4. Configure Azure OpenAI credentials
5. Set up SQL Server connection
6. Configure document storage endpoint
7. Test with sample patient data

---

## Usage

1. Insert a new question record into the SQL questions table
2. The scheduled trigger will pick up the new question
3. The workflow automatically processes the document
4. The answer is written back to SQL
5. Check the execution log for audit trail
