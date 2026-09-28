To: Devon Achebe, VP of Customer Retention, Summit Telecom 
From: Anna Reimers
Date: September 28, 2026 
Re: Customer Retention Model Recommendations

Devon, 

After review of the data and comparisons between the three methods, I suggest a small pilot implementation of the boosted decision tree model. In initial validation testing, the boosted tree model surpassed both the contract rule(0.7427) and logistic regression model(0.8384) with an AUC of 0.8456. My initial choice was the boosted decision tree model. However, the final evaluation test closed the gap between the logistic regression and boosted tree model, with AUCs of 0.8472 (logistic) and 0.8497 (trees). 

We also consider the “top 20% contact list” metric - the proportion of people which the model chose to include on the 281 person list who actually churn - an appropriate measure of the model's performance for the trend we are interested in. The logistic regression and boosted decision tree model perform equivalently here, both including 195 people who truly churn out of 281, and surpassing the contract rule’s performance of 112/281. 

For additional confidence: a 95% interval for pairwise comparison between both methods and the contract rule as baseline was: logistic regression - contract list (+0.0932 to +0.1285)and boosted trees - contract list (+0.0959 to +0.1311). Neither of the comparisons include zero, meaning that under no bootstrap did the contract list outperform in AUC.

When comparing Trees - Logistic, the 95% interval contains zero: −0.0049 to +0.0102. The majority of the interval favors the boosted decision tree, yet the remaining area covers the negative. Under some bootstraps the logistic regression AUC outperformed the boosted decision tree. These intervals are limited to capturing only the variation in the available data. 

My recommendation remains to implement a boosted decision tree model, with the following stipulations. Consider the variable S to represent the “save rate” – the percentage of customers who are converted from a “would be churner” to a customer because of the campaign. The break even value (point at which the money made by the campaign equals the price of the campaign) for s is 13.54% for logistic and tree models, since the 20% churn rate was equivalent for both. If the true s is above 13.5%, the campaign pays off, but if it is below, money will be lost. Since there is no current measurement or accurate way to predict the true value of s, I would not recommend blindly proceeding into a full-scale implementation. 

I suggest a limited pilot implementation to avoid the risk of large loss. I would implement the boosted tree model and observe the true value of s by randomly splitting customers into a call/holdout group, observe the behavior of both groups over a fixed time (1 month), compare churn rates, and make a decision based on the observed value of s (is it greater than 13.5%). 
