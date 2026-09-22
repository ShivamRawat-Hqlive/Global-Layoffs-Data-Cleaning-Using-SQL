# Global Layoffs Data Cleaning Using SQL

## 📌 Project Overview

This project focuses on cleaning and preparing a **global layoffs dataset** using **MySQL**.

The objective is to transform raw and inconsistent data into a structured, reliable, and analysis-ready dataset. The cleaning process includes duplicate detection and removal, data standardization, NULL-value handling, date conversion, and removal of records that cannot contribute meaningfully to the analysis.

The cleaned dataset is subsequently used for exploratory data analysis.

---

## 🎯 Objectives

* Identify and remove duplicate records
* Standardize inconsistent categorical values
* Clean country and industry fields
* Convert date values into the proper `DATE` format
* Handle missing and empty values
* Populate missing industry values where possible
* Remove records with insufficient information
* Create a clean staging table for analysis

---

## 🛠️ Technologies Used

* **MySQL**
* SQL
* Window Functions
* CTEs
* JOINs
* Aggregate Functions
* Data Cleaning Techniques

---

## 🗂️ Dataset Structure

The dataset contains information related to company layoffs, including:

* `company`
* `location`
* `industry`
* `total_laid_off`
* `percentage_laid_off`
* `date`
* `stage`
* `country`
* `funds_raised_millions`

---

## 🔄 Data Cleaning Process

### 1. Create a Staging Table

A staging table is created using the structure of the original `layoffs` table.

```sql
CREATE TABLE layoffs_staging
LIKE layoffs;
```

The original data is then copied into the staging table.

---

### 2. Identify Duplicate Records

`ROW_NUMBER()` is used with `PARTITION BY` to identify duplicate records across the relevant columns.

```sql
ROW_NUMBER() OVER(
    PARTITION BY company, location, industry,
    total_laid_off, percentage_laid_off,
    date, stage, country, funds_raised_millions
)
```

Records with a `row_num` greater than `1` are treated as duplicates.

---

### 3. Remove Duplicates

Duplicate records are removed from the staging dataset using the generated row numbers.

---

### 4. Standardize Country Values

Trailing periods are removed from country names to make categorical values consistent.

```sql
UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country);
```

---

### 5. Convert Date Format

The original date values are converted into MySQL `DATE` format.

```sql
UPDATE layoffs_staging2
SET date = STR_TO_DATE(date, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN date DATE;
```

---

### 6. Standardize Industry Values

Different representations of cryptocurrency-related industries are standardized to a single value:

```sql
UPDATE layoffs_staging2
SET industry = 'Crypto'
WHERE industry IN ('Crypto Currency', 'CryptoCurrency');
```

---

### 7. Handle Missing Industry Values

Empty industry values are converted to `NULL`.

Missing industry information is then populated using another record from the same company where an industry value is available.

```sql
UPDATE layoffs_staging2
SET industry = NULL
WHERE industry = '';
```

```sql
UPDATE layoffs_staging2 t1
JOIN layoffs_staging2 t2
ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
AND t2.industry IS NOT NULL;
```

---

### 8. Remove Unusable Records

Records where both `total_laid_off` and `percentage_laid_off` are missing are removed because they do not provide useful layoff information for analysis.

```sql
DELETE FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```

---

## 📊 Final Dataset

After cleaning, the staging table contains standardized and analysis-ready layoff records.

The temporary `row_num` column used for duplicate detection is removed from the final dataset.

---

## 📁 Project Structure

```text
Global-Layoffs-SQL/
│
├── Data Cleaning.sql
└── README.md
```

---

## 💡 Skills Demonstrated

* SQL Data Cleaning
* Data Quality Validation
* Duplicate Detection
* Window Functions
* CTEs
* JOIN Operations
* NULL Handling
* Data Standardization
* Date Transformation
* MySQL
* Data Preparation for EDA

---

## 👤 Author

**Shivam Rawat**

GitHub: [ShivamRawat-Hqlive](https://github.com/ShivamRawat-Hqlive)
