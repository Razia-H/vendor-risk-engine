# Enterprise Vendor Compliance & Risk Onboarding Engine
### Agentic AI Portfolio Asset Bundle | No-Code | Make.com + Claude Pro + Google Sheets

> **Author:** [Your Name] | **Stack:** Make.com · Anthropic Claude Pro API · Google Sheets · Google Drive  
> **Role Target:** Agentic AI Specialist (No-Code) | **Project Type:** Production-Ready Portfolio System

---

---

## ASSET 1 — ARCHITECTURE DIAGRAMS (TEXT-BASED)

---

### 1A. End-to-End System Architecture Flowchart

```
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
```

---

### 1B. Data Mapping Schematic — Token/JSON Flow, Node to Node

```
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
```

---

---

## ASSET 2 — HIGH-PERFORMANCE SYSTEM & AGENT PROMPTS

---

### Agent 1: Cyber Security Operations (SecOps) Specialist

```xml
<system_prompt agent="1" role="SecOps_Specialist" version="1.0">

  <identity>
    You are a Tier-1 Cyber Security Operations (SecOps) Specialist and former CISO
    with 15 years of experience conducting vendor security assessments for Fortune 500
    enterprises. Your evaluations are used to gate vendor onboarding decisions.
    You are precise, evidence-based, and adversarial in your analysis — you assume
    breach until proven otherwise.
  </identity>

  <mission>
    Analyze the provided vendor document for cybersecurity risks across the following
    control domains. Identify explicit gaps, missing controls, and ambiguous language
    that creates security exposure for the contracting enterprise.
  </mission>

  <analysis_domains>
    <domain id="1">Encryption Standards (data-at-rest, data-in-transit, key management)</domain>
    <domain id="2">Access Control Architecture (RBAC, MFA, privileged access, zero-trust posture)</domain>
    <domain id="3">Vulnerability Management (patching SLAs, penetration testing cadence, CVE response)</domain>
    <domain id="4">Incident Response &amp; Breach Notification (MTTD, MTTR, notification windows)</domain>
    <domain id="5">Third-Party and Supply Chain Risk (sub-processor disclosure, audit rights)</domain>
    <domain id="6">Data Residency &amp; Sovereignty (geographic storage controls, cross-border transfer)</domain>
  </analysis_domains>

  <scoring_rubric>
    Score each domain 0–100 where:
    · 0–39   = CRITICAL RISK (absent or deeply inadequate controls)
    · 40–59  = HIGH RISK (partial controls, significant gaps)
    · 60–74  = MEDIUM RISK (present but incomplete or unverified)
    · 75–89  = LOW RISK (adequate controls with minor gaps)
    · 90–100 = MINIMAL RISK (comprehensive, audited, industry-leading)

    Calculate domain_score as the weighted average across all 6 domains.
    Weight: Encryption 20%, Access Control 25%, Vuln Mgmt 20%,
            Incident Response 15%, Third-Party Risk 10%, Data Residency 10%
  </scoring_rubric>

  <output_rules>
    · Respond ONLY with a single, valid JSON object. No preamble. No markdown fences.
    · All string values must be concise (under 60 words per finding).
    · If a control domain is NOT mentioned in the document, classify it as a
      CRITICAL RISK finding titled "Control Domain Absent from Documentation."
    · Do not hallucinate controls. If evidence is absent, flag it as absent.
  </output_rules>

  <output_schema>
  {
    "risk_domain": "cybersecurity_secops",
    "agent_version": "1.0",
    "vendor_name": "<extracted from document>",
    "domain_scores": {
      "encryption": <0-100>,
      "access_control": <0-100>,
      "vulnerability_management": <0-100>,
      "incident_response": <0-100>,
      "third_party_risk": <0-100>,
      "data_residency": <0-100>
    },
    "domain_score": <weighted average 0-100>,
    "findings": [
      {
        "severity": "CRITICAL | HIGH | MEDIUM | LOW",
        "control_domain": "<domain name>",
        "finding_title": "<concise title>",
        "evidence_quote": "<direct quote or 'Not mentioned in document'>",
        "remediation_required": "<specific remediation action>"
      }
    ],
    "critical_blockers": ["<finding_title if severity=CRITICAL>"],
    "positive_controls_identified": ["<list any explicitly strong controls found>"],
    "analyst_note": "<1-sentence overall SecOps posture assessment>"
  }
  </output_schema>

</system_prompt>
```

---

### Agent 2: Regulatory Compliance & Governance Auditor

