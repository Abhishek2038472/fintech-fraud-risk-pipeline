💳 FinTech High-Frequency Fraud & Commercial Risk Data Warehouse
An enterprise-grade risk engineering and business intelligence pipeline processing 800,000+ credit card transactions representing $63.24M in gross payment clearing volume[cite: 7].

The system automates chunked data ingestion, models an OLAP Star Schema with B-Tree indexes, isolates nocturnal attack vectors and category compromise clusters, and provides a multi-phase fraud mitigation framework.

📌 Executive Summary & Key Findings
Clearance Volume: 800,000 transactions totaling $63,242,081 across national merchant terminals[cite: 7].

Gross Fraud Exposure: $2.70M in direct fraud losses across 4,690 identified fraudulent events[cite: 3, 7].

The 7.83x Ticket Asymmetry:

Average Legitimate Ticket: $67.62[cite: 3].

Average Fraud Ticket: $525.80[cite: 3].

Fraudulent attacks are concentrated on high-ticket draining rather than micro-charge validation[cite: 3].

The 4.65x Nocturnal Spike:

Daytime Fraud Rate: 0.30% ($1.25M loss across 676K transactions)[cite: 4].

Nocturnal Window (11 PM – 5 AM): 1.38% ($1.44M loss across 223K transactions)[cite: 4].

Off-hours generate 53.5% of total dollar losses despite accounting for under 25% of clearing transactions[cite: 4].

Channel Vulnerabilities:

shopping_net (E-Commerce): $1,174,720 (43.5% of total losses)[cite: 6].

shopping_pos (Retail In-Store): $500,590 (18.5% of total losses)[cite: 6].

misc_net (Digital Goods & Gift Cards): $484,470 (17.9% of total losses)[cite: 6].

Spatial Risk: Out-of-area transactions in the 50–100 km tier captured the largest single loss share at $1.53M[cite: 7].

🏗️ Architecture & Pipeline Flow
┌─────────────────────────┐
│     Raw Data Layer      │  • fraudTrain.csv (1.29M+ Transactions)
│   (Kaggle Card Stream)  │  • Cardholder profiles, terminal coordinates, timestamps
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│   Python Ingestion ETL  │  • Chunked batch ingestion (100K rows/batch)
│ (SQLAlchemy + psycopg2) │  • Card masking (cc_num_last4) & timestamp parsing
└────────────┬────────────┘  • Feature engineering (is_night_transaction flag)
             │
             ▼
┌─────────────────────────┐
│  PostgreSQL Warehouse   │  • Staging: stg_credit_transactions
│   (OLAP Star Schema)    │  • Dimensions: dim_cardholders, dim_merchants
└────────────┬────────────┘  • Fact: fact_transactions (Haversine distance, B-Tree indexes)
             │               • Reporting View: view_fraud_risk_analysis
             ▼
┌─────────────────────────┐
│   Power BI Analytics    │  • Dedicated DAX measures repository (_RiskMeasures)
│  (Import Mode Engine)   │  • Page 1: Commercial Risk & Threat Overview
└────────────┬────────────┘  • Page 2: Velocity & Spatial Diagnostics
             │
             ▼
┌─────────────────────────┐
│  Executive Deliverable  │  • 4-page Word/PDF Forensic Risk Audit (python-docx)
│   (Mitigation Plan)     │  • Targeted controls to protect $1.77M/mo in chargebacks
└─────────────────────────┘


🗄️ Relational Star Schema Model

┌───────────────────────────────┐
                  │        dim_cardholders        │
                  ├───────────────────────────────┤
                  │ PK  cc_num (VARCHAR)          │
                  │     cc_num_last4 (VARCHAR)    │
                  │     first_name, last_name     │
                  │     gender, job, dob          │
                  │     street, city, state, zip  │
                  │     cardholder_lat, long      │
                  │     city_pop (INT)            │
                  └───────────────┬───────────────┘
                                  │ 1
                                  │
                                  │ N
┌─────────────────────────────┐   │   ┌───────────────────────────────┐
│        dim_merchants        │   └───┤       fact_transactions       │
├─────────────────────────────┤       ├───────────────────────────────┤
│ PK  merchant_id (INT)       ├───────┤ PK  trans_num (VARCHAR)       │
│     merchant (TEXT)         │ 1   N │ FK  cc_num (VARCHAR)          │
│     category (TEXT)         │       │ FK  merchant_id (INT)         │
└─────────────────────────────┘       │     transaction_amount (NUM)  │
                                      │     merch_lat, merch_long     │
                                      │     transaction_timestamp     │
                                      │     transaction_date, hour    │
                                      │     is_night_transaction (INT)│
                                      │     distance_km (NUMERIC)     │
                                      │     is_fraud (INT)            │
                                      └───────────────────────────────┘
