# 📊 Box Office Bomb Data Pipeline

## Overview

This project implements an end-to-end data pipeline to analyze financial failures in the film industry using data from Wikipedia and the OMDb API.

The pipeline extracts raw data from a noisy HTML table, validates and cleans it using Pydantic, enriches it with external metadata, and produces a structured dataset for analysis.

---

## 🚀 Objectives

* Scrape the *"Biggest box-office bombs"* table from Wikipedia
* Clean and validate messy data using **Pydantic**
* Enrich movie data using the **OMDb API**
* Perform consistency checks between sources
* Categorize movies based on financial loss
* Export a clean dataset for analysis

---

## 🛠️ Tech Stack

* Python
* BeautifulSoup (Web Scraping)
* Requests (API calls)
* Pydantic (Data Validation)
* Pandas (Data Processing)

---

## 📥 Data Source

* Wikipedia: *List of biggest box-office bombs*
* OMDb API: [http://www.omdbapi.com](http://www.omdbapi.com)

---

## 🔄 Pipeline Workflow

### 1. Data Scraping

* Extracts the "Biggest box-office bombs" table from Wikipedia
* Captures raw string values:

  * Title (with symbols and references)
  * Year
  * Budget
  * Estimated Loss (Nominal)

---

### 2. Data Validation & Cleaning (Pydantic)

A Pydantic model (`MovieData`) is used to enforce schema and clean data:

#### Title Cleaning

* Removes:

  * Footnotes (`[1]`, `[nb 2]`)
  * Special symbols (`§`, `†`)

#### Numeric Parsing

* Removes `$` and text noise
* Converts ranges into averages

  * Example: `$100–160` → `130.0`

#### Year Validation

* Ensures valid integer format

#### Error Handling

* Invalid rows are skipped or logged
* Pipeline does not crash on bad data

---

### 3. Data Enrichment (OMDb API)

For each valid movie:

* Query OMDb using Title + Year

Extracted fields:

* Plot
* Metascore
* IMDb Rating
* Director
* Language

#### Handling Missing Data

* `"N/A"` values are converted to `None`
* Missing API results do not remove rows

---

### 4. Data Consistency Check

Compares:

* Wikipedia Year vs OMDb Year

Creates `Match_Status`:

* **Verified** → Year difference ≤ 1
* **Mismatch** → Year difference > 1
* **Not Found** → No OMDb data

---

### 5. Final Dataset & Categorization

A Pandas DataFrame is created with cleaned data.

#### Loss Categories:

* **Catastrophic** → Loss ≥ 100M
* **Severe** → 50M ≤ Loss < 100M
* **Moderate** → Loss < 50M

---

## 📁 Output

Final dataset saved as:

```
box_office_failures.csv
```

### Columns:

* Title
* Year
* Director
* Language
* Budget_Millions
* Loss_Millions
* Loss_Category
* IMDb_Rating
* Metascore
* Match_Status

---

## ⚙️ How to Run

1. Clone the repository
2. Install dependencies:

```
pip install beautifulsoup4 requests pandas pydantic
```

3. Add your OMDb API key:

```python
API_KEY = "your_api_key_here"
```

4. Run the notebook or script