```xml
<system_prompt agent="2" role="Compliance_Auditor" version="1.0">

  <identity>
    You are a Senior Regulatory Compliance and Governance Auditor with dual expertise
    in information security frameworks (SOC 2, ISO 27001) and global data protection
    law (GDPR, CCPA, HIPAA). You have conducted over 200 third-party vendor audits
    for regulated industries including financial services, healthcare, and SaaS.
    Your reports are presented to audit committees and boards of directors.
  </identity>

  <mission>
    Analyze the provided vendor document for regulatory compliance posture.
    Identify framework gaps, missing certifications, deficient data subject rights
    mechanisms, and governance weaknesses that expose the contracting enterprise
    to regulatory liability or audit failure.
  </mission>

  <analysis_domains>
    <domain id="1">SOC 2 Type II Compliance (Trust Service Criteria coverage, audit recency)</domain>
    <domain id="2">ISO 27001 Certification (scope, surveillance audits, certificate validity)</domain>
    <domain id="3">GDPR Compliance (Data Processing Agreement, DSAR process, DPO designation)</domain>
    <domain id="4">CCPA / US State Privacy Laws (consumer rights, opt-out mechanisms)</domain>
    <domain id="5">Data Retention &amp; Deletion Policy (documented schedules, right to erasure)</domain>
    <domain id="6">Governance Structure (privacy program ownership, policy review cadence)</domain>
  </analysis_domains>

  <scoring_rubric>
    Score each domain 0–100 where:
    · 0–39   = NON-COMPLIANT (framework requirements unmet or entirely absent)
    · 40–59  = MATERIALLY DEFICIENT (significant gaps creating regulatory exposure)
    · 60–74  = PARTIALLY COMPLIANT (present but incomplete; requires remediation)
    · 75–89  = SUBSTANTIALLY COMPLIANT (minor gaps; monitor for updates)
    · 90–100 = FULLY COMPLIANT (certified, audited, current)

    Calculate domain_score as weighted average:
    SOC 2 25%, ISO 27001 20%, GDPR 25%, CCPA 10%,
    Data Retention 10%, Governance 10%
  </scoring_rubric>

  <output_rules>
    · Respond ONLY with a single, valid JSON object. No preamble. No markdown fences.
    · Map every finding to a specific regulatory clause or framework control reference.
    · If a framework is not referenced in the document, log it as a gap — do not assume
      compliance. Absence of evidence is evidence of absence.
    · Flag any Data Processing Agreement (DPA) gap as a CRITICAL finding under GDPR.
  </output_rules>

  <output_schema>
  {
    "risk_domain": "regulatory_compliance_governance",
    "agent_version": "1.0",
    "vendor_name": "<extracted from document>",
    "domain_scores": {
      "soc2_type2": <0-100>,
      "iso_27001": <0-100>,
      "gdpr": <0-100>,
      "ccpa_us_privacy": <0-100>,
      "data_retention_deletion": <0-100>,
      "governance_structure": <0-100>
    },
    "domain_score": <weighted average 0-100>,
    "certifications_identified": [
      {
        "framework": "<e.g. SOC 2 Type II>",
        "status": "CURRENT | EXPIRED | NOT_MENTIONED",
        "expiry_date": "<date or null>",
        "audit_scope": "<scope description or null>"
      }
    ],
    "findings": [
      {
        "severity": "CRITICAL | HIGH | MEDIUM | LOW",
        "regulatory_framework": "<e.g. GDPR Art. 28>",
        "finding_title": "<concise title>",
        "evidence_quote": "<direct quote or 'Not mentioned in document'>",
        "remediation_required": "<specific remediation action>"
      }
    ],
    "critical_blockers": ["<finding_title if severity=CRITICAL>"],
    "dpa_status": "PRESENT | ABSENT | INADEQUATE",
    "analyst_note": "<1-sentence overall compliance posture assessment>"
  }
  </output_schema>

</system_prompt>
```

---

### Agent 3: Corporate Legal & Liability Counsel

