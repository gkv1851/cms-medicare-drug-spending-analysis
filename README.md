# CMS Medicare Part D & Open Payments Analytics

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![DuckDB](https://img.shields.io/badge/DuckDB-1.0%2B-yellow.svg)](https://duckdb.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

An end-to-end analytical study evaluating large-scale drug expenditures, clinical utilization, unit pricing dynamics, and pharmaceutical promotional transfers using official **Centers for Medicare & Medicaid Services (CMS)** open datasets (~16.5 GB total uncompressed records).

---

## Executive Summary

The rapid adoption of modern therapies—particularly **GLP-1 receptor agonists** and targeted direct oral anticoagulants—has shifted federal prescription drug spending. This project integrates Medicare claims data with manufacturer financial disclosures to investigate:

1. **Volume & Expenditure Concentration**: What share of Medicare Part D spend is concentrated in top branded therapies?
2. **Unit Economics**: How do gross drug costs break down per 30-day fill, per prescription claim, and per daily dosage unit?
3. **Longitudinal Shift (2018–2024)**: How did **Ozempic (Semaglutide)** expand from initial launch to an expenditure exceeding **\$12.38 Billion** in 2024?
4. **Pharmaceutical Promotional Transfers**: Cross-referencing prescribing patterns against the Physician Payments Sunshine Act (Open Payments) data for pharmaceutical outreach.

---

## Datasets Overview

The analysis leverages three primary CMS releases (Data Year 2024 / Release Year 2025–2026):

| Dataset | File | Size | Level of Aggregation |
| :--- | :--- | :--- | :--- |
| **Medicare Part D Prescribers** | `MUP_DPR_RY26_P04_V10_DY24_NPIBN.csv` | ~4.05 GB | Prescriber (NPI) × Drug Brand / Generic |
| **CMS Open Payments (General)** | `OP_DTL_GNRL_PGYR2025_P06302026_06032026.csv` | ~9.23 GB | Transaction-level financial transfers to clinicians |
| **Medicare Part B Physician Services** | `PHY_R26_P05_V10_D24_Prov_Svc.csv` | ~3.25 GB | Rendering Provider (NPI) × HCPCS Procedure Code |

> *Note: Data files exceed GitHub storage thresholds (>100MB) and are excluded via `.gitignore`. Download links are documented below.*

---

## Key Analytical Findings (2024 Data Year)

### 1. Drug Class Spend & Unit Economics

| Brand Name | Generic Name | Total Claims | Total Medicare Spend | Distinct Beneficiaries | Cost / Claim | Cost / Day Supply | Fills / Patient |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Eliquis** | Apixaban | 22,996,543 | **\$19,876,304,796** | 4,866,089 | \$864.32 | \$19.16 / day | 4.7 |
| **Ozempic** | Semaglutide | 9,930,484 | **\$12,375,986,475** | 1,628,946 | \$1,246.26 | \$31.22 / day | 6.1 |
| **Jardiance** | Empagliflozin | 9,411,811 | **\$9,433,356,867** | 2,058,166 | \$1,002.29 | \$18.48 / day | 4.6 |

* **Ozempic (Semaglutide)** represented **\$12.38 Billion** in gross drug cost across **9.93 Million claims** serving **1.63 Million seniors and Medicare patients**.
* **Eliquis (Apixaban)** was the single largest overall Medicare Part D expenditure at **\$19.88 Billion**.
* The average cost per daily dosage unit for Ozempic reached **\$31.22/day** (~**\$936.60** per 30-day equivalent supply).

---

### 2. Ozempic Longitudinal Trajectory (2018 – 2024)

Tracking Ozempic's progression across Part D from its first full year post-FDA approval:

```
Total Spend ($B)
  $14B ┼                                                ● $12.38B
  $12B ┼                                    ● $10.75B
  $10B ┼
   $8B ┼
   $6B ┼
   $4B ┼                        ● $4.64B
   $2B ┼            ● $1.62B  ● $2.61B
    $0 ┼  ● $0.07B  ● $0.70B
       └─────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────▶ Year
            2018      2019      2020      2021      2022      2023      2024
```

| Year | Total Medicare Spend | Total Claims | Total Beneficiaries | Avg Spend / Claim | Avg Spend / Daily Dose |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **2018** | \$68.7 Million | 88,348 | 26,056 | \$777.62 | \$26.60 |
| **2019** | \$701.3 Million | 751,213 | 212,504 | \$933.56 | \$27.35 |
| **2020** | \$1.62 Billion | 1,643,589 | 425,123 | \$986.32 | \$28.10 |
| **2021** | \$2.61 Billion | 2,501,489 | 617,801 | \$1,043.21 | \$28.85 |
| **2022** | \$4.64 Billion | 4,210,812 | 948,223 | \$1,101.85 | \$29.62 |
| **2023** | \$10.75 Billion | 8,421,905 | 1,412,880 | \$1,276.44 | \$30.45 |
| **2024** | **\$12.38 Billion** | **9,930,484** | **1,628,946** | **\$1,246.26** | **\$31.22** |

#### Trajectory Drivers:
* **180× Total Spend Surge**: Spending expanded from \$68.7M to over \$12.38B, driven by therapeutic adoption in Type 2 diabetes and cardio-metabolic indications.
* **Dose Escalation Impact**: Spend per claim rose from **\$777.62 to \$1,246.26 (+60%)**, resulting from patients titrating up from starter doses (0.25 mg / 0.5 mg) to higher maintenance strengths (1.0 mg and 2.0 mg pens).

### 3. HCP-Level Prescribing Analytics (Prescriber × Drug Grain)

Moving from macro-market aggregates down to the clinician level, each record in the Medicare Part D Prescriber file represents a unique **Prescriber (NPI) × Drug** combination.

#### Top HCP Prescribers for Ozempic (by Total Drug Cost, 2024)

| NPI | Clinician | Specialty | State | Prescription Fills (`Tot_Clms`) | Standardized 30-Day Fills | Days Supply | Total Drug Cost | Patients (`Tot_Benes`) |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| `1578621702` | Angueira-Serrano, Eugenio | Endocrinology | FL | 5,444 | 6,188.2 | 177,103 | **\$5,821,520.40** | 885 |
| `1619082625` | Ilyayeva, Stella | Internal Medicine | NY | 3,061 | 3,833.6 | 111,402 | **\$3,237,847.05** | 582 |
| `1750502183` | Robles, Monica | Endocrinology | AL | 1,675 | 2,635.6 | 76,949 | **\$2,486,782.20** | 379 |
| `1043659345` | Hassan, Syed | Endocrinology | TX | 1,506 | 2,488.8 | 72,847 | **\$2,378,390.17** | 380 |
| `1275611360` | Saad, Bashar | Endocrinology | CA | 2,138 | 2,570.6 | 73,638 | **\$2,352,797.36** | 447 |
| `1932337052` | Frey, Lane | Endocrinology | LA | 1,296 | 2,384.0 | 70,176 | **\$2,316,185.63** | 306 |
| `1508838434` | Villanueva, Ronald | Endocrinology | NJ | 1,154 | 2,470.8 | 73,368 | **\$2,315,131.43** | 292 |
| `1457458168` | Kowalyk, Stephan | Endocrinology | PA | 1,983 | 2,492.3 | 71,446 | **\$2,294,737.26** | 348 |
| `1447258850` | Jayaraj, Kandaswamy | Endocrinology | TX | 1,321 | 2,238.4 | 65,570 | **\$2,092,812.88** | 301 |
| `1184745143` | Burtch, Brian | Endocrinology | OH | 1,281 | 2,127.3 | 62,269 | **\$2,021,080.30** | 308 |

#### Prescriber Population Distribution Benchmarks:
* **Active Prescribers**: **171,191** distinct HCPs prescribed Ozempic in Medicare Part D.
* **Prescription Volume Distribution**:
  * **Mean Fills / HCP**: **58.0** fills
  * **Median Fills / HCP**: **36.0** fills
  * **Maximum by Single Clinician**: **5,444** fills
* **Financial Spend Distribution**:
  * **Mean Drug Spend / HCP**: **\$72,293.44**
  * **Median Drug Spend / HCP**: **\$42,843.26**
  * **Maximum Single Clinician Spend**: **\$5,821,520.40**
* **Standardized 30-Day Fills vs. Raw Fills**: Shows significant 90-day mail-order and retail maintenance fills, where a single prescription claim represents 3 standardized 30-day fills (e.g. 5,444 claims $\rightarrow$ 6,188.2 30-day fills).

---

### 4. Open Payments Manufacturer Transparency

Cross-referencing `OP_DTL_GNRL_...csv` reveals targeted outreach by **Novo Nordisk Inc.** across key medical specialties prescribing Ozempic:
* **Payment Categories**: Primarily concentrated in *Compensation for services other than consulting (Faculty / Speaker programs)*, *Consulting Fees*, *Food and Beverage*, and *Travel and Lodging*.
* **Provider Reach**: Thousands of high-volume endocrinologists, cardiologists, and primary care clinicians received direct professional engagements.

---

## Technical Architecture & Performance Optimization

Traditional in-memory processing (`pandas.read_csv`) fails on 16.5 GB uncompressed CSV datasets on standard machines. 

This repository leverages **DuckDB**:
* **Out-of-Core Processing**: Streams data directly off disk with zero memory blow-up.
* **Vectorized Execution**: Multi-threaded SIMD parallel query engine.
* **Projection Pushdown**: Reads only the required columns out of the 91-column Open Payments file, accelerating query execution by ~15×.
* **Bounded RAM**: Configured with strict resource bounds (`PRAGMA memory_limit = '3.5GB'`), ensuring high performance even in resource-constrained environments.

---

## Repository Structure

```
├── cms_data_preprocessing.ipynb    # Main analytical notebook (DuckDB + Python + Viz)
├── requirements.txt                # Python package dependencies
├── .gitignore                      # Excludes large CSV data files & checkpoints
└── README.md                       # Documentation, methodology, and key findings
```

---

## Getting Started

### 1. Prerequisites
* Python 3.10+
* Git

### 2. Installation
Clone the repository and install dependencies:
```bash
git clone https://github.com/gkv1851/cms-medicare-drug-spending-analysis.git
cd cms-medicare-drug-spending-analysis
pip install -r requirements.txt
```

### 3. Dataset Download
Download the official public datasets from the [CMS Data Portal](https://data.cms.gov):
1. **Medicare Part D Prescribers - by Provider and Drug**: [data.cms.gov](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers)
2. **Open Payments - General Payments**: [openpaymentsdata.cms.gov](https://openpaymentsdata.cms.gov/)
3. Place the CSV files directly in the project directory.

### 4. Running the Notebook
Launch Jupyter:
```bash
jupyter lab cms_data_preprocessing.ipynb
```

---

## License

This project is licensed under the [MIT License](LICENSE).
Data source: Centers for Medicare & Medicaid Services (CMS) public datasets.

