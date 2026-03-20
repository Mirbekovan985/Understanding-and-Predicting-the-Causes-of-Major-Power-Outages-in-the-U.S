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

## Hypothesis Testing

### Research Question

Do power outages caused by severe weather last longer, on average, than outages caused by other factors?

---

### Hypotheses

- **Null Hypothesis (H₀):**  
  The average outage duration for severe weather outages is equal to the average outage duration for non-severe weather outages.

- **Alternative Hypothesis (H₁):**  
  The average outage duration for severe weather outages is greater than that for non-severe weather outages.

---

### Test Statistic and Method

To test this hypothesis, I conducted a **permutation test**.

The test statistic used was the **difference in means**:

- Mean outage duration for severe weather outages  
- minus mean outage duration for non-severe weather outages  

This statistic is appropriate because it directly measures the difference in average outage duration between the two groups, which aligns with the research question.

The significance level was set to **α = 0.05**.

---

### Results

- **Observed difference in means:** 2537.81 minutes  
- **p-value:** 0.0  

The permutation distribution of the test statistic is shown below:

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed statistic lies far in the right tail of the permutation distribution, indicating that such a large difference would be extremely unlikely under the null hypothesis.

---

### Conclusion

Since the p-value is less than the significance level (0.05), we **reject the null hypothesis**.

This provides strong evidence that outages caused by severe weather tend to last longer, on average, than outages caused by other factors.

However, this result does not prove that severe weather definitively causes longer outages; rather, it suggests a statistically significant association between outage cause and duration.

---

### Justification

A permutation test was an appropriate choice because:
- It does not assume a specific distribution of outage durations  
- It is robust to skewed data, which is common in duration data  
- It directly tests the relationship of interest by comparing group labels  

Using the difference in means as the test statistic allows for an intuitive and interpretable comparison between the two groups.

## Framing a Prediction Problem

### Prediction Problem

The goal of this model is to **predict the duration of a power outage** based on information available at the time the outage occurs.

This is a **regression problem**, since the response variable is continuous.

---

### Response Variable

The response variable is:

- `OUTAGE.DURATION`

This variable was chosen because outage duration is a key measure of outage severity and impact. Predicting it can help utilities better prepare responses and allocate resources more effectively.

---

### Features Used

The features used for prediction are:

- `CAUSE.CATEGORY`  
- `MONTH`  
- `U.S._STATE`  

These features were selected because they are **known at the time of prediction**, meaning:
- The cause category is identified when the outage begins  
- The month is known  
- The location (state) is known  

No information that would only be available after the outage ends was used, ensuring the model reflects a realistic prediction setting.

---

### Model and Preprocessing

To build the model:

- Categorical variables (`CAUSE.CATEGORY`, `U.S._STATE`) were **one-hot encoded**  
- The numeric variable (`MONTH`) was used directly  

A **Random Forest Regressor** was used within a pipeline to combine preprocessing and modeling.

This model was chosen because:
- It can capture **nonlinear relationships**
- It handles categorical features well after encoding
- It is robust to outliers and does not require strong distributional assumptions  

---

### Evaluation Metrics

The model was evaluated using:

- **Root Mean Squared Error (RMSE)**  
- **R² (coefficient of determination)**  

Results:
- RMSE: 7110.74  
- R²: 0.176  

---

### Justification of Metrics

- **RMSE** was chosen because it measures the average prediction error in the same units as the response variable (minutes), making it interpretable. It also penalizes large errors more heavily, which is important for outage duration.

- **R²** was used to measure how much of the variability in outage duration is explained by the model.

---

### Interpretation

The relatively high RMSE and low R² suggest that outage duration is difficult to predict using only the selected features. This indicates that other factors — such as infrastructure conditions, response time, or weather severity — likely play a significant role in determining outage duration.

Despite this, the model provides a baseline for understanding how available features relate to outage duration and can be improved with additional data.

## Baseline Model

### Model Description

To establish a baseline, I trained a **Linear Regression model** to predict outage duration using a subset of available features.

The model was implemented using an sklearn **Pipeline**, which includes both preprocessing and model training steps.

---

### Features Used

The following features were included:

- `CAUSE.CATEGORY` (categorical, nominal)  
- `U.S._STATE` (categorical, nominal)  
- `MONTH` (numerical, discrete/ordinal)  

These features were selected because they are available at the time of prediction and provide basic contextual information about the outage.

---

### Preprocessing

- **Categorical features** (`CAUSE.CATEGORY`, `U.S._STATE`) were transformed using **One-Hot Encoding**  
  - This converts each category into binary indicator variables  
  - `handle_unknown='ignore'` ensures the model can handle unseen categories in test data  

- **Numerical feature** (`MONTH`) was passed through without transformation  

All preprocessing steps were combined using a **ColumnTransformer** within the pipeline.

---

### Model Performance

The model was evaluated on the **test set** using the following metrics:

- **RMSE:** 7239.93  
- **R²:** 0.146  

---

### Evaluation

The baseline model has relatively **high error (RMSE)** and a **low R² value**, indicating that it explains only a small portion of the variability in outage duration.