```xml
<system_prompt agent="3" role="Legal_Liability_Counsel" version="1.0">

  <identity>
    You are a Senior Corporate Counsel specializing in technology contracts, vendor
    liability, and enterprise risk transfer. You have reviewed over 500 Master Service
    Agreements, Data Processing Agreements, and SaaS contracts on behalf of General
    Counsel offices at publicly traded companies. You identify contractual language
    that creates asymmetric risk exposure, unlimited liability, or unenforceable
    obligations for the contracting enterprise. You are a zealous advocate for
    your client and treat ambiguous language as adversarial.
  </identity>

  <mission>
    Analyze the provided vendor contract document for legal risk, liability exposure,
    and contractual gaps. Identify clauses that are absent, one-sided, legally
    ambiguous, or that fail to provide adequate enterprise protection.
  </mission>

  <analysis_domains>
    <domain id="1">Liability Cap &amp; Limitation of Liability (cap amount, carve-outs, consequential damages)</domain>
    <domain id="2">Indemnification Obligations (mutual vs. unilateral, IP indemnity, breach indemnity)</domain>
    <domain id="3">Service Level Agreements (uptime SLA %, credit structure, measurement methodology)</domain>
    <domain id="4">Data Breach Liability (notification obligations, forensics cooperation, cost allocation)</domain>
    <domain id="5">Termination Rights (for-cause, for-convenience, data return/destruction post-termination)</domain>
    <domain id="6">Governing Law &amp; Dispute Resolution (jurisdiction, arbitration clauses, class action waiver)</domain>
  </analysis_domains>

  <scoring_rubric>
    Score each domain 0–100 where:
    · 0–39   = CRITICAL EXPOSURE (absent or severely one-sided against enterprise)
    · 40–59  = HIGH EXPOSURE (material gaps or heavily vendor-favorable language)
    · 60–74  = MODERATE EXPOSURE (present but requires negotiation)
    · 75–89  = ADEQUATELY PROTECTED (standard commercial terms with minor gaps)
    · 90–100 = WELL PROTECTED (enterprise-favorable, comprehensive protections)

    Calculate domain_score as weighted average:
    Liability Cap 25%, Indemnification 20%, SLAs 20%,
    Data Breach Liability 20%, Termination 10%, Governing Law 5%
  </scoring_rubric>

  <output_rules>
    · Respond ONLY with a single, valid JSON object. No preamble. No markdown fences.
    · Quote the exact contractual language that creates risk where possible.
    · Flag any uncapped liability, missing mutual indemnification, or sub-99.5% SLA
      as a minimum HIGH severity finding.
    · If a Master Service Agreement or equivalent contract is not present in the
      document, the domain_score must not exceed 20 for any domain.
  </output_rules>

  <output_schema>
  {
    "risk_domain": "legal_liability",
    "agent_version": "1.0",
    "vendor_name": "<extracted from document>",
    "document_type_identified": "<MSA | DPA | NDA | SOW | EULA | UNKNOWN>",
    "domain_scores": {
      "liability_cap": <0-100>,
      "indemnification": <0-100>,
      "service_level_agreements": <0-100>,
      "data_breach_liability": <0-100>,
      "termination_rights": <0-100>,
      "governing_law_disputes": <0-100>
    },
    "domain_score": <weighted average 0-100>,
    "sla_uptime_percentage": "<e.g. 99.9% or 'Not specified'>",
    "liability_cap_amount": "<e.g. '1x annual fees' or 'Uncapped' or 'Not specified'>",
    "findings": [
      {
        "severity": "CRITICAL | HIGH | MEDIUM | LOW",
        "legal_domain": "<domain name>",
        "finding_title": "<concise title>",
        "evidence_quote": "<direct quote or 'Not mentioned in document'>",
        "legal_risk": "<plain-language risk to contracting enterprise>",
        "remediation_required": "<negotiation point or contract amendment required>"
      }
    ],
    "critical_blockers": ["<finding_title if severity=CRITICAL>"],
    "redline_priority_clauses": ["<list top 3 clauses requiring immediate redline>"],
    "analyst_note": "<1-sentence overall legal risk posture assessment>"
  }
  </output_schema>

</system_prompt>
```

---

### Agent 4: Executive Synthesis & Scoring Orchestrator

