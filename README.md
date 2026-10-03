### Database Architecture (`fintech_risk_dw`)

#### 1. Dimension Tables
* **`dim_cardholders`** (Customer Entity)
  * **Primary Key:** `cc_num` (VARCHAR)
  * **Attributes:** `cc_num_last4`, `first_name`, `last_name`, `gender`, `job`, `dob`, `street`, `city`, `state`, `zip`, `lat`, `long`, `city_pop`
* **`dim_merchants`** (Merchant Terminal Entity)
  * **Primary Key:** `merchant_id` (INT)
  * **Attributes:** `merchant` (TEXT), `category` (TEXT)

#### 2. Fact Table
* **`fact_transactions`** (Clearinghouse Events)
  * **Primary Key:** `trans_num` (VARCHAR)
  * **Foreign Keys:** `cc_num` → `dim_cardholders(cc_num)`, `merchant_id` → `dim_merchants(merchant_id)`
  * **Metrics & Flags:** `transaction_amount` (NUM), `merch_lat`, `merch_long`, `transaction_timestamp`, `transaction_date`, `transaction_hour`, `is_night_transaction` (INT), `distance_km` (NUMERIC), `is_fraud` (INT)

#### 3. Entity Relationships
* `dim_cardholders` **(1)** ────── **(N)** `fact_transactions`
* `dim_merchants`   **(1)** ────── **(N)** `fact_transactions`
