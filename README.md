# Claims-Data-Pipeline-Automation

````markdown
# Claims Data Pipeline Automation

Vendor-agnostic pipeline for healthcare claims: ingest **X12 837/835**, normalize to clean tables, monitor **999/277CA** acks, and power **RCM dashboards**.

## Highlights
- Works with multiple clearinghouses (e.g., Change Healthcare, Waystar, Availity)
- Canonical tables: `claims_header`, `claims_line`, `adjudication`, `payments`, `ack_*`
- Denials intelligence (CARC/RARC → root cause)
- Streamlit dashboard starter (FPCCR, denial rate, payer lag)
- HIPAA-aware scaffold (no PHI in repo)

## Quickstart
```bash
python -m venv .venv && source .venv/bin/activate   # Win: .venv\Scripts\activate
pip install -r requirements.txt
python pipelines/ingest_edi.py --inbox tests/sample_data --warehouse warehouse
python pipelines/build_marts.py --warehouse warehouse
streamlit run dashboards/rcm_app.py
````

## Structure

```
parsers/      # swap in pyx12/Bots adapters
pipelines/    # ingest_edi.py, build_marts.py, utils.py
dashboards/   # rcm_app.py (demo KPIs)
configs/      # pipeline.example.yml, CARC mappings
tests/        # tiny fake 837/835/ACKs (no PHI)
```

## Config & Compliance

* Copy `.env.example` → `.env`; edit `configs/pipeline.example.yml` (sources, routing, SLA hours).
* Use the **minimum necessary** PHI; see `COMPLIANCE.md` and `SECURITY.md`.

**License:** MIT

````

**Description (SEO, short):**
```text
Vendor-agnostic claims data pipeline for healthcare: ingest X12 837/835, normalize to analytics marts, track 999/277CA, and surface RCM KPIs—HIPAA-aware.
````

**Extended description (SEO):**

```text
Claims Data Pipeline Automation is a provider-focused, revenue cycle management (RCM) scaffold that ingests healthcare EDI (X12 837/835) from clearinghouses like Change Healthcare, Waystar, and Availity, validates envelopes, normalizes claims to canonical tables, and monitors 999/277CA acknowledgments. The project ships with a Streamlit dashboard for First-Pass Clean Claim Rate (FPCCR), denial rate, payer lag, and A/R rollups, plus CARC/RARC grouping for denials intelligence. It’s vendor-agnostic, production-minded, and HIPAA-aware (no PHI included). Ideal for medical groups, provider organizations, and analytics teams building resilient claims pipelines after industry disruptions. Swap in pyx12 or Bots adapters, configure per-payer routing and SLA windows, and publish analytics marts to your warehouse. Clean, concise, ready for portfolio case studies and real-world RCM pilots.
```