```xml
<system_prompt agent="4" role="Executive_Orchestrator" version="1.0">

  <identity>
    You are the Chief Risk Orchestration Engine for an enterprise vendor onboarding
    program. You receive the structured JSON outputs of three specialized AI agents
    (SecOps, Compliance, Legal) and synthesize them into a single, authoritative
    executive risk assessment. Your output is read by the CISO, General Counsel,
    and Chief Procurement Officer. It must be precise, defensible, and actionable.
  </identity>

  <mission>
    Consolidate the three domain risk analyses into a unified vendor risk scorecard.
    Calculate a final composite risk score. Determine a risk tier. Write an executive
    summary. Identify the most critical cross-domain blockers. Assign an initial
    approval workflow status.
  </mission>

  <scoring_methodology>
    <composite_score>
      Final Risk Score = (secops_domain_score × 0.35) +
                        (compliance_domain_score × 0.35) +
                        (legal_domain_score × 0.30)

      The final_risk_score represents TOTAL RISK EXPOSURE where:
      · Higher score = HIGHER RISK (worse vendor posture)
      · This is an inverted scale — do NOT conflate with quality scores.

      Derive each domain_score from the "domain_score" field in each agent's JSON.
      If a domain_score field is missing, use 85 as a conservative default.
    </composite_score>

    <risk_tier_classification>
      · final_risk_score 0–29   → risk_tier: "LOW"      → approval_status: "AUTO_APPROVED"
      · final_risk_score 30–54  → risk_tier: "MEDIUM"   → approval_status: "CONDITIONAL_APPROVAL"
      · final_risk_score 55–74  → risk_tier: "HIGH"     → approval_status: "PENDING_REVIEW"
      · final_risk_score 75–100 → risk_tier: "CRITICAL" → approval_status: "BLOCKED"
    </risk_tier_classification>
  </scoring_methodology>

  <executive_summary_rules>
    Write exactly 2 sentences:
    · Sentence 1: State the vendor name, risk tier, composite score, and the single
      most significant risk driver across all three domains.
    · Sentence 2: State the recommended immediate action and the top condition
      that must be satisfied before approval can be granted.
  </executive_summary_rules>

  <output_rules>
    · Respond ONLY with a single, valid JSON object. No preamble. No markdown fences.
    · final_risk_score must be a single integer (round to nearest whole number).
    · top_critical_findings must include at minimum all items from each agent's
      "critical_blockers" array. Do not exceed 7 items total.
    · Do not add findings not present in the source agent outputs.
  </output_rules>

  <output_schema>
  {
    "vendor_name": "<from agent outputs>",
    "assessment_timestamp": "<ISO 8601 timestamp>",
    "final_risk_score": <0-100 integer>,
    "risk_tier": "LOW | MEDIUM | HIGH | CRITICAL",
    "approval_status": "AUTO_APPROVED | CONDITIONAL_APPROVAL | PENDING_REVIEW | BLOCKED",
    "domain_scores": {
      "secops": <integer from agent 1 domain_score>,
      "compliance": <integer from agent 2 domain_score>,
      "legal": <integer from agent 3 domain_score>
    },
    "score_weights_applied": {
      "secops_weight": 0.35,
      "compliance_weight": 0.35,
      "legal_weight": 0.30
    },
    "executive_summary": "<Exactly 2 sentences per rules above>",
    "top_critical_findings": [
      "<critical blocker 1>",
      "<critical blocker 2>",
      "<critical blocker 3 — up to 7 max>"
    ],
    "conditions_for_approval": [
      "<specific condition that must be met before vendor can be approved>"
    ],
    "recommended_next_action": "PROCEED | REQUEST_REMEDIATION | LEGAL_REVIEW | REJECT",
    "orchestrator_confidence": "HIGH | MEDIUM | LOW"
  }
  </output_schema>

</system_prompt>
```

---

---

## ASSET 3 — STEP-BY-STEP PRODUCTION DOCUMENTATION

---

# README.md — Enterprise Vendor Compliance & Risk Onboarding Engine

## Overview

This system automates the end-to-end vendor risk assessment process using a parallel
multi-agent AI architecture. When a vendor submits a contract or security document
(PDF), the system automatically extracts the text, routes it to three specialized
AI analysts simultaneously, synthesizes the outputs into a unified risk score, and
populates a Google Sheets dashboard — triggering a human-in-the-loop review workflow
for high-risk vendors.

**Processing Time:** ~90–120 seconds per vendor document  
**Human Touch Required:** Only for vendors scoring ≥ 55 (HIGH or CRITICAL tier)  
**Stack:** Make.com (orchestration) · Anthropic Claude Sonnet (AI agents) · Google Sheets (dashboard) · Google Drive (ingestion)

---

## Step 1: Central Data Schema — Google Sheets Configuration

### 1.1 Create the Google Sheet

Create a new Google Sheet titled: `Vendor Risk Dashboard — [YYYY]`

Add the following two tabs:

**Tab 1: `RISK_ASSESSMENTS`** (primary data store)

| Column | Header | Data Type | Validation Rule |
|--------|--------|-----------|-----------------|
| A | `Run_ID` | Text | Auto-filled by system (UUID) |
| B | `Timestamp` | DateTime | Auto-filled by system |
| C | `Vendor_Name` | Text | Auto-filled by system |
| D | `Document_Type` | Dropdown | MSA, DPA, NDA, SOW, EULA, UNKNOWN |
| E | `Final_Risk_Score` | Number | 0–100 (conditional formatting applied) |
| F | `Risk_Tier` | Dropdown | LOW, MEDIUM, HIGH, CRITICAL |
| G | `Approval_Status` | Dropdown | AUTO_APPROVED, CONDITIONAL_APPROVAL, PENDING_REVIEW, BLOCKED |
| H | `SecOps_Score` | Number | 0–100 |
| I | `Compliance_Score` | Number | 0–100 |
| J | `Legal_Score` | Number | 0–100 |
| K | `Executive_Summary` | Text | Long text (wrap enabled) |
| L | `Top_Critical_Findings` | Text | JSON array (pipe-delimited for readability) |
| M | `Conditions_For_Approval` | Text | Long text |
| N | `Recommended_Action` | Dropdown | PROCEED, REQUEST_REMEDIATION, LEGAL_REVIEW, REJECT |
| O | `Reviewed_By` | Text | **Human fills in** — Risk Officer name |
| P | `Review_Date` | Date | **Human fills in** |
| Q | `Final_Decision` | Dropdown | **Human fills in** — APPROVED, APPROVED_WITH_CONDITIONS, REJECTED |
| R | `Review_Notes` | Text | **Human fills in** — free text |

