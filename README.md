# Claims Data Pipeline Automation 🚀💉

Hey there, revenue cycle warriors! 👋 Tired of wrestling with messy claims data like it's a never-ending wrestling match? Enter **Claims Data Pipeline Automation** – your vendor-agnostic superhero for ingesting, normalizing, and analyzing 837/835 claims. Built by [QMTRY.ai](https://qmtry.ai) for provider teams battling the multi-clearinghouse chaos. We're talking resilient, audit-ready processing that turns EDI nightmares into dashboard delights. No more manual drudgery – just clean claims, smart denials intel, and KPIs that make your boss do a happy dance! 🕺💼

## ✨ What You Get (The Goodies Basket) 🎁

- **End-to-End Pipeline for X12 Flows** 📥: Handles 837P/837I/835/999/277CA like a pro, with hooks ready for 270/271 and 276/277 expansions.
- **Multi-Clearinghouse Magic** 🔄: Works with Change Healthcare, Waystar, Availity, and more – per-payer routing and failover to keep things humming even when vendors hiccup.
- **Canonical Data Model** 🗂️: Transforms raw chaos into tidy tables like claims_header, claims_line, adjudication, payments, and ack_* for easy analytics.
- **Denials Intelligence** 🕵️‍♂️: Maps CARC/RARC codes to root-cause groupings, plus ACK SLAs with alerts for those sneaky gaps.
- **Streamlit Dashboard Starter** 📊: Quick views on FPCCR, denial rates, payer lags, and A/R rollups – because who doesn't love a good chart party?
- **Audit Trail Superpowers** 🔍: Deterministic lineage hashes trace every byte from raw EDI to normalized rows. No more "where did that come from?" mysteries!
- **Portfolio-Ready Case Study Template** 📝: Show off your wins like a boss – impress recruiters or clients with real results.

## 🧠 Elevator Pitch (Buyer-Friendly Blurb) 🛗

“We reduced manual touches in claim intake and remit posting, improved First-Pass Clean Claim Rate, and cut payer lag by adding a deterministic parser + rules layer between EHR billing and multiple clearinghouses. Everything lands in analytics-ready marts with KPIs and SLA alarms.”  
*(Pro tip: Drop this in your next meeting and watch the nods multiply! 😎)*

## 🏗️ Architecture (The Blueprint of Awesomeness) 🛠️

