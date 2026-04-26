**B1. Problem Formulation **
__________________________________________________________________________________________________________________
**Part a**
__________________________________________________________________________________________________________________
**Problem understanding:**
The retailer wants to maximize items sold by choosing the best promotion strategy for each store every month.

**Target Variable:**
The target variable is items_sold, which represents the number of items sold at a store in a given month.

**Candidate Input Features:**
The input features (independent variables) can include:

  Promotion-related: promotion_type
  Store characteristics: store_size, location_type
  Customer behavior: monthly_footfall, customer_demographics (if available)
  External factors: competition_density
  Temporal features: month, is_weekend, is_festival, day_of_week

**Type of Machine Learning Problem:**
This is a Supervised Learning — Regression Problem

**Justification:**
The target variable (items_sold) is a continuous numerical value, not a category thus can be measured effectively.
The goal is to predict a quantity (sales volume) based on input features.
Historical data with known outputs is available, which makes it supervised learning.
Thus, regression models (e.g., Linear Regression, Random Forest Regressor) are appropriate to be used.

__________________________________________________________________________________________________________________
Part b
__________________________________________________________________________________________________________________

**Items Sold (sles volume) is a Better Target than Revenue because of following reasons:**
- Avoids price distortion: Promotions like discounts or BOGO reduce price per item, so revenue may drop even when more units are sold. items_sold reflects true demand.
- Aligns with promotion goals: Marketing campaigns aim to increase volume, not necessarily immediate revenue, so items_sold directly measures success.
- More stable and comparable: Revenue fluctuates due to pricing, product mix, and external factors, while sales volume is a cleaner, more consistent metric.
- Prevents misleading insights: A promotion could look ineffective in revenue terms but actually drive strong customer response.

**Broader Principle**

We shoulld choose a target variable that directly represents the business objective and is not confounded by external factors.

The target should have below properties:
- Reflect what you truly want to optimize.
- Be directly influenced by input features.
- Avoid hidden biases (like pricing effects).
__________________________________________________________________________________________________________________
**Part c**
__________________________________________________________________________________________________________________

A single global model will likely miss store-specific behavior, so a better approach is to account for heterogeneity across locations.

Proposed Strategy: Segmented / Hierarchical Modeling
Segment-Based Models
Build separate models for different groups of stores, e.g.:
	- Urban
	- Semi-urban
	- Rural

Advantage of this model:

- Stores in different locations have different customer behavior and demand patterns.
- Each model learns more relevant relationships for that segment.
- Improves prediction accuracy compared to one global model.


########################################################################################################################################
**B2 : Data and EDA Strategy**
__________________________________________________________________________________________________________________
**Part a**
__________________________________________________________________________________________________________________
Data Integration Strategy

Base table should be transactions which contains sales records. 
It should be joined with:
- store attributes → on store_id
- promotion details → on promotion_id (or equivalent key)
- calendar → on transaction_date

We should uase Left Join, it should keep all records fron the transaction table and add additional details to the records.

Grain of Final Dataset:
One row = one store × one month (or time period)

Before modelling below aggregate transaction-level data is required:

- Target Variable:
  	items_sold → sum per store per month
- Numerical Features:
  	monthly_footfall → sum or average
	competition_density → average
	Other numeric fields → mean/sum depending on meaning
- Categorical Features:
	promotion_type → most frequent (mode) or assigned promotion
	store_size, location_type → take as constant per store
- Calendar Features:
	is_weekend → proportion or count of weekends
	is_festival → binary flag (any festival in that month)


__________________________________________________________________________________________________________________
**Part b**
__________________________________________________________________________________________________________________

**Exploratory Data Analysis (EDA) Plan**

1. Distribution of Target Variable (items_sold)
Chart type: Histogram / Boxplot
Chart will help in checking Skewness (left/right) and Outliers (extremely high/low sales)
Impact on modeling:
	If highly skewed → apply log transformation
	If outliers exist → consider robust models or outlier handling

2. Promotion Type vs Items Sold
Chart type: Boxplot or Bar Chart
Chart will help in checking which promotions lead to higher median sales and variability across promotions
Impact on modeling:
	Confirms promotion_type is important
	May lead to interaction features (e.g., promotion × store size)

3. Numerical Feature Relationships
Chart: Correlation Heatmap / Scatter plots
Chart will help in checking strong positive/negative correlations and multicollinearity between features
Impact on modeling:
	Remove redundant features
	Choose models accordingly (tree-based models handle correlation better)

4. Store Characteristics Analysis
Chart: Grouped bar plots (e.g., store_size / location_type vs items_sold)
Chart will help in checking differences in sales patterns across store types
Impact on modeling:
	Encode categorical variables properly
	Consider segmentation or interaction terms

