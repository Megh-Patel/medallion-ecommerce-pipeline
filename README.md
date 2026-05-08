<img width="347" height="228" alt="Screenshot 2026-05-08 152155" src="https://github.com/user-attachments/assets/4e0e7951-6d3d-46e7-b529-00c8025d5dd9" />
# Medallion Architecture: E-Commerce Data Pipeline (PySpark)

## Project Overview
This project implements a **Medallion Architecture** to process and analyze over 4 million rows of e-commerce behavior data. Using **PySpark**, I transformed raw, unoptimized CSV data into a high-performance analytical layer stored in **Parquet** format.

## Project Roadmap & Milestones

### Goal 1: Environment & Schema (The Foundation)
**Objective:** Load 4M+ rows efficiently on limited hardware (16GB RAM).
* **Action:** Defined a manual `StructType` schema to avoid the "Double Scan" overhead of `inferSchema`.
* **Action:** Optimized SparkSession with 8GB driver memory allocation.
* **Success:** Verified load with `.count()` and achieved instant schema validation via `.printSchema()`.

### Goal 2: Data Quality & Optimization (The Engineering)
**Objective:** Clean "Dirty" raw data and optimize storage for performance.
* **Action:** Filtered out invalid data (negative prices) and handled nulls in `brand` and `user_session`.
* **Action:** Converted `event_time` strings to proper `TimestampType`.
* **Action:** **The Parquet Switch:** Migrated from CSV to Parquet format.
* **Success:** Reduced data loading time from minutes to seconds.

### Goal 3: Feature Engineering (The Logic)
**Objective:** Enrich the dataset to enable deeper business questioning.
* **Action:** Extracted temporal features (`hour`, `event_date`) for time-series analysis.
* **Action:** Parsed `category_code` using `split()` to create a high-level `department` column.
* **Success:** Created a "Silver" layer capable of answering "When" and "Where" questions.

### Goal 4: Business Insight (The Portfolio "Gold")
**Objective:** Translate technical engineering into executive-level insights.
* **Action:** Built a **Conversion Funnel** to track the journey from View → Cart → Purchase.
* **Action:** Calculated **Revenue Leaders** and identified peak shopping hours.
* **Success:** Discovered that **Accessories** is the primary revenue driver ($1.1M+) with peak activity at **19:00**.

## Technical Challenges & Solutions

### 1. Performance Optimization (Schema & Storage)
* **Challenge:** Processing millions of rows was slow when relying on automatic schema inference and raw CSV reads.
* **Solution:** Manually defined a `StructType` schema to bypass the costly inference phase. I converted the final dataset to **Parquet**, taking advantage of columnar storage for faster analytical queries.

### 2. Data Quality & Cleaning
* **Challenge:** High volume of missing metadata in `brand` and `category_code`, along with null session identifiers.
* **Solution:** * Implemented `fillna` logic to preserve transaction records that would otherwise be lost.
    * Used string manipulation (`split`) to engineer a clean `department` column for business reporting.
    * **Audit:** Dropped rows with null `user_session` values, as these typically represent bot activity or system errors that would skew behavioral metrics.

### 3. Handling Temporal Edge Cases
* **Challenge:** During conversion rate analysis, I encountered `ArithmeticException: [DIVIDE_BY_ZERO]`.
* **Solution:** I realized this was caused by "Ghost Purchases"—users buying items added to carts in previous months (outside our 1-month dataset). I utilized `F.when()` logic to safely handle these scenarios without crashing the pipeline.

## Key Business Insights
* **Revenue Giant:** The **Accessories** department is the primary driver of the business, generating over **$1.1M** in a single month.
* **Peak Engagement:** Website traffic peaks at **19:00 (7 PM)**, identifying the most effective window for marketing outreach.
* **Conversion Funnel:** By analyzing unique `user_id` interactions, I successfully mapped the journey from View to Purchase, accounting for users who skip the "view" step within the current data window.

## Tools & Skills
* **Engine:** PySpark (Spark SQL & DataFrames)
* **Storage:** Apache Parquet
* **Analysis:** Data Aggregation, Pivoting, Feature Engineering