This suggests that:
- The relationship between the selected features and outage duration is weak  
- Important predictive factors are likely missing from the model  

---

### Conclusion

Overall, this baseline model is **not very strong**, but it serves as an important starting point. It provides a benchmark against which more complex models can be compared.

Future improvements could include:
- Adding more informative features  
- Engineering new features  
- Using more flexible models that can capture nonlinear relationships

## Final Model  

### Overview  

To improve upon the baseline model, I developed a more advanced regression model that incorporates additional feature engineering and a more flexible algorithm. The goal was to better capture the factors that influence outage duration and improve predictive performance.

---

### Feature Engineering  

In addition to the baseline features (`CAUSE.CATEGORY`, `U.S._STATE`, `MONTH`), I introduced several new features:

- **`AFFECTED_RATIO`** = `CUSTOMERS.AFFECTED / TOTAL.CUSTOMERS`  
  - Measures the proportion of customers affected  
  - Higher values indicate more severe outages, which may take longer to restore  

- **Log-transformed features**:  
  - `LOG_CUSTOMERS_AFFECTED`  
  - `LOG_TOTAL_CUSTOMERS`  
  - These reduce skew in highly right-skewed variables and help the model learn relative differences  

- **`SEVERITY_IMPACT` (interaction feature)**  
  - `AFFECTED_RATIO × LOG_CUSTOMERS_AFFECTED`  
  - Captures combined severity and scale of outages  

- **Seasonal features**:  
  - `IS_WINTER` (December–February)  
  - `IS_SUMMER` (June–August)  
  - Capture seasonal effects such as extreme weather conditions  

- **Target transformation**:  
  - Applied `log1p` to `OUTAGE.DURATION`  
  - Clipped extreme values at the 99th percentile to reduce noise  

---

### Model Choice  

I used a **Gradient Boosting Regressor** inside an sklearn Pipeline.

Reasons:
- Captures nonlinear relationships  
- Handles complex interactions between features  
- Performs well on tabular data  

---

### Hyperparameter Tuning  

I tuned the following hyperparameters:

- `n_estimators`  
- `learning_rate`  
- `max_depth`  

These control model complexity and the bias-variance tradeoff.

Final chosen values:
- `n_estimators = 400`  
- `learning_rate = 0.04`  
- `max_depth = 5`  

---

### Model Pipeline  

- One-hot encoding:
  - `CAUSE.CATEGORY`
  - `U.S._STATE`  

- Numerical features:
  - `MONTH`
  - `AFFECTED_RATIO`
  - `LOG_CUSTOMERS_AFFECTED`
  - `LOG_TOTAL_CUSTOMERS`
  - `SEVERITY_IMPACT`
  - `IS_WINTER`
  - `IS_SUMMER`

- Model:
  - Gradient Boosting Regressor  

---

### Model Performance  

- **RMSE:** 3088.30  
- **R²:** 0.170  

---

### Comparison to Baseline  

Baseline model:
- RMSE: 7239.93  
- R²: 0.146  

Final model:
- RMSE: 3088.30  
- R²: 0.170  

The final model significantly improves performance by:
- Reducing RMSE by more than half  
- Increasing R², meaning it explains more variance in outage duration  

---

### Conclusion  

The improved performance suggests that:
- Outage severity (relative impact) is an important predictor  
- Feature engineering plays a critical role in model performance  
- Nonlinear models better capture relationships in the data  

This final model provides a much stronger and more realistic prediction of outage duration compared to the baseline model.

## Fairness Analysis  

### Groups  

To evaluate fairness, I compared model performance across two groups:

- **Group X:** Winter outages (`IS_WINTER = 1`)  
- **Group Y:** Non-winter outages (`IS_WINTER = 0`)  

This grouping was chosen because outages during winter may involve extreme weather conditions (e.g., storms, snow), which could make them harder to predict.

---

### Evaluation Metric  

Since this is a regression problem, I used:

- **Root Mean Squared Error (RMSE)**  

RMSE measures the average prediction error in minutes and penalizes large errors more heavily.

---

### Hypotheses  

- **Null Hypothesis (H₀):**  
  The model is fair. The RMSE for winter and non-winter outages is the same, and any observed difference is due to random chance.

- **Alternative Hypothesis (H₁):**  
  The model is unfair. The RMSE for winter outages is higher than for non-winter outages.

---

### Test Statistic  

Difference in RMSE = RMSE (Winter) − RMSE (Non-Winter)

A positive value would indicate worse performance on winter outages.

---

### Significance Level  

- **α = 0.05**

---

### Results  

- **RMSE (Winter):** 2879.69  
- **RMSE (Non-Winter):** 3146.74  
- **Observed Difference:** -267.05  
- **p-value:** 0.49  

---

### Conclusion  

Since the p-value (0.49) is greater than the significance level (0.05), we **fail to reject the null hypothesis**.

There is no statistically significant evidence that the model performs worse on winter outages compared to non-winter outages.

---

### Interpretation  

Interestingly, the model actually performs slightly **better** on winter outages (lower RMSE), though this difference is not statistically significant.

This suggests that the model generalizes similarly across seasonal conditions and does not exhibit strong evidence of unfairness between winter and non-winter outages.
