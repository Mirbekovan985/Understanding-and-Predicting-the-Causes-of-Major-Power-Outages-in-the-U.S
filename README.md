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
