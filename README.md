````markdown
# Claims Data Pipeline Automation 🚀💉

**Vendor-agnostic pipeline for healthcare claims.** Ingest **X12 837/835**, normalize to a clean schema, track **999/277CA** acks, and light up **RCM KPIs**—without vendor lock-in.  
Built by [QMTRY.ai](https://qmtry.ai) for revenue-cycle teams that need resilient, audit-ready flows in a multi-clearinghouse world.

> _Result: fewer manual touches, cleaner claims, faster cash, and dashboards your CFO actually opens._

---

## ✨ What You Get
- **End-to-end X12**: 837P/837I/835/999/277CA (+ hooks for 270/271 & 276/277)  
- **Multi-clearinghouse**: Change Healthcare, Waystar, Availity—per-payer routing & failover  
- **Canonical model**: `claims_header`, `claims_line`, `adjudication`, `payments`, `ack_*`  
- **Denials intelligence**: CARC/RARC → root-cause groupings + ACK SLA alerts  
- **Dashboards**: FPCCR, denial rate, payer lag, A/R rollups (Streamlit starter)  
- **Audit trail**: deterministic lineage from raw EDI → normalized rows (no PHI in repo)

---

## ⚡ Quickstart (Demo)
```bash
python -m venv .venv && source .venv/bin/activate    # Win: .venv\Scripts\activate
pip install -r requirements.txt
python pipelines/ingest_edi.py --inbox tests/sample_data --warehouse warehouse
python pipelines/build_marts.py --warehouse warehouse
streamlit run dashboards/rcm_app.py
````

> **Note:** Parser stubs output demo rows. When you’re ready, swap in `pyx12` or `Bots` in `parsers/` and update `requirements.txt`.

---

## 🗺️ Layout

```
parsers/      # pyx12/Bots adapters (swappable)
pipelines/    # ingest_edi.py, build_marts.py, utils.py
dashboards/   # rcm_app.py (starter KPIs)
configs/      # pipeline.example.yml, CARC mappings
tests/        # tiny fake 837/835/ACKs (no PHI)
```

---

## 🔧 Configure

* Copy `.env.example` → `.env`
* Edit `configs/pipeline.example.yml` (sources, routing, `ack_sla_hours`, warehouse path)

---

## 🧩 Elevator Pitch

“We cut manual touches, improved **FPCCR**, and reduced **payer lag** by inserting a deterministic parser + rules layer between EHR billing and multiple clearinghouses—feeding clean marts and SLA-backed dashboards.”

---

## 🧱 Architecture (Mermaid)

```mermaid
%%{init: {'theme':'dark','themeVariables': { 'primaryColor': '#0f172a', 'primaryTextColor': '#e5e7eb', 'lineColor': '#94a3b8', 'fontSize': '12px' }}}%%
flowchart LR
  subgraph Provider Systems
    EHR[EHR/PM (e.g., Epic Resolute HB/PB)\nUB-04 / CMS-1500 data]
  end
  subgraph Clearinghouse(s)
    CH1[Change Healthcare]
    CH2[Waystar]
    CH3[Availity]
  end
  subgraph Ingestion Layer
    W1[Watchers (SFTP/AS2/API)]
    V[Envelope validation (ISA/GS/ST)]
    ACK[ACK Monitor\n999 / 277CA]
  end
  subgraph Parsing & Normalization
    P1[Parse 837P/I/D]
    P2[Parse 835 (ERA)]
    MAP[Map → canonical tables]
  end
  subgraph Storage & Marts
    STG[(Raw/Staging)]
    DQ[Data Quality rules]
    MART[(Analytics Marts\nRCM KPIs + Stars inputs)]
  end
  subgraph Apps & Ops
    DEN[Denials Intelligence\n(CARC/RARC → root cause)]
    DB[Dashboards & SLAs]
    EXP[Exports & Workqueues]
    ALERT[Ops alerts\n(missed ACKs, payer lag)]
  end
  EHR -- 837 --> CH1 & CH2 & CH3
  CH1 & CH2 & CH3 -- 999/277CA/835 --> W1
  W1 --> V --> ACK
  ACK --> ALERT
  V --> P1 & P2 --> STG --> MAP --> DQ --> MART --> DB
  MAP --> DEN --> EXP
```

---

## 🔐 Compliance

HIPAA-aware scaffold. Use **minimum necessary** PHI, secrets via `.env`, PHI-free logs. See `COMPLIANCE.md` & `SECURITY.md`.
**License:** MIT

```

---

### Description (SEO, short)
Vendor-agnostic healthcare claims pipeline that ingests **X12 837/835**, normalizes to analytics-ready tables, tracks **999/277CA**, and powers **RCM dashboards**—HIPAA-aware, no PHI.

### Extended Description (SEO)
Claims Data Pipeline Automation by **QMTRY.ai** helps provider revenue-cycle teams ingest healthcare EDI (X12 837/835) from clearinghouses like Change Healthcare, Waystar, and Availity; validate envelopes and **999/277CA** acknowledgments; and normalize claims into a clean, canonical schema (`claims_header`, `claims_line`, `adjudication`, `payments`, `ack_*`). The project ships with a Streamlit dashboard for FPCCR, denial rate, payer lag, and A/R rollups plus CARC/RARC-based denials intelligence. It’s multi-clearinghouse ready, vendor-agnostic, and HIPAA-aware (no PHI included), making it ideal for medical groups and analytics teams seeking resilient, audit-ready pipelines and portfolio-grade case studies.
::contentReference[oaicite:0]{index=0}
```
