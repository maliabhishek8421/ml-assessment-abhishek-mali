# Business Case Analysis — Promotion Effectiveness

## B1. Problem Formulation

### (a) Problem Formulation

This problem can be formulated as a supervised learning regression task.

- Target Variable:  
  The target variable is the number of items sold (sales volume) for a given store, promotion, and time period.

- Input Features:  
  Candidate features include:
  - Promotion type (Flat Discount, BOGO, etc.)
  - Store attributes (store size, location type, footfall)
  - Competition density
  - Temporal features (month, seasonality, festival flags, weekends)
  - Historical sales patterns

- Problem Type:  
  This is a supervised regression problem because the goal is to predict a continuous numerical value (items sold) based on input features.

This formulation allows the business to simulate expected outcomes for different promotions and choose the one that maximizes sales.


### (b) Target Variable Justification

Using items sold (sales volume) is more reliable than total revenue because revenue can be distorted by pricing changes introduced by promotions.

For example, a heavy discount may increase units sold but reduce total revenue per item, making revenue an inconsistent measure of promotion effectiveness.

Items sold directly reflects customer response to promotions and is a better measure of demand stimulation.

This illustrates a key principle in machine learning: the target variable must align with the true business objective and should not be influenced by confounding factors that distort interpretation.


### (c) Alternative Modelling Strategy

Instead of a single global model, a segmented or hierarchical modelling approach should be used.

For example:
- Train separate models for urban, semi-urban, and rural stores  
OR  
- Use cluster-based models based on customer or store behavior  

This is justified because different store types respond differently to promotions due to variations in demographics, purchasing power, and competition.

A global model may average out these effects, leading to suboptimal recommendations. Segment-specific models capture local patterns more accurately and improve prediction performance.


## B2. Data and EDA Strategy

### (a) Data Preparation and Grain

The four tables (transactions, store attributes, promotions, and calendar) should be joined using appropriate keys:

- Transactions → joined with store attributes using store_id  
- Transactions → joined with promotion details using promotion_id  
- Transactions → joined with calendar using transaction_date  

The final dataset should be aggregated to a monthly store-level grain:

One row = one store × one month × one promotion  

Aggregations include:
- Total items sold (target variable)  
- Average basket size  
- Total transactions  
- Average footfall (if available)  

This aggregation aligns with the business decision frequency (monthly promotion planning).


### (b) Exploratory Data Analysis

1. Promotion vs Sales Analysis (Bar Plot / Box Plot)  
   - Compare items sold across different promotion types  
   - Identify which promotions perform best  
   - Helps in baseline understanding and feature importance  

2. Sales by Location Type  
   - Compare urban, semi-urban, and rural stores  
   - Detect structural differences in demand  
   - Supports segmentation strategy  

3. Time Series Analysis  
   - Plot sales over time  
   - Identify seasonality and trends  
   - Detect spikes during festivals or specific months  
   - Helps create time-based features  

4. Correlation Heatmap  
   - Analyze relationships between numerical variables  
   - Identify key drivers and multicollinearity  
   - Guides feature selection  

These analyses help understand patterns, identify key drivers, and guide feature engineering and model selection.


### (c) Handling Promotion Imbalance

If 80% of transactions occur without promotions, the model may become biased toward predicting non-promotion scenarios.

This can lead to poor learning of promotion effects and inaccurate recommendations.

To address this:
- Use reweighting or sampling techniques to balance promotion vs non-promotion data  
- Create a binary feature indicating promotion presence  
- Ensure adequate representation of all promotion types  

This helps the model better learn the true impact of promotions.


## B3. Model Evaluation and Deployment

### (a) Train-Test Split and Metrics

A time-based split should be used instead of a random split.

For example:
- Train on first 2.5 years  
- Test on the last 6 months  

A random split is inappropriate because it causes data leakage by mixing past and future data, leading to overly optimistic performance.

Evaluation Metrics:
- RMSE (Root Mean Squared Error): Measures overall prediction error and penalizes large errors  
- MAE (Mean Absolute Error): Measures average error and is easier to interpret  

Lower RMSE and MAE indicate better prediction accuracy. These metrics represent how close predicted sales are to actual sales.


### (b) Explaining Model Recommendations

Feature importance helps explain why different promotions are recommended for the same store in different months.

For example:
- In December, festival-related features and seasonal demand may be dominant, leading to a Loyalty Points Bonus recommendation  
- In March, lower demand or different patterns may favor a Flat Discount  

By analyzing feature importance and model inputs, we can understand how promotion type, seasonality, and store characteristics influence predictions.

These insights can be communicated using feature importance charts or SHAP values to improve transparency and trust.


### (c) Deployment Strategy

1. Model Saving:  
   Save the trained pipeline using joblib or pickle  

2. Monthly Data Preparation:  
   Collect latest store, promotion, and calendar data  
   Apply the same preprocessing pipeline  

3. Prediction:  
   Generate predictions for all promotion options for each store  
   Select the promotion with the highest predicted sales  

4. Automation:  
   Schedule a monthly batch process for recommendations  

5. Monitoring:  
   Track RMSE and MAE over time  
   Monitor data drift and feature distribution changes  
   Compare predicted vs actual sales  

6. Retraining:  
   Retrain periodically (e.g., quarterly) or when performance degrades  

This ensures the model remains accurate, reliable, and aligned with business needs.