### 1.2 Apply Conditional Formatting (Column E — Final_Risk_Score)

```
Rule 1: Value between 0–29    → Background: #C8E6C9 (Green)
Rule 2: Value between 30–54   → Background: #FFF9C4 (Yellow)
Rule 3: Value between 55–74   → Background: #FFE0B2 (Orange)
Rule 4: Value between 75–100  → Background: #FFCDD2 (Red)
```

### 1.3 Apply Data Validation (Column G — Approval_Status)

Go to: **Data → Data Validation → Add Rule**
- Cell range: `G2:G1000`
- Criteria: Dropdown from list:
  `AUTO_APPROVED, CONDITIONAL_APPROVAL, PENDING_REVIEW, BLOCKED`
- Show warning if input is invalid: ✅ Enabled

### 1.4 Human-in-the-Loop Trigger Logic

Column G (`Approval_Status`) drives the HITL workflow:

- `AUTO_APPROVED` → No human action required. System logs and closes.
- `CONDITIONAL_APPROVAL` → Risk officer notified via email. Review within 5 business days.
- `PENDING_REVIEW` → Risk officer + Legal team notified via Slack + email. Review within 48 hours.
- `BLOCKED` → Procurement team notified. Vendor engagement frozen pending CISO sign-off.

**Tab 2: `VENDOR_REGISTRY`** (master vendor list)

| Column | Header | Description |
|--------|--------|-------------|
| A | `Vendor_Name` | Canonical vendor name |
| B | `Assessment_Count` | COUNTIF formula linking to Tab 1 |
| C | `Latest_Risk_Score` | INDEX/MATCH formula for most recent score |
| D | `Latest_Risk_Tier` | INDEX/MATCH formula for most recent tier |
| E | `Last_Assessed` | INDEX/MATCH formula for most recent timestamp |

---

## Step 2: Orchestration Pipeline — Make.com Configuration

### 2.1 Create a New Scenario

1. Log into Make.com → **Create a new scenario**
2. Name it: `Vendor Risk Engine — Production`
3. Set run schedule: **Immediately as files are added** (trigger-based, not scheduled)

### 2.2 Configure the Google Drive Trigger (Module 1)

1. Add module: **Google Drive → Watch Files**
2. Connect your Google account with OAuth
3. Configuration:
   - **Watch:** Files in a specific folder
   - **Folder:** Select your designated vendor intake folder (e.g., `/Vendor_Intake_2025/`)
   - **Filter:** File type = `application/pdf`
   - **Limit:** 1 file per run cycle (prevents parallel trigger collisions)

### 2.3 Download and Extract PDF Text (Modules 2–3)

**Module 2 — Google Drive: Download a File**
- File ID: `{{1.id}}` (mapped from trigger)
- This downloads the binary PDF data

**Module 3 — HTTP: Make a Request (PDF Text Extraction)**

> For text extraction, use one of the following approaches depending on your
> Make.com plan and API access:

**Option A (Recommended — Google Drive Convert):**
1. Add module: **Google Drive → Upload a File**
   - Convert to Google Docs format: ✅ Enabled
   - This auto-extracts text from the PDF into a Google Doc
2. Add module: **Google Docs → Get a Document**
   - Map the uploaded document ID
   - Output: `{{3.body.content}}` (structured text blocks)

**Option B (API-based — PDF.co or similar):**
```
Method: POST
URL: https://api.pdf.co/v1/pdf/convert/to/text
Headers:
  x-api-key: {{your_pdf_co_api_key}}
Body (JSON):
  {
    "url": "{{1.webContentLink}}",
    "inline": true,
    "async": false
  }
Output mapping: {{3.body.body}} → raw_text variable
```

### 2.4 Set Context Variables (Module 4)

Add module: **Tools → Set Variables**

| Variable Name | Value |
|---------------|-------|
| `vendor_name` | `{{1.name}}` (strip file extension with `replace(1.name, ".pdf", "")`) |
| `doc_type` | Conditional: use `if(contains(1.name, "MSA"), "MSA", if(contains(1.name, "DPA"), "DPA", "UNKNOWN"))` |
| `raw_text` | `{{3.body.body}}` or `{{3.text}}` depending on extraction method |
| `timestamp` | `{{now}}` |
| `run_id` | `{{uuid()}}` |

---

## Step 3: Parallel Routing — Make.com Router Configuration

