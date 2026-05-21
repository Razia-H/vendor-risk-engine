╔══════════════════════════════════════════════════════════════════════════════════════════╗
║           ENTERPRISE VENDOR COMPLIANCE & RISK ONBOARDING ENGINE                        ║
║                     End-to-End System Architecture                                      ║
╚══════════════════════════════════════════════════════════════════════════════════════════╝

  ┌─────────────────────┐
  │   INGESTION LAYER   │
  │  ─────────────────  │
  │  Google Drive Folder│
  │  (Vendor PDF Drop)  │
  └────────┬────────────┘
           │  TRIGGER: New file detected (Watch Files module)
           ▼
  ┌─────────────────────┐
  │   EXTRACTION LAYER  │
  │  ─────────────────  │
  │  Make.com           │
  │  PDF Text Parser    │
  │  (HTTP + Base64     │
  │   or Google Doc     │
  │   Convert module)   │
  └────────┬────────────┘
           │  OUTPUT: raw_text_string (plain text, ~3,000–8,000 tokens)
           ▼
  ┌─────────────────────┐
  │  CONTEXT PACKAGER   │
  │  ─────────────────  │
  │  Make.com           │
  │  Set Variables:     │
  │  · vendor_name      │
  │  · doc_type         │
  │  · raw_text         │
  │  · timestamp        │
  └────────┬────────────┘
           │  OUTPUT: Shared Context Object (JSON)
           ▼
╔══════════╧══════════════════════════════════════════════════════════╗
║                     PARALLEL ROUTER (Make.com)                      ║
║          Split into 3 simultaneous independent agent paths          ║
╠══════════╦══════════════════════════╦══════════════════════════════╣
║ ROUTE 1  ║        ROUTE 2           ║           ROUTE 3            ║
║          ║                          ║                              ║
║  ┌─────┐ ║  ┌──────────────────┐   ║  ┌──────────────────────┐   ║
║  │HTTP │ ║  │       HTTP       │   ║  │         HTTP         │   ║
║  │POST │ ║  │       POST       │   ║  │         POST         │   ║
║  └──┬──┘ ║  └────────┬─────────┘   ║  └──────────┬───────────┘   ║
║     │    ║           │             ║             │               ║
║  AGENT 1 ║        AGENT 2          ║           AGENT 3           ║
║  SecOps  ║  Regulatory/Compliance  ║  Legal & Liability Counsel  ║
║  Analyst ║       Auditor           ║                             ║
║          ║                          ║                              ║
║  OUTPUT  ║        OUTPUT            ║          OUTPUT             ║
║  JSON    ║         JSON             ║           JSON              ║
║  secops_ ║     compliance_          ║         legal_              ║
║  result  ║       result             ║         result              ║
╚══════════╩══════════════════════════╩══════════════════════════════╝
           │              │                        │
           └──────────────┴────────────────────────┘
                                  │
                    AGGREGATION: Make.com Array Aggregator
                    (Waits for ALL 3 routes to complete)
                                  │
                                  ▼
                    ┌─────────────────────────┐
                    │  AGENT 4: ORCHESTRATOR  │
                    │  ─────────────────────  │
                    │  Executive Synthesis &  │
                    │  Scoring Engine         │
                    │                         │
                    │  · Consolidates 3 JSON  │
                    │    agent outputs        │
                    │  · Calculates Risk      │
                    │    Score (0–100)        │
                    │  · Writes 2-sentence    │
                    │    Executive Summary    │
                    │  · Assigns RISK TIER:   │
                    │    🟢 LOW / 🟡 MED /    │
                    │    🔴 HIGH / ⛔ CRITICAL │
                    └────────────┬────────────┘
                                 │  OUTPUT: final_synthesis JSON
                                 ▼
                    ┌─────────────────────────┐
                    │  GOOGLE SHEETS WRITER   │
                    │  ─────────────────────  │
                    │  Map JSON fields →      │
                    │  Sheet columns          │
                    │  (Add Row module)       │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │  HUMAN-IN-THE-LOOP      │
                    │  APPROVAL WORKFLOW      │
                    │  ─────────────────────  │
                    │  · Risk Score ≥ 70:     │
                    │    Auto-flag for review │
                    │  · Slack/Email Alert    │
                    │    sent to Risk Officer │
                    │  · Google Sheet status  │
                    │    column set to        │
                    │    "PENDING_REVIEW"     │
                    │  · Human updates col    │
                    │    to "APPROVED" or     │
                    │    "REJECTED"           │
                    └─────────────────────────┘

                    ╔═══════════════════════════════════════════════════════════════════════════╗
║           DATA MAPPING SCHEMATIC — Make.com Node-to-Node Flow            ║
╚═══════════════════════════════════════════════════════════════════════════╝

