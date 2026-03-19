# Analyzing Power Outages  
Project for DSC 80 at UCSD  

By Nuraiym Mirbekova  

---

## Introduction  

In this project, I analyzed a dataset of major power outages in the United States from January 2000 to July 2016. These outages are defined as events that impacted at least 50,000 customers or resulted in significant electricity demand loss.

The dataset includes information about outage events, as well as:
- geographic characteristics  
- climate conditions  
- electricity usage  
- population and urbanization  

I began by cleaning the dataset and performing exploratory data analysis (EDA) to better understand the data. Then, I explored relationships between outage severity and different contributing factors.

Finally, I addressed my research question:

> **What factors most influence the duration of major power outages?**

To answer this, I built a predictive model to estimate outage duration.

---

## Dataset  

The original dataset contains over 1,500 outage events. I focused on the following columns:

### Time Information  
- `YEAR`  
- `MONTH`  
- `OUTAGE.START.DATE`  
- `OUTAGE.START.TIME`  
- `OUTAGE.RESTORATION.DATE`  
- `OUTAGE.RESTORATION.TIME`  

### Location / Region  
- `U.S._STATE`  
- `NERC.REGION`  
- `CLIMATE.REGION`  

### Cause of Outage  
- `CAUSE.CATEGORY`  
- `CAUSE.CATEGORY.DETAIL`  

### Severity / Impact  
- `OUTAGE.DURATION`  
- `CUSTOMERS.AFFECTED`  
- `DEMAND.LOSS.MW`  

### Climate  
- `ANOMALY.LEVEL`  

### Electricity & Population  
- `TOTAL.PRICE`  
- `TOTAL.SALES`  
- `TOTAL.CUSTOMERS`  
- `POPPCT_URBAN`  
- `POPDEN_URBAN`  
- `AREAPCT_URBAN`  

---

## Data Cleaning and Exploratory Data Analysis  

### Cleaning  

I focused on the following columns:

`YEAR`, `MONTH`,  
`U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`,  
`ANOMALY.LEVEL`,  
`OUTAGE.START.DATE`, `OUTAGE.START.TIME`,  
`OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`,  
`CAUSE.CATEGORY`, `CAUSE.CATEGORY.DETAIL`,  
`OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`,  
`TOTAL.PRICE`, `TOTAL.SALES`, `TOTAL.CUSTOMERS`,  
`POPPCT_URBAN`, `POPDEN_URBAN`, `AREAPCT_URBAN`

<iframe
    src="assets/preview_table.html"
    width="100%"
    height="400"
    frameborder="0">
</iframe>

## Data Cleaning  

- I used the `OBS` column to remove non-data rows, then removed `OBS`  
- I dropped the `variables` column  
- I restricted the dataset to the columns listed above  


I created new features:

- `OUTAGE.START` (from date + time)  
- `OUTAGE.RESTORATION` (from date + time)  
- `OUTAGE.DURATION_MIN` (duration in minutes)  

I also removed invalid values where:
- `OUTAGE.DURATION_MIN <= 0`  

This resulted in a clean dataset called `analysis_df`.

---

## Exploratory Data Analysis  

### Univariate Analysis  

I analyzed the distributions of:

<iframe
    src="assets/outages_by_cause.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

<iframe
    src="assets/outage_duration_dist.html"
    width="800"
    height="600"
    frameborder="0">
</iframe>

These showed that outage duration and size are highly skewed.


---

### Bivariate Analysis  

I explored relationships between:

<iframe
  src="assets/customers_vs_duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/duration_by_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I found that larger outages tend to last longer, though the relationship is not very strong.

---

## Grouping and Aggregates  

I grouped the data to identify patterns:

- Grouped by `NERC.REGION` and computed averages of:
  - `OUTAGE.DURATION_MIN`  
  - `CUSTOMERS.AFFECTED`  

- Created pivot tables using:
  - `CLIMATE.REGION`  
  - `CAUSE.CATEGORY`  

<iframe
    src="assets/pivot_table.html"
    width="100%"
    height="400"
    frameborder="0">
</iframe>

This helped identify which regions and causes are associated with more severe outages.
