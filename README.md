Claims Data Pipeline Automation

Vendor-agnostic ingestion, normalization, and analytics for 837/835 claims with clearinghouse acks, denials intelligence, and dashboard-ready marts.

Built by QMTRY.ai for provider Revenue Cycle teams that need resilient, audit-ready claims processing in a multi-clearinghouse world.

✨ What you get

End-to-end pipeline for X12 flows: 837P/837I/835/999/277CA (hooks for 270/271 and 276/277).

Multi-clearinghouse ready (Change Healthcare, Waystar, Availity, …) with per-payer routing & failover.

Canonical data model (claims_header, claims_line, adjudication, payments, ack_*) → analytics marts.

Denials intelligence (CARC/RARC → root-cause groupings) + ACK SLAs with gap alerts.

Streamlit dashboard starter: FPCCR, denial rate, payer lag, A/R rollups.

Audit trail from raw EDI → normalized rows via deterministic lineage hashes.

Portfolio-ready case study template to showcase results.

🧠 Elevator pitch (buyer-friendly)

“We reduced manual touches in claim intake and remit posting, improved First-Pass Clean Claim Rate, and cut payer lag by adding a deterministic parser + rules layer between EHR billing and multiple clearinghouses. Everything lands in analytics-ready marts with KPIs and SLA alarms.”

🏗️ Architecture
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
  V[Validation: ISA/GS/ST envelope]
  ACK[ACK Monitor\n999 / 277CA]
end

subgraph Parsing & Normalization
  P1[Parse X12 837P/I/D]
  P2[Parse 835 (ERA)]
  MAP[Map → Canonical tables:\nclaims_header • claims_line • adjudication • payments • ack_*]
end

subgraph Storage & Marts
  STG[(Raw/Staging)]
  DQ[Data Quality rules]
  MART[(Analytics Marts:\nRCM KPIs + Stars inputs)]
end

subgraph Apps & Ops
  DEN[Denials Intelligence\n(CARC/RARC → root cause)]
  DB[Dashboards & SLAs]
  EXP[Exports & Workqueues]
  ALERT[Ops alerts\n(missed 999/277CA, payer lag)]
end

EHR -- 837 --> CH1 & CH2 & CH3
CH1 & CH2 & CH3 -- 999/277CA/835 --> W1
W1 --> V --> ACK
ACK --> ALERT
V --> P1 & P2 --> STG --> MAP --> DQ --> MART --> DB
MAP --> DEN --> EXP

📦 Repository layout
.
├── dashboards/
│   └── rcm_app.py               # Streamlit starter
├── marts/
│   └── schemas/                 # Example DDLs
├── parsers/
│   ├── x12_pyx12_adapter.py     # parser stub (swap in pyx12)
│   └── x12_bots_adapter.py      # parser stub (swap in Bots)
├── pipelines/
│   ├── ingest_edi.py            # X12 → canonical JSONL
│   ├── build_marts.py           # KPI marts
│   └── utils.py                 # IO + lineage hashes
├── configs/
│   ├── pipeline.example.yml     # routing & SLA config
│   └── mappings/carc_groups.csv # CARC → root cause
├── tests/
│   ├── test_mapping.py
│   └── sample_data/             # fake 837/835/ACKs (no PHI)
├── .github/workflows/ci.yml
├── COMPLIANCE.md
├── SECURITY.md
├── requirements.txt
├── .env.example
└── README.md

🚀 Quickstart (local demo)
# 1) Create a virtual environment
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

# 2) Install minimal deps
pip install -r requirements.txt

# 3) Run the demo ETL on sample files (no PHI)
python pipelines/ingest_edi.py --inbox tests/sample_data --warehouse warehouse
python pipelines/build_marts.py --warehouse warehouse

# 4) Launch dashboard
streamlit run dashboards/rcm_app.py


Parser adapters: The stubs output realistic demo rows. When ready, enable a real translator (e.g., pyx12 or Bots) in parsers/ and update requirements.txt.

🔧 Configuration

Copy .env.example → .env and set secrets for SFTP/AS2/API when attaching to real endpoints.

Edit configs/pipeline.example.yml for:

sources (clearinghouse endpoints, folders for 837/835)

routing (default & payer overrides)

ack_sla_hours (trigger alerts if 999/277CA absent past SLA)

warehouse_dir (local path or object storage mount)

🧾 Canonical data model

Tables

Table	Purpose
claims_header	Claim header info: IDs, payer, type, DOS, POS, billed amount
claims_line	CPT/HCPCS/RevCode lines: units, billed/allowed/paid, patient
adjudication	CARC/RARC, group codes, status, remarks
payments	Check/EFT posting details, post dates, traces
ack_999	Functional acks (accepted/rejected, error codes)
ack_277ca	Claim acknowledgment with edit trails
kpi_daily	Aggregated metrics for dashboards & SLAs

All rows include _hash for lineage back to the raw EDI snapshot.

📊 KPIs & dashboards (starter)

FPCCR (First-Pass Clean Claim Rate)

Initial denial rate & top CARC drivers

Payer cycle time / lag

Total billed / allowed / paid

277CA rejection reasons (edit classes)

Auto-posting % from 835s

Open dashboards/rcm_app.py to extend visuals (Plotly/Streamlit).

🛡️ Resilience & observability

Multi-clearinghouse routing (primary/secondary, payer-level overrides)

ACK SLA monitors for 999/277CA; alert on gaps or elevated reject rates

Nightly reconciliation of 837 → 835 volumes by payer

Runbooks to pause submissions when an upstream vendor degrades

🧩 Integrations (examples)

EHR/PM: Epic Resolute HB/PB or any system producing standard X12 837

Clearinghouses: Change Healthcare, Waystar, Availity (SFTP/AS2/API)

Warehouses: Postgres, DuckDB, Snowflake, Delta Lake (via loader)

FHIR (optional): join payer ExplanationOfBenefit resources to enrich adjudication context

🧪 Testing & CI
pytest -q


Lightweight unit tests keep CI green by default.

GitHub Actions workflow (.github/workflows/ci.yml) sets up Python 3.11, installs deps, and runs tests.

🧱 Case study template (paste into your portfolio)

Use this to tell the story. Replace bracketed text with your results.

Client: [Multi-specialty provider group, ~XX locations]
Problem: High denial rate ([X]%), single-vendor risk, slow payer acks.
Approach:

Deterministic X12 parsing + canonical model

Dual-clearinghouse routing with ACK SLAs

KPI dashboard (FPCCR, payer lag, CARC drivers)
Results (90 days):

FPCCR ↑ [+X.X pp]

Initial denial rate ↓ [−Y.Y pp]

Payer lag ↓ [Z.Z days]

Manual touches ↓ [~W%] in remit posting
Screenshots: store PNG/GIFs in docs/case-study/

🧭 Roadmap

Production adapters for pyx12 / Bots / Spark-based parsers

Eligibility & status joins (270/271, 276/277)

Auto-appeal letter generation (human-in-the-loop)

Prior auth joins & payer benchmarking

🔐 Compliance & security

HIPAA BAAs with all processors; minimum necessary PHI

TLS in transit; KMS at rest; optional field-level tokenization

Role-based access; short-lived credentials; PHI-free logs

See COMPLIANCE.md and SECURITY.md for checklists and runbooks

No PHI is included in this repo. Sample files in tests/sample_data/ are synthetic placeholders.

🤝 Contributing

PRs welcome! Please open an issue first if you plan significant changes to the data model or pipeline interfaces.

📜 License

MIT — see LICENSE
.