NODE 1: Google Drive — Watch Files
─────────────────────────────────────────────────────────────────────
  EMITS ──►  {
               "id": "{{1.id}}",
               "name": "{{1.name}}",            ← filename (e.g. "Acme_Corp_MSA.pdf")
               "mimeType": "{{1.mimeType}}",
               "webContentLink": "{{1.webContentLink}}"
             }

NODE 2: Google Drive — Download File (Binary)
─────────────────────────────────────────────────────────────────────
  RECEIVES ◄── {{1.id}}
  EMITS ──►  {
               "data": "{{2.data}}"             ← raw binary blob
             }

NODE 3: Tools — Base64 Encode → HTTP POST to PDF Parser API
─────────────────────────────────────────────────────────────────────
  RECEIVES ◄── {{2.data}}
  EMITS ──►  {
               "raw_text": "{{3.text}}"         ← extracted plaintext string
             }

NODE 4: Set Variables (Context Package)
─────────────────────────────────────────────────────────────────────
  ASSEMBLES ──►  {
                   "vendor_name":  "{{1.name}}",       ← from Node 1
                   "doc_type":     "MSA",               ← parsed from filename
                   "raw_text":     "{{3.text}}",        ← from Node 3
                   "timestamp":    "{{now}}",
                   "run_id":       "{{uuid()}}"
                 }

NODE 5A/5B/5C: HTTP — POST to Anthropic API (3x parallel)
─────────────────────────────────────────────────────────────────────
  EACH ROUTE SENDS:
  {
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1024,
    "system": "<AGENT_SYSTEM_PROMPT>",
    "messages": [
      {
        "role": "user",
        "content": "VENDOR: {{4.vendor_name}}\nDOC_TYPE: {{4.doc_type}}\nDOCUMENT:\n{{4.raw_text}}"
      }
    ]
  }

  EACH ROUTE RECEIVES:
  {
    "content": [
      {
        "type": "text",
        "text": "{\"risk_domain\":\"...\", \"findings\":[...], \"domain_score\":...}"
      }
    ]
  }

  PARSED OUTPUT per route:
  ┌──────────────────────────────────────────────────────┐
  │ Route 1 → secops_result   = {{5a.content[].text}}   │
  │ Route 2 → compliance_result = {{5b.content[].text}} │
  │ Route 3 → legal_result    = {{5c.content[].text}}   │
  └──────────────────────────────────────────────────────┘

NODE 6: Array Aggregator (Wait for all 3 routes)
─────────────────────────────────────────────────────────────────────
  COLLECTS ──►  [
                  {{secops_result}},
                  {{compliance_result}},
                  {{legal_result}}
                ]
  EMITS ──►    "aggregated_analyses": "<stringified JSON array>"

NODE 7: HTTP — POST to Anthropic API (Agent 4 — Orchestrator)
─────────────────────────────────────────────────────────────────────
  SENDS ──►  {
               "model": "claude-sonnet-4-20250514",
               "max_tokens": 1024,
               "system": "<ORCHESTRATOR_SYSTEM_PROMPT>",
               "messages": [
                 {
                   "role": "user",
                   "content": "{{6.aggregated_analyses}}"
                 }
               ]
             }

  RECEIVES ──►  {
                  "vendor_name": "Acme Corp",
                  "final_risk_score": 74,
                  "risk_tier": "HIGH",
                  "executive_summary": "...",
                  "approval_status": "PENDING_REVIEW",
                  "domain_scores": {
                    "secops": 68,
                    "compliance": 79,
                    "legal": 72
                  },
                  "top_critical_findings": ["...", "...", "..."]
                }

NODE 8: Google Sheets — Add Row
─────────────────────────────────────────────────────────────────────
  MAPS ──►
  ┌─────────────────────┬─────────────────────────────────────────┐
  │ SHEET COLUMN        │ MAKE.COM TOKEN                          │
  ├─────────────────────┼─────────────────────────────────────────┤
  │ Run ID              │ {{7.run_id}}                            │
  │ Timestamp           │ {{7.timestamp}}                         │
  │ Vendor Name         │ {{7.vendor_name}}                       │
  │ Document Type       │ {{4.doc_type}}                          │
  │ Final Risk Score    │ {{7.final_risk_score}}                  │
  │ Risk Tier           │ {{7.risk_tier}}                         │
  │ SecOps Score        │ {{7.domain_scores.secops}}              │
  │ Compliance Score    │ {{7.domain_scores.compliance}}          │
  │ Legal Score         │ {{7.domain_scores.legal}}               │
  │ Executive Summary   │ {{7.executive_summary}}                 │
  │ Critical Findings   │ {{7.top_critical_findings}}             │
  │ Approval Status     │ {{7.approval_status}}                   │
  │ Reviewed By         │ [BLANK — human fills in]                │
  │ Review Date         │ [BLANK — human fills in]                │
  │ Final Decision      │ [BLANK — human fills in]                │
  └─────────────────────┴─────────────────────────────────────────┘
