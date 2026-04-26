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




########################################################################################################################################
**B2 : Data and EDA Strategy**
__________________________________________________________________________________________________________________
**Part a**
__________________________________________________________________________________________________________________

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
