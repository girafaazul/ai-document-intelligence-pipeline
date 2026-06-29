# AI Document Intelligence Pipeline
### Automated document data extraction powered by Claude AI + n8n

---

## Overview

This pipeline automates the extraction of structured data from any business document — invoices, contracts, purchase orders, financial reports — and saves it directly to Google Sheets without any manual input.

Built for finance, legal, and logistics teams that process high volumes of documents and need reliable, structured data without manual data entry.

---

## The Problem It Solves

Most businesses receive dozens of documents per day in unstructured formats. Manually reading, extracting, and entering that data into spreadsheets or ERPs is:

- Time-consuming (15–30 min per document)
- Error-prone (human typos, missed fields)
- Unscalable (grows linearly with volume)

This pipeline reduces that to **under 10 seconds per document**, with zero human intervention.

---

## How It Works

```
Incoming Document (PDF/text via webhook)
        ↓
Payload Validation (document_type required)
        ↓
Claude AI Extraction (structured JSON output)
        ↓
JSON Parser + Data Enrichment
        ↓
Parse Validation (error handling)
        ↓
Google Sheets (auto-append row)
        ↓
Webhook Response (success / error with status)
```

---

## Fields Extracted Automatically

| Field | Description |
|---|---|
| `document_type` | Invoice, Contract, PO, Report |
| `document_number` | ID or reference number |
| `date` | Document issue date |
| `due_date` | Payment or delivery due date |
| `vendor_name` | Issuing company |
| `client_name` | Receiving company |
| `total_amount` | Final amount |
| `currency` | USD, EUR, BRL, etc. |
| `line_items` | Array of products/services with values |
| `payment_terms` | Net 30, immediate, etc. |
| `processed_at` | Pipeline execution timestamp |

---

## Tech Stack

| Tool | Role |
|---|---|
| **n8n** | Workflow orchestration |
| **Claude API (Anthropic)** | AI extraction engine |
| **Google Sheets API** | Structured data storage |
| **Webhook (HTTP)** | Document intake trigger |

---

## Workflow Architecture

![AI Document Intelligence Pipeline](./workflow-screenshot.png)

> 9-node workflow with full error handling — validation layer on intake, parse error detection, and typed HTTP response codes (200 / 400 / 422).

---

## Error Handling

The pipeline handles two failure points explicitly:

- **400 Bad Request** — payload missing required `document_type` field
- **422 Unprocessable Entity** — Claude returned a response that could not be parsed as valid JSON

Both return structured error responses with timestamps, making debugging straightforward in production environments.

---

## Example Payload (Webhook Input)

```json
{
  "document_type": "invoice",
  "document_text": "Invoice #2024-0891 | Issued: June 15 2026 | Due: July 15 2026 | From: Acme Corp | To: Globex Ltd | Item: Software License — $1,200.00 | Total: $1,200.00 USD | Terms: Net 30"
}
```

## Example Output (Google Sheets Row)

```json
{
  "document_type": "invoice",
  "document_number": "2024-0891",
  "date": "2026-06-15",
  "due_date": "2026-07-15",
  "vendor_name": "Acme Corp",
  "client_name": "Globex Ltd",
  "total_amount": 1200.00,
  "currency": "USD",
  "payment_terms": "Net 30",
  "processed_at": "2026-06-29T14:32:00.000Z"
}
```

---

## Use Cases

- **Accounting firms** — automate invoice intake and reconciliation
- **Legal teams** — extract key dates and parties from contracts
- **Logistics companies** — process purchase orders and delivery confirmations
- **E-commerce operations** — parse supplier invoices at scale

---

## Setup

1. Import `ai-document-intelligence.json` into your n8n instance
2. Add your **Anthropic API key** as an HTTP Header Auth credential (`x-api-key`)
3. Connect your **Google Sheets** credential and set your Sheet ID
4. Activate the webhook and send your first document

---

## About

Built by a freelance AI Automation Engineer specializing in n8n workflows, Claude API integrations, and business process automation.

Open to custom implementations — [contact via Upwork](#)