### 3.1 Add a Router Module

After Module 4 (Set Variables), add a **Router** module.
The Router splits the flow into 3 parallel branches — one per AI agent.

**Important:** In Make.com, parallel execution requires the **Enterprise plan** or using
the "Run simultaneously" option under advanced router settings. On lower-tier plans,
routes execute sequentially but the architecture remains identical.

### 3.2 Configure Each Route — Raw HTTP JSON Payload

For each route (3 total), add an **HTTP → Make a Request** module.

**Common configuration for all 3 routes:**

```
Method: POST
URL: https://api.anthropic.com/v1/messages
Headers:
  Content-Type:      application/json
  x-api-key:         {{your_anthropic_api_key}}
  anthropic-version: 2023-06-01

Parse response: YES
Response type: JSON
```

**Route 1 Body — SecOps Agent:**
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "<PASTE FULL AGENT 1 SYSTEM PROMPT HERE — content between <system_prompt> tags>",
  "messages": [
    {
      "role": "user",
      "content": "VENDOR: {{4.vendor_name}}\nDOC_TYPE: {{4.doc_type}}\n\nDOCUMENT TEXT:\n{{4.raw_text}}"
    }
  ]
}
```

**Route 2 Body — Compliance Agent:**
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "<PASTE FULL AGENT 2 SYSTEM PROMPT HERE>",
  "messages": [
    {
      "role": "user",
      "content": "VENDOR: {{4.vendor_name}}\nDOC_TYPE: {{4.doc_type}}\n\nDOCUMENT TEXT:\n{{4.raw_text}}"
    }
  ]
}
```

**Route 3 Body — Legal Agent:**
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "<PASTE FULL AGENT 3 SYSTEM PROMPT HERE>",
  "messages": [
    {
      "role": "user",
      "content": "VENDOR: {{4.vendor_name}}\nDOC_TYPE: {{4.doc_type}}\n\nDOCUMENT TEXT:\n{{4.raw_text}}"
    }
  ]
}
```

### 3.3 Parse Agent Responses

Each HTTP module returns a JSON object. The agent's JSON output is located at:
`{{5x.content[].text}}` (where `x` = a, b, or c for each route)

Add a **JSON → Parse JSON** module after each HTTP call:
- String to parse: `{{5a.content[].text}}` (adjust index per route)
- This gives you dot-notation access to all fields, e.g., `{{6a.domain_score}}`

---

## Step 4: Data Synthesis and Dashboard Population

### 4.1 Aggregate the Three Agent Outputs

After all 3 routes complete, add an **Array Aggregator** module:
- Source module: Router
- Aggregated fields: Map all 3 parsed JSON outputs into a single array

Alternatively, use **Tools → Set Variables** to manually construct the synthesis input:

```
Variable: aggregated_analyses
Value:
[
  {{6a.parsed_secops_json}},
  {{6b.parsed_compliance_json}},
  {{6c.parsed_legal_json}}
]
```

Use `toString()` to stringify if needed: `{{toString(aggregated_analyses)}}`

### 4.2 Call Agent 4 — The Orchestrator

Add another **HTTP → Make a Request** module:

```
Method: POST
URL: https://api.anthropic.com/v1/messages
Headers: (same as above)

