# Credit_Risk_Feature_Engine
Credit_Risk_Feature_Engine : Automated Bureau Data Aggregation

A Python Based Framework to engineer 100+ Risk Variables from raw credit bureau datasets, including 48-month dpd Trends, utilization metrics 
and inquiry velocity Check.

# Bureau Feature Engineering Engine (BFEE)

## 📌 Project Overview
This repository contains a modular Python library designed to transform raw, unstructured Credit Bureau data into high-value, actionable attributes (features). 

Credit bureau data is typically hierarchical (multiple accounts and enquiries per customer) and time-varying. This engine handles the complexity of cleaning, type-casting, time-alignment, and multi-level aggregation. The resulting feature set serves as the foundational inputs for Credit Scorecards, Regulatory Reporting, and Portfolio Risk Analytics.

## 💼 Business Context & Strategic Importance

### 1. The Building Block of Scorecards
The predictive power of an Application or Behavior Scorecard is driven by well-engineered bureau features. This library generates recursive features (e.g., delinquency or enquiry velocity over various time horizons) that capture a consumer's credit trajectory.

### 2. Risk Management & Credit Policy
These features allow risk managers to set granular credit policies. For example, utilizing the `max_utilisation_active_cc` feature engineered here, a policy can be set to reject applicants with high revolving utilization, mitigating "burst" risk.

### 3. Early Warning Systems (EWS)
By tracking features like `count_enquiries_last_1_month` (credit hunting behavior) or sudden spikes in `MAX_DPD_EXCC`, the bank can proactively manage credit limits or initiate collections.

## 🛠️ Technical Architecture & Modules

### A. Account Analysis (`account_variablenew.py`)
Processes Trade Line details and handles the complexity of different loan products.
* **Credit Card Analytics**: Identifies active cards, calculates individual and aggregate utilization, and identifies sanction limits.
* **Loan Concentration**: Aggregates exposure across Personal Loans (PL), Housing Loans (HL), and Business Loans (BL).
* **Tenure & Vintage**: Calculates the "Calendar Month" difference between account opening and the business date to determine credit history length

### B. Delinquency & Behavior (`dpd.py`)
Processes historical repayment tables to create time-sliced payment behavior features.
* **Time Alignment**: Standardizes various reporting dates to a single reference point to calculate "Days Diff".
* **Status Mapping**: Converts categorical status codes (e.g., 'SUB', 'DBT', 'LSS') into numeric Day Past Due (DPD) equivalents for mathematical modeling.
* **Rolling Windows**: Creates "Max DPD" features for 1, 3, 6, 12, 24, and 36-month horizons.

### C. Enquiry Intelligence (`enquires.py`)
Analyzes credit-seeking behavior to detect "Credit Hunting".
* **Segmented Enquiries**: Separates secured vs. unsecured enquiries to understand applicant intent
* **Velocity Tracking**: Monitors the frequency of enquiries in the last 30 to 90 days as a proxy for financial stress.

## 🚀 Usage Example
```python
from bureau_engine import account_variablenew as av, dpd, enquires

# Load Bureau Data
ac_df = pd.read_csv("account_variable.pdf") # Reference file
dpd_df = pd.read_csv("dpd.pdf")             # Reference file

# 1. Engineer Account Features
ac_cleaned = av.clean_accounts(ac_df)
ac_features = av.aggregate_account_metrics(ac_cleaned)

# 2. Engineer Delinquency Features
dpd_features = dpd.calculate_rolling_dpd(dpd_df)

# 3. Final Master Feature Set
master_df = ac_features.merge(dpd_features, on='LOS_App_Number', how='left')

