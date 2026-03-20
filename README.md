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

| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:-------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Central            |                   2 |                       1 |                   21 |           3 |               0 |              124 |                               6 |
| East North Central |                   0 |                       0 |                    9 |           1 |               1 |              104 |                               3 |
| Northeast          |                   4 |                       2 |                   81 |           1 |               3 |              168 |                               7 |
| Northwest          |                   2 |                       0 |                   27 |           1 |               2 |               29 |                               1 |
| South              |                   6 |                       0 |                   12 |           1 |              11 |              104 |                              21 |
| Southeast          |                   5 |                       0 |                    4 |           0 |               3 |              116 |                              15 |
| Southwest          |                   3 |                       1 |                   26 |           1 |               0 |                7 |                               7 |
| West               |                   7 |                       3 |                   17 |          26 |               0 |               57 |                              22 |
| West North Central |                   0 |                       0 |                    2 |           0 |               1 |                4 |                               0 |

This helped identify which regions and causes are associated with more severe outages.

## Missingness Analysis

### Missing Not At Random (MNAR)

One column that may be **Missing Not At Random (MNAR)** is `OUTAGE.DURATION`.

This is because the likelihood of missing outage duration values may depend on the duration itself. For example, outages that are extremely short or not properly recorded may be more likely to have missing duration values. Since the probability of missingness depends on the unobserved value, this fits the definition of MNAR.

To better understand this missingness (and potentially reclassify it as MAR), additional data would be useful. For instance, information about reporting practices, data collection procedures, or whether certain types of outages are systematically excluded could help explain why values are missing.

---

### Missingness Dependency

I analyzed whether the missingness of `OUTAGE.DURATION` depends on other variables using permutation tests.

First, I created an indicator column:
- `duration_missing`, which is `True` when `OUTAGE.DURATION` is missing

I then performed permutation tests comparing variability in missingness across groups.

---

### Dependent Relationship: CAUSE.CATEGORY

I tested whether missingness depends on `CAUSE.CATEGORY`.

- Observed statistic: (from notebook output)
- p-value: (from notebook output)

The permutation test produced a small p-value, indicating that the missingness of `OUTAGE.DURATION` **does depend on** `CAUSE.CATEGORY`.

This suggests that outages caused by certain factors are more likely to have missing duration values, indicating a relationship between missingness and an observed variable (consistent with MAR behavior).

<iframe
  src="assets/missingness_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot shows the distribution of permutation statistics, with the observed statistic marked in red. Since the observed value lies in the tail of the distribution, we reject the null hypothesis of independence.

---

### Independent Relationship: YEAR

I also tested whether missingness depends on `YEAR`.

- Observed statistic: 0.0002975531346297562
- p-value: 0.48

The permutation test resulted in a large p-value, indicating that the missingness of `OUTAGE.DURATION` **does not depend on** `YEAR`.

<iframe
  src="assets/missingness_year.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In this case, the observed statistic falls well within the permutation distribution, so we fail to reject the null hypothesis.

---

### Conclusion

Overall, the missingness of `OUTAGE.DURATION` appears to depend on some observed variables (such as `CAUSE.CATEGORY`) but not others (such as `YEAR`). While there is evidence of MAR behavior, the possibility of MNAR cannot be ruled out, since missingness may also depend on the unobserved duration values themselves.
