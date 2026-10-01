# E-Commerce Data Engineering Lakehouse

**Modern Data Engineering for AI Systems — SDAIA Academy Final Project**

An end-to-end Data Engineering Lakehouse pipeline for processing e-commerce transaction data using **PySpark** and **Delta Lake**.

The project demonstrates how transaction data can be ingested, validated, quality-checked, separated into trusted and invalid records, stored using Delta Lake, and transformed into business analytics.

---

## Project Overview

E-commerce transaction data can contain data-quality issues such as:

* Missing values
* Invalid prices
* Zero quantities
* Duplicate transactions
* Invalid transaction types
* Cancellation transactions

This project implements a controlled data engineering pipeline that identifies these issues, separates invalid records into a **Quarantine** layer, and stores trusted data in a **Silver Delta Lake** layer for downstream analytics.

---

## Project Objectives

The main objectives are to:

* Build an end-to-end data engineering pipeline.
* Ingest structured e-commerce transaction data.
* Store raw data in a Bronze Delta Lake layer.
* Apply automated data-quality validation.
* Separate failed records into a Quarantine layer.
* Transform trusted records into a Silver Delta Lake layer.
* Generate business analytics from trusted data.
* Demonstrate Delta Lake capabilities and transaction history.
* Build a reliable data foundation for future AI/ML applications.

---

## Architecture

<img src="architecture/architecture.png" width="700">

The pipeline follows a controlled data flow:

```text
Embedded Transaction Data
          │
          ▼
   Bronze Delta Lake
          │
          ▼
 Data Quality Checks
          │
          ▼
     Quality Gate
       /       \
    PASS       FAIL
     │           │
     ▼           ▼
  Silver     Quarantine
  Delta         Delta
     │
     ▼
 Business Analytics
```

---

## Data Source

The project uses the **UCI Online Retail dataset** as the reference dataset.

The original dataset contains transactional records from a UK-based online retailer.

For this project, a **small embedded sample dataset is defined directly inside the notebook**. This allows the complete pipeline and data-quality scenarios to be demonstrated without requiring an external data file during execution.

The notebook therefore contains the sample transaction data used by the pipeline directly in the PySpark workflow.

### Reference Dataset

* UCI Machine Learning Repository — Online Retail Dataset
* Kaggle mirror of the Online Retail dataset

---

## Data Engineering Pipeline

### 1. Data Ingestion

The pipeline uses **PySpark** to create and process the transaction DataFrame.

The embedded sample contains transaction fields such as:

* `InvoiceNo`
* `StockCode`
* `Description`
* `Quantity`
* `InvoiceDate`
* `UnitPrice`
* `CustomerID`
* `Country`

---

### 2. Bronze Layer

The raw input data is stored as a **Delta Lake Bronze table**.

The Bronze layer preserves the ingested transaction data before trusted transformations are applied.

```text
Raw Transaction Data
        ↓
Bronze Delta Lake
```

---

### 3. Data Quality Validation

The project applies multiple automated data-quality checks.

#### Completeness

Critical fields are checked for missing values:

* `InvoiceNo`
* `StockCode`
* `Quantity`
* `UnitPrice`
* `InvoiceDate`
* `Country`

`CustomerID` is not treated as mandatory because the source dataset can contain missing customer identifiers.

#### Validity

The pipeline checks for:

* Null `UnitPrice`
* Negative `UnitPrice`
* Null `Quantity`
* Zero `Quantity`

#### Uniqueness

Duplicate transaction records are identified using the transaction columns.

#### Business Rules

The pipeline validates transaction behavior:

* Standard invoices should have positive quantities.
* Cancellation invoices beginning with `C` should have negative quantities.

---

## Quality Gate

After the data-quality checks, the pipeline creates a **Quality Gate** that determines whether records can enter the trusted data layer.

```text
Data Quality Checks
        │
        ▼
   Quality Gate
     /       \
   PASS      FAIL
    │          │
    ▼          ▼
 Silver    Quarantine
```

Records that fail the validation rules are routed to the **Quarantine** layer instead of being included in trusted analytics.

This prevents invalid records from directly affecting downstream business analysis.

---

## Delta Lake Layers

### Bronze

Contains the ingested raw transaction data.

```text
/tmp/delta/bronze
```

### Silver

Contains validated and transformed trusted transaction data.

```text
/tmp/delta/silver
```

### Quarantine

Contains records that fail data-quality validation.

```text
/tmp/delta/quarantine
```

---

## Trusted Data Transformation

The trusted dataset is created after applying the required validation rules.

