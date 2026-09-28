## Data cleaning decisions
 
Within the dataset, there are 7,043 rows, 21 columns, and expected churn counts. The only missing values are 11 TotalCharge entries, which belong to zero tenure customers and those entries were imputed with 0.0. There was no action taken to remove outliers, all rows in the data were finite numeric values. Scaling was learned on the training data and applied to the validation and test sets for the three numeric input columns. The four categorical columns were one-hot-encoded before running the logistic regression model. The boosted decision tree model used the same inputs as the logistic regression model.

## Partitions and training-only fitting 

The 7,043 customers that are represented in the data were split into a training (60%), validation(20%), and test set(20%). The data was split so that each partition had an equivalent churn rate of 26.54%. There was no overlap between rows in partitions, each row was assigned to one and only one partition. The scaling, encoding, and fitting of each model was only performed on and using the data from the training set.

## The three methods and their inputs 

The methods that are going to be compared and investigated in this project are as explained below. 
Contract rule: Assigns each customer the churn rate of their contract type from the training data (there are only three possible scores).

Logistic regression: combines the input columns, learns each of their weights, and creates a single churn probability.
Boosted trees: builds 100 small decision trees, each correcting the downfalls of the previous, and combines them into a churn probability.
Both the logistic regression and boosted trees model use the same seven columns as inputs, tenure, MonthlyCharges, TotalCharges, Contract, InternetService, PaperlessBilling, and PaymentMethod. The contract rule uses only the Contract column. For the linear regression model, inputs must be numeric, the numeric columns are scaled and categorical are one-hot-encoded into numerical factors.

## The validation choice is the boosted trees model (recorded September 27, 2026, before final evaluation). 

The boosted trees model (AUC of 0.8456) surpasses both other methods - the contract rule model (AUC 0.7427) and the logistic regression model (AUC 0.8384). Comparing the top 20% observed churn, boosted trees captured 183 churners out of the 281 list, compared to the logistic regression which captured 172 churners. The boosted tree model would include an extra 11 people on Devon’s list who actually churned compared to the next best model. Therefore based on the validation set, the choice is the boosted trees model. However, the gap between boosted trees and logistic regression is small and the final test will show whether this margin will hold true.

## Final-test comparison of the three methods

The results of the final test evaluation reported almost identical performances between the logistic regression and boosted trees models. The respective AUC for the contracts rule, logistic regression and boosted tree models were - 0.7373, 0.8472, and 0.8497. Boosted trees slightly surpassed logistic regression in AUC by 0.0025. The observed churn for each model per the 281 (top 20% list) were: contract list (112/281), logistic regression (195/281), and boosted trees (195/281). The logistic regression and boosted tree models outperformed the contract list model. Furthermore, the logistic regression and boosted tree models had a narrower gap in AUC and identical performances on the top 20% list observed churn.

## Uncertainty 

For the next evaluation, the data is bootstrapped by redrawing out of the pool of 1,409 test customers 1,000 times. This is designed to include easier and harder variations of customers so every method's AUC rises and falls over different samples. 

Pairwise comparison was used to ensure that both methods can be accurately compared since they are being scored on the same customers and subtracted. The goal was to control for the "difficulty" of the data, create variation and understand the true gap between methods. 

Under bootstrap, the 95% confidence intervals of AUC for each method were: contract list (0.7163 to 0.7557), logistic regression (0.8254 to 0.8700), and boosted trees (0.8283 to 0.8732). The 95% intervals for pairwise comparison between both methods using contract rule as baseline was: logistic regression - contract list (+0.0932 to +0.1285)and boosted trees - contract list (+0.0959 to +0.1311). Neither of the pairwise comparisons cross zero, meaning that under none of the bootstraps did the contract list perform better in AUC. We can be very confident that even the absolute unluckiest of bootstrap draws will not lead the contract list method to perform better than either of the others in AUC. 

