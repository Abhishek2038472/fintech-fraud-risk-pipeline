Data Pipeline & Ingestion
Source: 800,000+ transactional records (fraudTrain.csv) streamed from Kaggle.

Pipeline: Python batch ingestion (chunksize=100000) via SQLAlchemy and psycopg2.

Transforms: Masked card numbers (cc_num_last4), parsed timestamps into transactional dates/hours, and engineered the nocturnal risk flag (is_night_transaction: 11 PM – 5 AM).


Database Architecture (fintech_risk_dw)
┌───────────────────────────────┐
                  │        dim_cardholders        │
                  ├───────────────────────────────┤
                  │ PK  cc_num (VARCHAR)          │
                  │     cc_num_last4 (VARCHAR)    │
                  │     first_name, last_name     │
                  │     gender, job, dob          │
                  │     street, city, state, zip  │
                  │     lat, long, city_pop       │
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