The pipeline also derives additional fields.

### Revenue

```text
Revenue = Quantity × UnitPrice
```

### TransactionType

Transactions are classified as:

* `Sale`
* `Cancellation`

based on the `InvoiceNo`.

### OrderDate

The transaction timestamp is converted into a date field for analytics.

---

## Business Analytics

Business analytics are generated from the trusted Silver data.

### Sales KPIs

The current implementation calculates:

* Total Orders
* Unique Customers
* Total Sales Revenue
* Average Line Revenue

### Revenue by Country

Provides:

* Revenue by country
* Number of distinct invoices

### Revenue by Product

Provides:

* Revenue by product
* Quantity sold by product

### Monthly Revenue

Aggregates sales revenue by month to identify monthly trends.

The current implementation calculates **Sales Revenue** from transactions classified as `Sale`. It does not implement a separate net-revenue calculation.

---

## Data Quality Test Cases

The notebook includes both passing and failing examples.

### PASS Examples

* Valid sales transactions
* Valid cancellation transactions

### FAIL Examples

The pipeline tests scenarios such as:

* Duplicate records
* Negative quantity on a standard invoice
* Negative `UnitPrice`

These scenarios demonstrate how the **Quality Gate** identifies invalid records and routes them to the **Quarantine** layer.

---

## Technologies

| Technology   | Purpose                                    |
| ------------ | ------------------------------------------ |
| Python       | Programming language                       |
| PySpark      | Distributed data processing                |
| Apache Spark | Data processing engine                     |
| Delta Lake   | Reliable data storage and table management |
| Google Colab | Development environment                    |
| GitHub       | Version control and project sharing        |

---

## Project Structure

```text
E-Commerce-Data-Engineering-Lakehouse/
│
├── architecture/
│   └── architecture.png
│
├── E-ecommerce_data_engineering.ipynb
│
├── README.md
│
└── requirements.txt
```

---

## Installation

Install the required packages:

```bash
pip install pyspark delta-spark
```

Or install them using:

```bash
pip install -r requirements.txt
```

---

## How to Run

### Google Colab

The project notebook is available here:

**E-Commerce Data Engineering Notebook**

[Open Notebook on GitHub](https://github.com/lameesalghamlas/E-Commerce-Data-Engineering-Lakehouse/blob/main/E-ecommerce_data_engineering.ipynb)

The notebook can be opened and executed using Google Colab.

### Local Environment

Clone the repository:

```bash
git clone https://github.com/lameesalghamlas/E-Commerce-Data-Engineering-Lakehouse.git
```

Navigate to the project:

```bash
cd E-Commerce-Data-Engineering-Lakehouse
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Then open:

```text
E-ecommerce_data_engineering.ipynb
```

and run the notebook cells sequentially.

---

## Delta Lake History

The project demonstrates Delta Lake table history using:

```sql
DESCRIBE HISTORY delta.`{SILVER_PATH}`
```

This allows the pipeline to inspect the history of changes to the Silver Delta table.

---

## Project Deliverables

The project includes:

* PySpark data ingestion pipeline
* Bronze Delta Lake layer
* Automated data-quality checks
* Quality Gate
* Quarantine layer
* Silver Delta Lake layer
* Data transformations
* Business analytics
* Delta Lake history
* Project architecture
* GitHub documentation

---

## Future Improvements

Potential extensions for the project include:

* Implementing a dedicated Gold analytics layer.
* Adding incremental data processing.
* Adding automated data-quality monitoring.
* Adding orchestration using Apache Airflow or a cloud orchestration service.
* Connecting the pipeline to a cloud data platform.
* Adding machine-learning features based on trusted transaction data.
* Building an AI/RAG application on top of the trusted data.

These are future extensions and are **not part of the current implementation**.

---

## References

* UCI Machine Learning Repository — Online Retail Dataset
* Delta Lake Documentation
* Apache Spark / PySpark Documentation
* Google Colab

---

## Project

**E-Commerce Data Engineering Lakehouse**

**Modern Data Engineering for AI Systems — SDAIA Academy Final Project**

[View Project Repository](https://github.com/lameesalghamlas/E-Commerce-Data-Engineering-Lakehouse)

[Open Project Notebook](https://github.com/lameesalghamlas/E-Commerce-Data-Engineering-Lakehouse/blob/main/E-ecommerce_data_engineering.ipynb)


Modern Data Engineering for AI Systems — SDAIA Academy Final Project

Built with Python, PySpark, Apache Spark, and Delta Lake.


SDAIA Academy link: https://github.com/SDAIAAcademy

