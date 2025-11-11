# DSAI3202 – Lab 4 : Text Feature Engineering 

## 🧠 Objective
The objective of this lab was to perform **text feature engineering** on the curated Goodreads reviews dataset (Gold layer from Lab 3).  
The goal was to transform raw review text into quantitative features for use in machine-learning models, while maintaining a clean and leakage-safe workflow using **Azure Databricks** and **Delta Lake**.

---

## Workflow Summary

### Starting Point – Gold Dataset from Lab 3
- Began with the **Gold-layer `curated_reviews` table** created in Lab 3.  
- The dataset included:  
  `review_id`, `book_id`, `author_id`, `user_id`, `rating`, `review_text`, `n_votes`, `date_added`, `title`, `name`, `language_code`.  
- **Purpose:** prepare this curated data for ML by extracting numeric text-based features.

---

### Step 1 – Data Splitting
- Created **Train (70 %)**, **Validation (15 %)**, and **Test (15 %)** splits.  
- Ensured **no data leakage** by fitting transformations only on training data.  
- Saved splits under:  
  `/gold/features_v2/train`, `/gold/features_v2/val`, `/gold/features_v2/test`.

---

### Step 2 – Text Cleaning and Basic Features
- Normalized text: lowercased, trimmed spaces, removed control characters.  
- Dropped reviews shorter than 10 characters.  
- Engineered numeric features:  
  - **review_length_words** – word count per review  
  - **review_length_chars** – character count  
  - **review_age_days** – days since the review was written  

**Purpose:** provide simple quantitative indicators of review size and recency.

---

### Step 3 – Sentiment and TF-IDF Feature Engineering
Because of Py4J restrictions in Databricks, TF-IDF was computed manually using Spark SQL functions.

**Process:**
1. Tokenize text into words with regex.  
2. Compute **Term Frequency (TF)** = count of each word per review.  
3. Compute **Document Frequency (DF)** = how many reviews contain each word.  
4. Compute **Inverse Document Frequency (IDF)** = `log(total_docs / (df + 1))`.  
5. Combine TF × IDF = **TF-IDF weight** per word.  
6. Average all TF-IDF values per review → `avg_tfidf`.

**Outputs:**  
`/gold/features_v3/train_tfidf_full`  
`/gold/features_v3/val_tfidf_full`  
`/gold/features_v3/test_tfidf_full`

---

### Step 4 – Verification and Validation
- Verified schema and record counts across all splits.  
- Ensured consistent columns:  
  `review_id`, `rating`, `review_length_words`, `review_length_chars`, `review_age_days`, `avg_tfidf`, and metadata fields.  
- Confirmed all stored as **Delta tables** for reliability.

---

## Lab Questions and Answers

### Q1 – What is the purpose of feature engineering in this lab?
> To convert raw text reviews into structured numerical features such as length, sentiment, and TF-IDF.  
> These numeric representations allow ML algorithms to process textual information.

---

### Q2 – Why split the data before feature extraction?
> To prevent **data leakage**.  
> TF-IDF and other transformations must be fit only on training data so validation and test sets remain unbiased.

---

### Q3 – Which features were engineered?
> - **review_length_words** – number of words  
> - **review_length_chars** – number of characters  
> - **review_age_days** – review recency  
> - **avg_tfidf** – average importance score of words in each review

---

### Q4 – Explain TF-IDF and its usefulness.
> **TF-IDF (Term Frequency × Inverse Document Frequency)** measures how unique a word is in a review relative to all reviews.  
> It highlights distinctive words and down-weights very common ones, improving text classification performance.

---

### Q5 – How was sentiment handled?
> VADER sentiment scoring was optional.  
> Due to environment limits, the focus remained on TF-IDF as the primary text-importance feature.

---

### Q6 – What is the purpose of the Gold-layer outputs?
> To provide **machine-learning-ready datasets** containing all engineered text and numeric features.  
> These serve as direct inputs for **Lab 5 (Model Training and Evaluation)**.

---

##  Technologies Used
- **Azure Databricks** – text processing and feature engineering  
- **PySpark (DataFrames + SQL)** – aggregation and TF-IDF calculation  
- **Azure Data Lake Gen2** – data storage for splits  
- **Delta Lake** – reliable and versioned output tables  

---

##  Conclusion
Lab 4 successfully transformed unstructured Goodreads review text into structured, meaningful numerical features.  
The final **Gold-layer TF-IDF datasets** are analytics-ready and form the foundation for model training in Lab 5.

