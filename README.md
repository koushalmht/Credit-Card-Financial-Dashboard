# Credit Card Financial Dashboard

An end-to-end data project that takes raw banking data (credit card usage + customer demographics), loads it into a PostgreSQL database via SQL, and visualizes it in an interactive **Power BI dashboard**.

![Dashboard Preview]

Dashboard-1<img width="1162" height="662" alt="Credit Card Transaction" src="https://github.com/user-attachments/assets/b8ddea9f-9fdd-46d8-9787-27df9aa5bc2a" />

Dashboard-2<img width="1167" height="657" alt="Credit Card Customer" src="https://github.com/user-attachments/assets/5145955d-3978-4c79-943c-d6abba09180f" />

Dashboard-3<img width="1157" height="652" alt="Other Insights" src="https://github.com/user-attachments/assets/b0cb6fbf-bc2c-43ab-b9fb-586188d4a5e0" />

# Project Overview

This project simulates a real-world banking analytics workflow:
1. Raw weekly credit card transaction data and customer records are loaded from CSV files into a relational database (PostgreSQL), including a separate "late-arriving data" load (Week-53) to mimic an incremental update process.
2. The consolidated data is connected to **Power BI** to build an interactive financial dashboard for tracking credit card performance, revenue, and customer insights.

# Repository Contents

| File | Description |
|---|---|
| `01_sql_tables.sql` | DDL script — creates the `cc_detail` and `cust_detail` tables |
| `credit_card.csv` | Core credit card dataset (~10,100 rows, Weeks 1–52, 2023) |
| `customer.csv` | Core customer dataset (~10,100 rows) |
| `cc_add.csv` | Late-arriving credit card data — Week-53 (185 rows) |
| `cust_add.csv` | Late-arriving customer data — Week-53 (185 rows) |
| `Credit Card Financial Dashboard.pbix` | Power BI dashboard file |

# Data Model

**`cc_detail`** — one row per client per week, credit card activity
- `Client_Num`, `Card_Category` (Blue/Silver/Gold/Platinum), `Annual_Fees`, `Activation_30_Days`, `Customer_Acq_Cost`
- `Week_Start_Date`, `Week_Num`, `Qtr`, `current_year`
- `Credit_Limit`, `Total_Revolving_Bal`, `Total_Trans_Amt`, `Total_Trans_Ct`, `Avg_Utilization_Ratio`
- `Use_Chip`, `Exp_Type`, `Interest_Earned`, `Delinquent_Acc`

**`cust_detail`** — one row per client, demographic/profile data
- `Client_Num`, `Customer_Age`, `Gender`, `Dependent_Count`, `Education_Level`, `Marital_Status`
- `State_cd`, `Zipcode`, `Car_Owner`, `House_Owner`, `Personal_Loan`, `Contact`, `Customer_Job`
- `Income`, `Cust_Satisfaction_Score`

`Client_Num` is the shared key linking the two tables (and the two tables in Power BI).

# Power BI Dashboard

The dashboard connects to the PostgreSQL database (or the CSVs directly) and includes:

- **KPIs:** Total revenue, total transactions, total interest earned, customer acquisition cost, delinquency rate
- **Trends:** Week-over-week and quarter-over-quarter revenue/transaction trends across 2023
- **Segmentation:** Performance by card category (Blue/Silver/Gold/Platinum), by state, by income/age group
- **Customer insights:** Satisfaction score breakdown, education level, marital status, job type
- **Filters/slicers:** Quarter, week, card category, state

  <img width="1162" height="615" alt="image" src="https://github.com/user-attachments/assets/0b34eded-c828-4aaa-a1f2-1eca5e9e7936" />

  <img width="1165" height="660" alt="image" src="https://github.com/user-attachments/assets/174b1c8d-6950-41ce-a247-f6688864a2c2" />

  <img width="1162" height="647" alt="image" src="https://github.com/user-attachments/assets/70fbd83a-2d43-4ea8-8760-c8f82173f4de" />

  <img width="1162" height="602" alt="image" src="https://github.com/user-attachments/assets/5d1a53a2-2a9b-4a51-b9b9-20609e8d29cd" />

  <img width="1162" height="611" alt="image" src="https://github.com/user-attachments/assets/a6149e09-4273-419c-b474-fc1643de3057" />

# Data Pipeline (SQL)

1. **Create the database**
   ```sql
   CREATE DATABASE ccdb;
   ```

2. **Create the tables**
   ```sql
   \i 01_sql_tables.sql
   ```

3. **Load the data** (update file paths to match your local environment)
   ```sql
   COPY cc_detail FROM 'path/to/credit_card.csv' DELIMITER ',' CSV HEADER;
   COPY cust_detail FROM 'path/to/customer.csv' DELIMITER ',' CSV HEADER;

   -- Append the late-arriving Week-53 data
   COPY cc_detail FROM 'path/to/cc_add.csv' DELIMITER ',' CSV HEADER;
   COPY cust_detail FROM 'path/to/cust_add.csv' DELIMITER ',' CSV HEADER;
   ```

   > **Note:** If you hit `ERROR: date/time field value out of range`, set the session datestyle before loading:
   > ```sql
   > SET datestyle TO 'ISO, DMY';
   > ```
   > Still got error!!, then,
   > -- Use Psql to uppload CSV files 
--\copy cc_detail FROM 'file_path' WITH (FORMAT csv, HEADER true)
-- \copy cust_detail FROM 'file_path' WITH (FORMAT csv, HEADER true)

4. Connect Power BI** to PostgreSQL (Get Data → PostgreSQL database) and build/refresh the report.

# Dataset at a Glance

- **Time period:** Full year 2023, weekly granularity (Week-1 through Week-53)
- **Card categories:** Blue, Silver, Gold, Platinum
- **Geography:** Customers across 28 U.S. states
- **Records:** ~10,295 rows across both tables combined

# Tools Used

- **PostgreSQL** — database design & ETL (`COPY` bulk loading)
- **SQL** — table creation, data loading
- **Power BI** — data modeling, DAX measures, dashboard visualization

- **PostgreSQL** — database design & ETL (`COPY` bulk loading)
- **SQL** — table creation, data loading
- **Power BI** — data modeling, DAX measures, dashboard visualization