5. Time-Based Trends
Chart: Line plot (items_sold over time)
Chart will help in checking seasonality (monthly trends) and spikes during festivals
Impact on modeling:
	Create time-based features (month, festival flags)
	Consider time-aware validation


__________________________________________________________________________________________________________________
**Part c**
__________________________________________________________________________________________________________________

**Impact of Imbalance **
- The model may become biased toward “no promotion” patterns, since most training data comes from that group.
- It may underestimate the true effect of promotions or fail to learn differences between promotion types.
- Predictions for promotional scenarios may be less accurate and less reliable.

  **Steps to Address the Imbalance**
- Resampling Techniques:Oversample promotion cases or undersample non-promotion cases to balance the data.
- Stratified Analysis / Validation:Ensure evaluation considers both promotion and non-promotion cases fairly.
- Feature Engineering:Create a binary feature like is_promotion to explicitly capture promotion presence.
- Model Choice:Use models like Random Forest or Gradient Boosting, which handle imbalance better.
- Separate Evaluation:Evaluate model performance specifically on promotion vs non-promotion subsets.

################################################################################################################
  B3: Model Evaluation and Deployment
  _________________________________________________________________________________________________________________
**Part a**
__________________________________________________________________________________________________________________

**Train–Test Split Strategy**
- We should use a temporal split (no shuffling):
	Train on the first ~80% of months (earlier period)
	Test on the most recent ~20% of months
- This strategy mimics real usage thus helps in predicting future sales from past data.

**Random Split is Inappropriate because:**
- It breaks the time order of data i.e. mixes past and future
- It causes data leakage as model indirectly sees future patterns
- It produces over-optimistic results that won’t hold true in practice

Evaluation Metrics

1. MAE (Mean Absolute Error):
It measures the average difference between actual and predicted values.
It tells us how many items, on average, the model’s prediction is off by.
For Example: MAE = 20 means predictions are off by about 20 items.

2. RMSE (Root Mean Squared Error):
It is similar to MAE but gives more importance to large errors.
It shows how serious the model’s biggest mistakes are.
A higher RMSE indicates that the model makes some large prediction errors.

3. MAPE (Mean Absolute Percentage Error):
It expresses the error as a percentage.
It tells us how far the predictions are from actual values in percentage terms.
Example: MAPE = 10% means predictions are off by about 10%.

_________________________________________________________________________________________________________________
**Part b**
__________________________________________________________________________________________________________________
Explanation Using Feature Importance

To understand why the model gives different promotion recommendations for the same store in different months, we would analyze feature importance and feature values.


1. Check Feature Importance
We should look at which features are most influential (e.g., month, is_festival, footfall, promotion_type, competition_density)
This tells us what factors the model uses most to make decisions.

2. Compare December vs March Inputs

For Store 12, we would compare the feature values:

December month is likely festival season thus there can be high demand that results in higher customer activity. Thus, Model prefers Loyalty Points to retain customers and increase repeat purchases.
March month is possibly normal or low-demand period. Customers may be more price-sensitive. Thus, Model prefers Flat Discount to boost sales volume.

3. Understand Model Behavior
The model is not just learning “best promotion per store”
It learns “best promotion based on context” (time + conditions)
So different months have different conditions that leads to different recommendations

4. Communication to Marketing Team

The model considers factors like season, customer activity, and demand patterns.
In December, customers are more active, so loyalty rewards work better.
In March, demand is lower, so discounts are more effective in increasing sales.

_________________________________________________________________________________________________________________
**Part c**
__________________________________________________________________________________________________________________

**End-to-End Deployment Process**

1. Saving the Trained Model
After training, save the full pipeline (preprocessing + model) using joblib

	import joblib
	joblib.dump(model_pipeline, 'model.pkl')

This ensures the same preprocessing steps and model are reused during prediction.

2. Preparing New Monthly Data
At the start of each month:
- Collect latest data for all 50 stores (store features, calendar info, etc.)
- Apply same feature engineering (e.g., date features, encoding structure)

	Load the saved model:

		model = joblib.load('model.pkl')

	Pass new data into the model:

		predictions = model.predict(new_data)

3. Generating Recommendations
For each store create multiple inputs (one for each promotion type) and predict items_sold for each option. Out of all select the promotion with highest predicted sales

4. Monitoring Model Performance

To ensure the model stays reliable:
a. Track Prediction Accuracy: Compare predicted vs actual items_sold monthly. Monitor metrics like RMSE and MAE
b. Data Drift Monitoring: Check if new data differs significantly from training data(e.g., changes in customer behavior, competition)
c. Performance Degradation: If error metrics increase consistently, model may be outdated and we have to create new model.

5. Retraining Strategy
We can retrain the model periodically (e.g., every 3–6 months), or when performance drops significantly
We should use latest data to capture new trends.