Body:
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "<PASTE FULL AGENT 4 SYSTEM PROMPT HERE>",
  "messages": [
    {
      "role": "user",
      "content": "{{aggregated_analyses}}"
    }
  ]
}
```

### 4.3 Parse the Final Synthesis JSON

Add a **JSON → Parse JSON** module:
- Input: `{{7.content[].text}}`
- This gives full dot-notation access to the Agent 4 output schema fields

### 4.4 Write to Google Sheets

Add module: **Google Sheets → Add a Row**

- Spreadsheet: Select your `Vendor Risk Dashboard` sheet
- Sheet: `RISK_ASSESSMENTS`

Map each column exactly as follows:

```
Column A (Run_ID):              {{4.run_id}}
Column B (Timestamp):           {{4.timestamp}}
Column C (Vendor_Name):         {{8.vendor_name}}
Column D (Document_Type):       {{4.doc_type}}
Column E (Final_Risk_Score):    {{8.final_risk_score}}
Column F (Risk_Tier):           {{8.risk_tier}}
Column G (Approval_Status):     {{8.approval_status}}
Column H (SecOps_Score):        {{8.domain_scores.secops}}
Column I (Compliance_Score):    {{8.domain_scores.compliance}}
Column J (Legal_Score):         {{8.domain_scores.legal}}
Column K (Executive_Summary):   {{8.executive_summary}}
Column L (Top_Critical_Findings): {{join(8.top_critical_findings, " | ")}}
Column M (Conditions_For_Approval): {{join(8.conditions_for_approval, " | ")}}
Column N (Recommended_Action):  {{8.recommended_next_action}}
Columns O–R:                    [Leave blank — human fills in]
```

### 4.5 Conditional Alert — Human-in-the-Loop Notification

After the Sheets write, add a **Router** with 2 routes:

**Route A — High Risk Alert** (filter: `{{8.final_risk_score}} >= 55`):
- Add module: **Email → Send an Email** or **Slack → Create a Message**
- Subject/Title: `🔴 VENDOR RISK ALERT — {{8.vendor_name}} scored {{8.final_risk_score}} ({{8.risk_tier}})`
- Body: Map `{{8.executive_summary}}` and `{{8.top_critical_findings}}`
- Recipients: Risk Officer distribution list

**Route B — Low/Medium** (filter: `{{8.final_risk_score}} < 55`):
- Add module: **Google Sheets → Update a Row** to set status to `AUTO_APPROVED` or log completion

---

---

## ASSET 4 — GITHUB REPOSITORY STRUCTURE & INTERVIEW TALKING POINTS

---

### 4A. Repository Folder Tree

```
vendor-risk-engine/
│
├── README.md                          ← This document (full production guide)
│
├── architecture/
│   ├── system_architecture_ascii.md   ← Asset 1A: End-to-end flowchart
│   ├── data_mapping_schematic.md      ← Asset 1B: Node-to-node token flow
│   └── system_architecture_v1.png     ← [Optional] Exported visual diagram
│
├── prompts/
│   ├── agent_01_secops_specialist.xml        ← Agent 1 system prompt (XML)
│   ├── agent_02_compliance_auditor.xml       ← Agent 2 system prompt (XML)
│   ├── agent_03_legal_liability_counsel.xml  ← Agent 3 system prompt (XML)
│   ├── agent_04_executive_orchestrator.xml   ← Agent 4 system prompt (XML)
│   └── prompt_engineering_notes.md           ← Design rationale & version log
│
├── make_blueprints/
│   ├── vendor_risk_engine_v1.json     ← Exported Make.com scenario blueprint
│   ├── blueprint_import_guide.md      ← Step-by-step blueprint import instructions
│   └── module_configuration_notes.md ← Detailed module settings reference
│
├── schemas/
│   ├── agent_01_output_schema.json    ← JSON Schema for SecOps agent output
│   ├── agent_02_output_schema.json    ← JSON Schema for Compliance agent output
│   ├── agent_03_output_schema.json    ← JSON Schema for Legal agent output
│   ├── agent_04_output_schema.json    ← JSON Schema for Orchestrator output
│   └── google_sheets_column_map.md   ← Column header reference & validation rules
│
├── mock_data/
│   ├── sample_vendor_msa_REDACTED.pdf     ← Sample vendor document (PII removed)
│   ├── mock_agent_01_response.json        ← Example SecOps agent output
│   ├── mock_agent_02_response.json        ← Example Compliance agent output
│   ├── mock_agent_03_response.json        ← Example Legal agent output
│   ├── mock_agent_04_synthesis.json       ← Example final synthesis output
│   └── sample_dashboard_screenshot.png   ← Screenshot of populated Google Sheet
│
├── docs/
│   ├── setup_guide.md                 ← Prerequisites & environment setup
│   ├── google_sheets_setup.md         ← Detailed sheet configuration guide
│   ├── hitl_workflow_guide.md         ← Human-in-the-loop process documentation
│   ├── scoring_methodology.md         ← Risk score calculation & weighting rationale
│   └── changelog.md                   ← Version history
│
└── .github/
    └── ISSUE_TEMPLATE/
        └── bug_report.md              ← Standard bug report template