When comparing Trees - Logistic, the 95% interval does contain zero and is −0.0049 to +0.0102. The majority of the interval favors the boosted decision trees, but some of the interval covers the negative and suggests that under certain bootstraps the logistic regression method will outperform in AUC. However, these intervals still are only able to create variation in the data that has already been observed. These intervals are limited by the current data and would change with any update in data or future trend in customer behavior. 

## Value scenarios

| Method | r | s | Net value per 1,000 contacts |
|---|---:|---:|---:|
| Contract | 0.398577 | 10% | -$3,569.40 |
| Contract | 0.398577 | 15% | -$2,254.09 |
| Contract | 0.398577 | 20% | -$938.79 |
| Logistic | 0.693950 | 10% | -$1,619.93 |
| Logistic | 0.693950 | 15% | $670.11 |
| Logistic | 0.693950 | 20% | $2,960.14 |
| Boosted trees | 0.693950 | 10% | -$1,619.93 |
| Boosted trees | 0.693950 | 15% | $670.11 |
| Boosted trees | 0.693950 | 20% | $2,960.14 |

Hand check of the first row: 
(Trees, s = 10%) : 0.398577×0.10×66 = 2.6306082; subtract 6.20 = - 3.5693918; multiply by 1000 = $3569.39; rounding different from codex result by $0.01

S represents the save rate, which is the share of customers who would have been churners that the retention call converts into a customer. The contract list model results in a net loss under every value of s tested. Both of the logistic regression and boosted tree models also have a net loss at an s value of 10% but show net profit at s = 15% and above. The break even value for the save rate is 13.54% for both logistic regression and boosted trees,since the 20% churn rate was discovered to be equal for both models. The break even rate is the point at which the money saved by the campaign was equal to the costs of it. If the real s is above 13.5%, the campaign pays off but if it is under then the campaign will lose money. 

Based on the above findings, I would suggest to Devon that further investigation, experimentation and analysis would be useful before implementing either model/campaign. Since it is clearly outlined that there is no measurement or data collected on what the true value of s could possibly be, I would not recommend blindly proceeding into a full initial implementation. 
My suggestion would be to start with a limited pilot experiment on a much smaller scale. I would implement the boosted tree model and observe the true value of s by randomly splitting customers into a call/holdout group, observe the behavior of both groups over a fixed period of time (1 month), compare the churn rates, and then make a decision based on the observed s compared to the breakeven rate.

## Calibration

| Scope / probability group | Customers | Mean predicted | Observed churn |
|---|---:|---:|---:|
| Overall test set | 1,409 | 27.05% | 26.54% |
| Top-20% contact list | 281 | 64.79% | 69.40% |
| 0.0–0.2 | 724 | 7.96% | 7.18% |
| 0.2–0.4 | 272 | 29.78% | 25.37% |
| 0.4–0.6 | 236 | 49.63% | 52.12% |
| 0.6–0.8 | 142 | 67.69% | 69.01% |
| 0.8–1.0 | 35 | 83.45% | 91.43% |

A well calibrated model is predicting probabilities of observed churn rates that are consistently close to the observed values in each category. The boosted tree mean predicted values versus observed churn values are very accurate when looking at the test set from an overall view (mean prediction was 27.05% compared to observed 26.54%). 

However, when looking into the top 20% contact list (281 customers), the mean predicted value is 64.79% compared to an observed value of 69.40%. The model is underestimating the true churn rate of these customers. There is also a large difference between the mean predicted churn rate (83.45%) versus the observed churn (91.43%) for the 35 customers who scored in the 0.8-1.0 probability range, which is the largest discrepancy between values in the dataset but is not supported by a large enough number of data points to suggest a calibration issue.

## Q4 comparison table

| Method | Final-test AUC | ΔAUC vs. contract rule | 95% interval for ΔAUC | Carry forward? | Reason |
|---|---:|---:|---|---|---|
| Contract rule | 0.7373 | — | 0.7163 to 0.7557 |  |  |
| Logistic regression | 0.8472 | +0.1099 | +0.0932 to +0.1285 |  |  |
| Boosted trees | 0.8497 | +0.1124 | +0.0959 to +0.1311 |  |  |