Picture this: A seamless flow from your EHR to dashboards, dodging clearinghouse pitfalls like a ninja. Here's the visual magic (Mermaid style – because diagrams are cooler than walls of text!):

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
```

## 📦 Repository Layout (Where the Magic Lives) 🗺️

```
.
├── dashboards/
│ └── rcm_app.py # Streamlit starter 📊
├── marts/
│ └── schemas/ # Example DDLs 🗃️
├── parsers/
│ ├── x12_pyx12_adapter.py # parser stub (swap in pyx12) 🔧
│ └── x12_bots_adapter.py # parser stub (swap in Bots) 🤖
├── pipelines/
│ ├── ingest_edi.py # X12 → canonical JSONL 📥
│ ├── build_marts.py # KPI marts 🏗️
│ └── utils.py # IO + lineage hashes 🛠️
├── configs/
│ ├── pipeline.example.yml # routing & SLA config ⚙️
│ └── mappings/carc_groups.csv # CARC → root cause 🕵️
├── tests/
│ ├── test_mapping.py
│ └── sample_data/ # fake 837/835/ACKs (no PHI) 🧪
├── .github/workflows/ci.yml # CI workflow 🚀
├── COMPLIANCE.md # Compliance deets 🔒
├── SECURITY.md # Security besties 🛡️
├── requirements.txt # Deps list 📋
├── .env.example # Env template 🔑
└── README.md # You're reading it! 👀
```

## 🚀 Quickstart (Local Demo – Zero to Hero in Minutes!) ⚡

Ready to test-drive this beast? No real data needed – we've got synthetic samples to play with. Let's go!

1. **Create a Virtual Environment** 🛡️  
   `python -m venv .venv`  
   *Windows:* `.venv\Scripts\activate`  
   *macOS/Linux:* `source .venv/bin/activate`

2. **Install Minimal Deps** 📦  
   `pip install -r requirements.txt`

3. **Run the Demo ETL on Sample Files (No PHI!)** 🏃‍♂️  
   `python pipelines/ingest_edi.py --inbox tests/sample_data --warehouse warehouse`  
   `python pipelines/build_marts.py --warehouse warehouse`

4. **Launch the Dashboard** 📈  
   `streamlit run dashboards/rcm_app.py`  

*Parser Adapters Note:* Stubs spit out demo rows for fun. When you're serious, plug in a real translator like pyx12 or Bots in `parsers/` and tweak `requirements.txt`. Boom! 💥

## 🔧 Configuration (Tune It Like a Guitar) 🎸

Copy `.env.example` to `.env` and stash your secrets for SFTP/AS2/API connections.  

Tweak `configs/pipeline.example.yml` for:  
- **Sources** 📍: Clearinghouse endpoints or folders for 837/835.  
- **Routing** 🔀: Default and payer-specific overrides.  
- **ACK SLA Hours** ⏰: Alert thresholds for missing 999/277CA.  
- **Warehouse Dir** 🗄️: Local path or object storage mount.  

Easy peasy – now you're the config wizard! 🧙‍♂️

## 🧾 Canonical Data Model (The Heart of the Beast) ❤️

| Table          | Purpose                                                                 |
|----------------|-------------------------------------------------------------------------|
| claims_header  | Claim header info: IDs, payer, type, DOS, POS, billed amount            |
| claims_line    | CPT/HCPCS/RevCode lines: units, billed/allowed/paid, patient            |
| adjudication   | CARC/RARC, group codes, status, remarks                                 |
| payments       | Check/EFT posting details, post dates, traces                           |
| ack_999        | Functional acks (accepted/rejected, error codes)                        |
| ack_277ca      | Claim acknowledgment with edit trails                                   |
| kpi_daily      | Aggregated metrics for dashboards & SLAs                                |

All rows come with `_hash` for that sweet, sweet lineage back to raw EDI. Traceability FTW! 🔗

## 📊 KPIs & Dashboards (Starter Pack – Level Up Your Insights) 🌟

- FPCCR (First-Pass Clean Claim Rate) ✅  
- Initial denial rate & top CARC drivers ❌  
- Payer cycle time / lag ⏱️  
- Total billed / allowed / paid 💰  
- 277CA rejection reasons (edit classes) 📉  
- Auto-posting % from 835s 🤖  

Open `dashboards/rcm_app.py` and hack away with Plotly/Streamlit. Turn data into dazzling visuals – your stakeholders will thank you! 🎉

## 🛡️ Resilience & Observability (Bulletproof Your Ops) 🦾

- **Multi-Clearinghouse Routing** 🔄: Primary/secondary with payer overrides – no single point of failure!  
- **ACK SLA Monitors** ⏰: Alerts for missing 999/277CA or spike in rejects.  
- **Nightly Reconciliation** 🌙: Match 837 → 835 volumes by payer.  
- **Runbooks** 📖: Pause submissions during vendor meltdowns.  

Stay ahead of the curve – because downtime is for amateurs! 😤

## 🧩 Integrations (Plug & Play Vibes) 🔌

- **EHR/PM** 🏥: Epic Resolute HB/PB or any X12 837 producer.  
- **Clearinghouses** 📡: Change Healthcare, Waystar, Availity (SFTP/AS2/API).  
- **Warehouses** 🗄️: Postgres, DuckDB, Snowflake, Delta Lake.  
- **FHIR (Optional)** 📚: Join payer ExplanationOfBenefit for adjudication superpowers.  

Mix and match – it's like LEGO for claims data! 🧱

## 🧪 Testing & CI (Keep It Green & Mean) ✅

Run `pytest -q` for quick checks.  

GitHub Actions in `.github/workflows/ci.yml` handles Python 3.11 setup, deps install, and tests. CI/CD magic to keep your repo shipshape! 🚢

## 🧱 Case Study Template (Your Portfolio Glow-Up) 📸

Client: [Multi-specialty provider group, ~XX locations]  
Problem: High denial rate ([X]%), single-vendor risk, slow payer acks.  
Approach:  
- Deterministic X12 parsing + canonical model  
- Dual-clearinghouse routing with ACK SLAs  
- KPI dashboard (FPCCR, payer lag, CARC drivers)  
Results (90 days):  
- FPCCR ↑ [+X.X pp]  
- Initial denial rate ↓ [−Y.Y pp]  
- Payer lag ↓ [Z.Z days]  
- Manual touches ↓ [~W%] in remit posting  

Screenshots: Stash PNG/GIFs in `docs/case-study/`. Shine bright! ✨

## 🧭 Roadmap (Future-Proof Fun) 🔮

- Production adapters for pyx12 / Bots / Spark-based parsers 🤖  
- Eligibility & status joins (270/271, 276/277) 📈  
- Auto-appeal letter generation (human-in-the-loop) ✉️  
- Prior auth joins & payer benchmarking 📊  

Watch this space – we're just getting started! 🚀

## 🔐 Compliance & Security (Lock It Down) 🛡️

HIPAA BAAs? Check. TLS in transit, KMS at rest, optional tokenization? Double check. Role-based access, short-lived creds, PHI-free logs? Triple check!  

Dive into `COMPLIANCE.md` and `SECURITY.md` for checklists and runbooks. No PHI in this repo – samples are 100% synthetic. Safety first! 👮‍♂️

## 🤝 Contributing (Join the Party!) 🎉

PRs welcome! Open an issue first for big changes to the data model or interfaces. Let's build something epic together! 🤝

## 📜 License (Free as a Bird) 🕊️

MIT – see [LICENSE](LICENSE). Go forth and automate! 🌍
