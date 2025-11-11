# DSAI3202 – Lab 3 : Data Preprocessing on Azure

## Objective
The objective of this lab was to design and implement a complete data-preprocessing workflow on **Microsoft Azure** using the Goodreads dataset.  
The goal was to transform messy raw JSON files into clean, structured, and analytics-ready data following the **Medallion Architecture** (Bronze → Silver → Gold).

---

## Workflow Summary

### Bronze Layer – Raw Data
- Created an Azure Data Lake Gen2 storage account with a hierarchical namespace.  
- Uploaded raw JSON datasets (`books`, `reviews`, `authors`) to the **/raw/** container.  
- This layer stores data exactly as ingested—no modifications—to preserve the source.

**Purpose:** keep an immutable copy of the original data.

---

### Silver Layer – Processed Data
- Built pipelines in **Azure Data Factory (ADF)** to read JSON from the Bronze layer and write Parquet files to the Silver layer.  
- Added schema mapping and field flattening.  
- Validated successful runs through the ADF monitor tab.  
- Result: structured, query-friendly Parquet data in `/processed/books/`, `/processed/reviews/`, `/processed/authors/`.

**Purpose:** transform and standardize data for downstream processing.

---

### Gold Layer – Curated Data
- Connected to Azure Databricks workspace for advanced cleaning and joining.  
- Used PySpark to filter invalid records, remove duplicates, and enforce rating values between 1 and 5.  
- Combined `books`, `authors`, and cleaned `reviews` into a single **Curated Reviews** table.  
- Saved the final dataset as a **Delta Table** named `curated_reviews` inside the default schema.

**Purpose:** deliver analytics-ready, high-quality data for reporting and machine learning.

---

## Lab Questions and Answers

### Q1 – What is the purpose of the Bronze, Silver, and Gold layers?
> - **Bronze:** Stores raw data as-is.  
> - **Silver:** Cleans and structures the data into Parquet format.  
> - **Gold:** Joins and enriches data to produce curated, analysis-ready datasets.

---

### Q2 – Why convert JSON to Parquet?
> Parquet is columnar, compressed, and optimized for Spark processing. It reduces storage cost and improves read performance compared to JSON.

---

### Q3 – Why use Azure Data Factory instead of Databricks for ingestion?
> ADF is ideal for ETL pipelines and file conversion tasks with minimal code. It simplifies moving data from raw to processed storage before handing off to Databricks for advanced transformations.

---

### Q4 – What issues were found in the reviews dataset and how were they handled?
> Some records had missing IDs, invalid ratings, or empty text fields. These were removed or corrected in Databricks to ensure data integrity.

---

### Q5 – What cleaning steps were performed in Databricks?
> - Removed rows with null IDs or ratings  
> - Cast ratings to integer and kept values 1 – 5  
> - Trimmed and filtered short or empty reviews  
> - Dropped duplicates based on `review_id`

---

### Q6 – What is Delta Lake and why is it used?
> Delta Lake adds ACID transactions, schema enforcement, and version control to Parquet data. It ensures consistency and reliability across the data lifecycle.

---

### Q7 – What is the final output of this lab?
> The final output is the **`curated_reviews` Delta table** in the Gold layer, which serves as the foundation for feature engineering and model training in future labs.

---

## Technologies Used
- **Azure Data Factory** – Data ingestion and format conversion  
- **Azure Data Lake Gen2** – Centralized hierarchical storage  
- **Azure Databricks (Python / PySpark)** – Data cleaning and curation  
- **Delta Lake Format** – Reliable and transactional data storage  

---

## Conclusion
Lab 3 successfully implemented the Azure Medallion Architecture pipeline. Raw Goodreads data was ingested, validated, and transformed into a clean Delta table ready for analytics and machine learning. This lab established the foundation for Lab 4 and future AI workflows.

