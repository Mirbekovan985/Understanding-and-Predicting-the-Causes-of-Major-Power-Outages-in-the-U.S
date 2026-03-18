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

---

OUTAGE.START  OUTAGE.RESTORATION  OUTAGE.DURATION_MIN  \
0 2011-07-01 17:00:00 2011-07-03 20:00:00               3060.0   
1 2014-05-11 18:38:00 2014-05-11 18:39:00                  1.0   
2 2010-10-26 20:00:00 2010-10-28 22:00:00               3000.0   
3 2012-06-19 04:30:00 2012-06-20 23:00:00               2550.0   
4 2015-07-18 02:00:00 2015-07-19 07:00:00               1740.0   

   CUSTOMERS.AFFECTED  TOTAL.CUSTOMERS U.S._STATE  POPPCT_URBAN  \
0             70000.0        2595696.0  Minnesota         73.27   
1                 NaN        2640737.0  Minnesota         73.27   
2             70000.0        2586905.0  Minnesota         73.27   
3             68200.0        2606813.0  Minnesota         73.27   
4            250000.0        2673531.0  Minnesota         73.27   

   ANOMALY.LEVEL      CLIMATE.REGION      CAUSE.CATEGORY  \
0           -0.3  East North Central      severe weather   
1           -0.1  East North Central  intentional attack   
2           -1.5  East North Central      severe weather   
3           -0.1  East North Central      severe weather   
4            1.2  East North Central      severe weather   

  CAUSE.CATEGORY.DETAIL    YEAR  MONTH  
0                   NaN  2011.0    7.0  
1             vandalism  2014.0    5.0  
2            heavy wind  2010.0   10.0  
3          thunderstorm  2012.0    6.0  
4                   NaN  2015.0    7.0

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
  src="assets//Users/outages_by_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets//Users/outage_duration_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

These showed that outage duration and size are highly skewed.

---

### Bivariate Analysis  

I explored relationships between:

<iframe
  src="assets//Users/customers_vs_duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets//Users/duration_by_cause.html"
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

CAUSE.CATEGORY      equipment failure  fuel supply emergency  \
CLIMATE.REGION                                                 
Central                           1.3                    0.6   
East North Central                0.0                    0.0   
Northeast                         1.5                    0.8   
Northwest                         3.2                    0.0   
South                             3.9                    0.0   

CAUSE.CATEGORY      intentional attack  islanding  public appeal  \
CLIMATE.REGION                                                     
Central                           13.4        1.9            0.0   
East North Central                 7.6        0.8            0.8   
Northeast                         30.5        0.4            1.1   
Northwest                         43.5        1.6            3.2   
South                              7.7        0.6            7.1   

CAUSE.CATEGORY      severe weather  system operability disruption  
CLIMATE.REGION                                                     
Central                       79.0                            3.8  
East North Central            88.1                            2.5  
Northeast                     63.2                            2.6  
Northwest                     46.8                            1.6  
South                         67.1                           13.5

This helped identify which regions and causes are associated with more severe outages.