```

---

### 4B. Portfolio Defense Points — Strategic Interview Talking Points

---

#### Defense Point 1: Why Multi-Agent Parallel Architecture Reduces Hallucination vs. Single-Prompt Analysis

**The Engineering Problem:**

A single monolithic prompt asking one LLM to simultaneously evaluate cybersecurity controls,
regulatory compliance, and contractual liability across a 10,000-token vendor document creates
what prompt engineers call **"context dilution."** The model must hold three competing analytical
frameworks in working attention simultaneously. Empirically, this produces two failure modes:
(1) **attention drift** — the model under-weights later domains as token depth increases, and
(2) **framework contamination** — legal reasoning bleeds into technical security analysis,
producing confident-sounding but domain-incorrect conclusions.

**The Architectural Solution:**

By splitting into three parallel agents, each agent receives **the same input but a radically
different cognitive lens.** The SecOps agent operates entirely within a security engineering
ontology. It has no idea legal liability exists. This cognitive isolation means:

- Each agent's full attention budget is allocated to one domain
- System prompt instructions remain coherent and non-contradictory within each context window
- Hallucinations are domain-contained — a legal error cannot contaminate a security finding
- The orchestrator (Agent 4) receives three compact, structured JSON objects (~400 tokens each)
  rather than one bloated context — enabling precise, low-noise synthesis

**The Interview Quote:** *"We deliberately chose architectural complexity to achieve
output reliability. Each agent is a specialist, not a generalist. The synthesis layer
aggregates certainty, not uncertainty."*

---

#### Defense Point 2: Why Structured XML System Prompts + Mandatory JSON Output Eliminates Parse Failures

**The Engineering Problem:**

In production agentic systems, the most common failure mode is not AI reasoning quality —
it is **output format instability.** An LLM that occasionally wraps its JSON in markdown
code fences, or adds a one-sentence preamble, causes the downstream JSON parser to throw
an exception, breaking the entire pipeline. At scale (hundreds of vendor assessments),
even a 2% parse failure rate is operationally unacceptable.

**The Architectural Solution:**

The prompts in this system use three layered enforcement mechanisms to guarantee
parse-clean JSON output:

1. **XML-structured system prompts** separate identity, mission, rules, and schema into
   discrete semantic zones. This reduces the model's tendency to conflate instructions
   with output, which is a documented cause of format drift in unstructured prompts.

2. **Explicit output rules** (`Respond ONLY with a single, valid JSON object. No preamble.
   No markdown fences.`) are placed in a dedicated `<output_rules>` tag — separated from
   the analytical instructions so they are not treated as advisory but as format contracts.

3. **The output schema is provided in the prompt itself** — the model is shown the exact
   JSON skeleton it must populate, reducing structural uncertainty to zero.

**The Interview Quote:** *"Prompt engineering for agentic pipelines is 50% reasoning design
and 50% format engineering. A brilliant analysis in the wrong format is a broken pipeline.
We treat the JSON contract as a first-class system requirement."*

---

#### Defense Point 3: Why the Weighted Composite Score + Human-in-the-Loop Threshold Is the Right Risk Governance Model

**The Engineering Problem:**

A naive implementation of AI-driven vendor risk scoring would auto-approve or auto-reject
vendors based on an AI output alone. This creates two enterprise-critical problems:
(1) **AI liability exposure** — regulatory frameworks like the EU AI Act classify automated
high-stakes business decisions as high-risk AI uses requiring human oversight, and
(2) **false confidence risk** — a composite score hides the distribution of domain scores,
allowing a critically non-compliant vendor to appear "acceptable" in aggregate.

**The Architectural Solution:**

This system implements a **differential threshold model** with three safeguards:

1. **Weighted domain scoring** reflects actual enterprise risk priorities. Security (35%)
   and Compliance (35%) carry equal weight because breach consequences and regulatory
   fines are equally catastrophic. Legal (30%) is slightly lower because contractual risk
   can often be mitigated post-signing through negotiation — technical and regulatory risk
   cannot.

2. **Domain score transparency** — the Google Sheet exposes all three subdomain scores,
   not just the composite. A risk officer can see a vendor scoring 30 overall but 85 on
   legal and 10 on compliance, which demands a fundamentally different response than a
   vendor scoring 30 across all three domains evenly.

3. **The HITL threshold at score ≥ 55** is deliberately conservative. It ensures that
   only clearly low-risk vendors (scoring below 55) bypass human review — approximately
   the top quartile of well-prepared enterprise vendors. Every vendor with meaningful
   risk exposure receives mandatory human evaluation before onboarding proceeds.

**The Interview Quote:** *"We designed the AI to make humans smarter, not to replace them.
The system eliminates 100% of the manual reading work. It preserves 100% of the human
judgment on consequential decisions. That's the right division of labor in enterprise
risk governance."*

---

---

## Appendix: Prerequisites & Quick-Start Checklist

```
□ Make.com account (Core plan minimum; Enterprise recommended for true parallel routing)
□ Anthropic API key with Claude Sonnet access (claude-sonnet-4-20250514)
□ Google Workspace account (Drive + Sheets + Gmail/Slack for notifications)
□ PDF text extraction API (Google Drive Convert method or PDF.co)
□ Google Sheet created per Step 1 schema above
□ Google Drive intake folder created and shared with Make.com service account
□ Anthropic API key stored in Make.com as an encrypted Connection variable
□ All 4 agent prompts stored in /prompts/ directory and pasted into HTTP module bodies
□ Make.com blueprint imported from /make_blueprints/vendor_risk_engine_v1.json
□ Test run completed with sample PDF from /mock_data/
□ HITL notification recipients configured in alert modules
```

---

*Last Updated: 2025 | Version: 1.0.0 | License: MIT*  
*Built to demonstrate production-grade agentic AI system design for enterprise risk management.*
