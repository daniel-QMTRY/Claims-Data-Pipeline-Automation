<h1 align="center">Claims Data Pipeline Automation 🚀💉</h1>
<p align="center"><strong>Vendor-agnostic pipeline for healthcare claims.</strong> Ingest <strong>X12 837/835</strong>, normalize to a clean schema, track <strong>999/277CA</strong> acks, and light up <strong>RCM KPIs</strong>—without vendor lock-in.</p>
<p align="center">Built by <a href="https://qmtry.ai"><strong>QMTRY.ai</strong></a></p>

<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.11%2B-3776AB?logo=python&logoColor=white">
  <img alt="Streamlit" src="https://img.shields.io/badge/Streamlit-Dashboard-FF4B4B?logo=streamlit&logoColor=white">
  <img alt="CI" src="https://img.shields.io/badge/GitHub%20Actions-CI-2088FF?logo=githubactions&logoColor=white">
  <img alt="License" src="https://img.shields.io/badge/License-MIT-2EA44F">
  <img alt="HIPAA aware" src="https://img.shields.io/badge/HIPAA-aware-0F766E">
</p>

---

## Highlights
- **End-to-end X12:** 837P/837I/835/999/277CA (+ hooks for 270/271 & 276/277)
- **Multi-clearinghouse:** Change Healthcare, Waystar, Availity — per-payer routing & failover
- **Canonical model:** `claims_header`, `claims_line`, `adjudication`, `payments`, `ack_*`
- **Denials intelligence:** CARC/RARC → root-cause groupings + ACK-SLA gap alerts
- **Dashboards:** FPCCR, denial rate, payer lag, A/R rollups (Streamlit starter)
- **Audit trail:** deterministic lineage from raw EDI → normalized rows (no PHI in repo)

---

## Quickstart
```bash
python -m venv .venv && source .venv/bin/activate    # Win: .venv\Scripts\activate
pip install -r requirements.txt
python pipelines/ingest_edi.py --inbox tests/sample_data --warehouse warehouse
python pipelines/build_marts.py --warehouse warehouse
streamlit run dashboards/rcm_app.py

---

## Parser stubs output demo rows. When ready, swap in pyx12 or Bots in parsers/ and update requirements.txt.

flowchart LR
  A["EHR/PM (e.g., Epic Resolute)"] --> B1["Change Healthcare"]
  A --> B2["Waystar"]
  A --> B3["Availity"]

  B1 --> C["Watchers (SFTP/AS2/API)"]
  B2 --> C
  B3 --> C

  C --> D["Validate ISA/GS/ST"]
  D --> E["ACK monitor 999/277CA"]
  E --> Z["Ops alerts"]

  D --> F["Parse 837"]
  D --> G["Parse 835"]
  F --> H["Map → canonical tables"]
  G --> H

  H --> I[("Raw / staging")]
  I --> J["Data quality rules"]
  J --> K[("Analytics marts (RCM & Stars)")]
  K --> L["Dashboards & SLAs"]

  H --> M["Denials intelligence (CARC/RARC)"]
  L --> N["Exports & workqueues"]

parsers/      # pyx12/Bots adapters (swappable)
pipelines/    # ingest_edi.py, build_marts.py, utils.py
dashboards/   # rcm_app.py (starter KPIs)
configs/      # pipeline.example.yml, CARC mappings
tests/        # tiny fake 837/835/ACKs (no PHI)


flowchart LR
  A["EHR/PM (e.g., Epic Resolute)"] --> B1["Change Healthcare"]
  A --> B2["Waystar"]
  A --> B3["Availity"]

  B1 --> C["Watchers (SFTP/AS2/API)"]
  B2 --> C
  B3 --> C

  C --> D["Validate ISA/GS/ST"]
  D --> E["ACK monitor 999/277CA"]
  E --> Z["Ops alerts"]

  D --> F["Parse 837"]
  D --> G["Parse 835"]
  F --> H["Map → canonical tables"]
  G --> H

  H --> I[("Raw / staging")]
  I --> J["Data quality rules"]
  J --> K[("Analytics marts (RCM & Stars)")]
  K --> L["Dashboards & SLAs"]

  H --> M["Denials intelligence (CARC/RARC)"]
  L --> N["Exports & workqueues"]

parsers/      # pyx12/Bots adapters (swappable)
pipelines/    # ingest_edi.py, build_marts.py, utils.py
dashboards/   # rcm_app.py (starter KPIs)
configs/      # pipeline.example.yml, CARC mappings
tests/        # tiny fake 837/835/ACKs (no PHI)

Configure & Comply

Copy .env.example → .env

Edit configs/pipeline.example.yml (sources, routing, ack_sla_hours, warehouse path)

Minimum-necessary PHI; secrets in env; PHI-free logs. See COMPLIANCE.md & SECURITY.md

License: MIT

Description (short):
Vendor-agnostic healthcare claims pipeline that ingests X12 837/835, normalizes to analytics tables, tracks 999/277CA, and powers RCM dashboards—HIPAA-aware, no PHI.

Extended description:
Claims Data Pipeline Automation by QMTRY.ai helps provider revenue-cycle teams ingest healthcare EDI (X12 837/835) from clearinghouses like Change Healthcare, Waystar, and Availity; validate envelopes and 999/277CA acknowledgments; and normalize claims into a clean canonical schema (claims_header, claims_line, adjudication, payments, ack_*). It includes a Streamlit dashboard for FPCCR, denial rate, payer lag, and A/R rollups, plus CARC/RARC-based denials intelligence. Multi-clearinghouse ready, vendor-agnostic, and HIPAA-aware (no PHI included)—ideal for provider RCM teams building resilient, audit-ready pipelines.
