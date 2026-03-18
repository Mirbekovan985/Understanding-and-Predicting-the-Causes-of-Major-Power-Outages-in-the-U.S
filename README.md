# Predicting Power Outage Duration in the U.S.

Project for DSC 80 at UC San Diego  
By Nuraiym Mirbekova

## Introduction

In this project, I analyzed a dataset of major power outages in the United States from 2000 to 2016. These outages are defined as events that impacted at least 50,000 customers or caused significant energy demand loss.

Understanding power outages is important because they affect millions of people and have major economic and safety consequences. Being able to predict outage duration can help energy companies allocate resources more efficiently and reduce recovery time.

### Research Question

My central question is:

**What factors best predict the duration of a power outage?**

---

### Dataset Overview

The dataset contains **1534 rows**, where each row represents a power outage event.

The key columns used in this analysis include:

- `OUTAGE.DURATION`: Duration of the outage (minutes)  
- `CAUSE.CATEGORY`: Cause of the outage  
- `MONTH`: Month of occurrence  
- `U.S._STATE`: State where outage occurred  
- `TOTAL.CUSTOMERS`: Number of customers in the state  
- `TOTAL.SALES`: Electricity consumption  
- `TOTAL.PRICE`: Electricity price  

These features provide information about timing, location, cause, and scale of outages.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The first step is to clean the data to ensure it is suitable for analysis.

#### Loading and Initial Filtering

I loaded the dataset from an Excel file and removed non-data rows by keeping only rows where the `OBS` column is numeric. I also dropped the `variables` column, as it does not contain useful information for analysis.

---

#### Selecting Relevant Columns

Next, I selected a subset of relevant columns related to time, location, causes, severity, climate, and electricity usage. These include:

- Time: `YEAR`, `MONTH`, `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`
- Location: `U.S._STATE`, `CLIMATE.REGION`
- Causes: `CAUSE.CATEGORY`, `CAUSE.CATEGORY.DETAIL`
- Severity: `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, `DEMAND.LOSS.MW`
- Climate: `ANOMALY.LEVEL`
- Context: `TOTAL.PRICE`, `TOTAL.SALES`, `TOTAL.CUSTOMERS`, `POPPCT_URBAN`, `POPDEN_URBAN`, `AREAPCT_URBAN`

This step reduces noise and ensures the dataset focuses on meaningful variables.

---

#### Data Type Conversion

I converted several columns to numeric format, including:

- `YEAR`, `MONTH`, `ANOMALY.LEVEL`
- `CUSTOMERS.AFFECTED`, `TOTAL.CUSTOMERS`
- `POPPCT_URBAN`

Invalid values were coerced into missing values (`NaN`) to ensure consistency.

---

#### Creating Timestamp Features

I combined the date and time columns to create full timestamp features:

- `OUTAGE.START` = `OUTAGE.START.DATE` + `OUTAGE.START.TIME`
- `OUTAGE.RESTORATION` = `OUTAGE.RESTORATION.DATE` + `OUTAGE.RESTORATION.TIME`

This allows for accurate time-based calculations.

---

#### Computing Outage Duration

I computed outage duration in minutes using:

- `OUTAGE.DURATION_MIN = OUTAGE.RESTORATION − OUTAGE.START`

Any values where the duration was less than or equal to 0 were treated as invalid and replaced with `NaN`, since outages cannot have negative or zero duration.

---

#### Final Dataset

I created a cleaned dataset `analysis_df` containing key variables:

- Outcome: `OUTAGE.DURATION_MIN`
- Time: `YEAR`, `MONTH`
- Impact: `CUSTOMERS.AFFECTED`, `TOTAL.CUSTOMERS`
- Location: `U.S._STATE`, `POPPCT_URBAN`
- Climate: `ANOMALY.LEVEL`, `CLIMATE.REGION`
- Causes: `CAUSE.CATEGORY`, `CAUSE.CATEGORY.DETAIL`

This cleaned dataset is used for all subsequent analysis.

---

### Univariate Analysis

I examined the distributions of key variables.

<iframe src="assets/duration_plot.html" width="800" height="500"></iframe>

The distribution of outage duration is highly right-skewed, with most outages being relatively short and a small number lasting significantly longer.

---

<iframe src="assets/customers_plot.html" width="800" height="500"></iframe>

The number of customers affected is also highly skewed, indicating that most outages impact relatively few people, while a few large outages affect many customers.

---

### Bivariate Analysis

I explored relationships between pairs of variables.

<iframe src="assets/scatter_plot.html" width="800" height="500"></iframe>

There is a weak positive relationship between the number of customers affected and outage duration, though the variability is high.

---

<iframe src="assets/box_plot.html" width="800" height="500"></iframe>

Outage duration varies across cause categories, with some categories (such as severe weather) tending to have longer durations.

---

### Interesting Aggregates

I examined the number of outages per cause category.

