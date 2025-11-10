# AI Development Workflow Assignment

**Course:** AI for Software Engineering  
**Duration:** 7 days  
**Total Points:** 100  
**Group Members:** [Your Group Members Names Here]  
**Date:** [Submission Date]

---

## Table of Contents

1. [Part 1: Short Answer Questions](#part-1-short-answer-questions)
2. [Part 2: Case Study Application](#part-2-case-study-application)
3. [Part 3: Critical Thinking](#part-3-critical-thinking)
4. [Part 4: Reflection & Workflow Diagram](#part-4-reflection--workflow-diagram)
5. [References](#references)

---

## Part 1: Short Answer Questions (30 points)

### 1. Problem Definition (6 points)

#### a. Define a hypothetical AI problem

**Problem Statement:**  
Predicting daily supermarket/store sales demand for fresh bread.

**Context:**  
Supermarkets face a constant challenge of determining how much bread to bake daily. Over-baking leads to waste and financial loss, while under-baking results in stockouts, disappointed customers, and lost revenue. This AI system aims to forecast daily bread demand using historical sales, weather patterns, and other relevant factors.

---

#### b. List 3 objectives and 2 stakeholders

**Objectives:**

1. **Minimize Food Waste**  
   Accurately predict demand to prevent over-baking and reduce unsold bread. Even a 10% reduction in waste can save thousands of dollars annually and reduce environmental impact.

2. **Optimize Production**  
   Improve operational efficiency by creating accurate schedules for staffing and raw ingredient orders. This allows better planning for flour, yeast procurement, and baker shift scheduling.

3. **Prevent Stockouts**  
   Ensure sufficient stock during peak times to protect revenue and customer satisfaction. Repeated stockouts damage customer loyalty and may drive customers to competitors.

**Stakeholders:**

1. **Store Manager**  
   Oversees overall store profitability. Needs the system to minimize waste costs and prevent revenue loss from stockouts. Uses predictions for strategic decisions on inventory, pricing, and promotions.

2. **Bakery Department Head**  
   Manages daily bakery operations. Requires daily predictions to plan production schedules, manage ingredient inventory, and schedule staff appropriately.

---

#### c. Propose 1 Key Performance Indicator (KPI) to measure success

**KPI:** Percentage reduction in daily bread waste.

**Rationale:**  
This metric is easily measurable by counting unsold loaves before and after AI implementation. It directly ties to business value, as each wasted loaf represents lost money and environmental impact. A clear target, such as 40% waste reduction within 3 months, provides a concrete success measure that stakeholders can understand and track.

---

### 2. Data Collection & Preprocessing (8 points)

#### a. Identify 2 data sources for your problem

**Data Source 1: Historical Sales Data (Point-of-Sale System)**

Contains transaction timestamps, quantities sold, bread types, prices, and stockout times. This data reveals actual customer demand patterns, seasonal trends, peak shopping hours, and provides 12-24 months of historical foundation for pattern recognition.

**Data Source 2: External Data (Weather & Calendar Information)**

Includes weather forecasts (temperature, precipitation), public holidays, school breaks, and local events. Weather affects shopping behavior, holidays change purchasing patterns, and local events impact foot traffic. Sources include weather APIs like OpenWeatherMap and public holiday databases.

---

#### b. Explain 1 potential bias in the data

**Stockout Bias (Censored Demand)**

Sales data shows low demand on days when bread sold out early, not because customers didn't want more, but because none was available. This "censored demand" means we only see sales until stock depleted, not true customer demand. For example, if 100 loaves sold out by 2 PM, actual demand might have been 150, but only 100 was recorded.

**Impact:** The model learns from recorded sales rather than actual demand, leading to systematic under-prediction on high-demand days. This perpetuates stockouts and defeats our objective of preventing them.

---

#### c. Outline 3 preprocessing steps

**1. Handling Missing Data**

Fill gaps in data caused by system downtime or unavailable information. For small gaps (1-2 hours), use averages of surrounding values. For complete missing days, use the average of the same weekday from surrounding weeks. For missing weather data, use historical averages or alternative APIs.

**2. Feature Engineering**

Create meaningful features from raw data. Transform dates into DayOfWeek (1-7), IsWeekend (0/1), Month (1-12), and IsHoliday (0/1). Convert weather into IsRainy, TemperatureCategory, and IsSevereWeather. Create sales-based features like SalesLast7Days, SalesSameDayLastWeek, and TrendDirection. This gives the model actionable patterns to learn from.

**3. Normalization (Feature Scaling)**

Rescale numerical features to a common range (0-1) so features with different units are treated fairly. Without normalization, features with larger numbers dominate learning, even if less important. Apply Min-Max scaling: divide the difference between each value and the minimum by the range (maximum minus minimum).

---

### 3. Model Development (8 points)

#### a. Choose a model and justify your choice

**Model:** Random Forest Regressor

**Justification:**  
Random Forest is an ensemble method that creates multiple decision trees and averages their predictions. It's ideal for this problem because it handles mixed data types (categorical like holidays, numerical like temperature) without complex transformations, captures non-linear relationships (sales don't change linearly with weather), and resists overfitting through its averaging approach. It also provides feature importance rankings and handles outliers well, making it robust and interpretable for stakeholders.

---

#### b. Describe how you would split data into training/validation/test sets

**Three-Way Time-Based Split:**

- **Training Set (70%)** - Older historical data (e.g., Jan 2022 - Dec 2023) for the model to learn patterns
- **Validation Set (15%)** - Recent data (e.g., Jan 2024 - Mar 2024) for tuning hyperparameters  
- **Test Set (15%)** - Most recent data (e.g., Apr 2024 - Jun 2024) for final evaluation

**Rationale:**  
Time-based splitting (not random shuffling) is crucial because it mimics real deployment where the model trained on past data predicts future sales. This chronological split ensures honest performance estimation.

---

#### c. Name 2 hyperparameters you would tune and why

**1. n_estimators (Number of Trees)**

Controls how many decision trees the Random Forest creates. Too few trees (e.g., 10) leads to high variance and less accurate predictions. Optimal range (100-200) balances accuracy and training speed. Too many trees (1000+) provides minimal accuracy improvement while significantly increasing training time.

**2. max_depth (Maximum Tree Depth)**

Controls how many questions each tree can ask before making a prediction. Shallow trees (depth 2-3) underfit and miss important patterns. Optimal depth (8-12) captures meaningful patterns without memorizing noise. Deep trees (20+) overfit by memorizing training data specifics, performing poorly on new data.

---

### 4. Evaluation & Deployment (8 points)

#### a. Select 2 evaluation metrics and explain their relevance

**1. Mean Absolute Error (MAE)**

Measures average prediction error in loaves of bread. It's easily understood by stakeholders - "on average, forecasts will be wrong by 6 loaves." This directly translates to business costs: if each loaf costs $2 and MAE is 6 loaves, the daily forecast error cost is $12. It's actionable for planning and treats over-predictions and under-predictions equally.

**2. R-squared (R²)**

Measures how much sales variation the model explains, scored between 0 and 1. A high R² (0.7-0.9) means features like weather and holidays genuinely predict sales. Low R² (0.2-0.4) indicates sales are mostly random or missing key features. It validates feature selection and communicates confidence to stakeholders about the model's predictive power.

---

#### b. What is concept drift? How would you monitor it post-deployment?

**Concept Drift:**  
Occurs when real-world patterns the model learned change over time, reducing prediction accuracy. Examples include new competitors opening nearby, consumer behavior shifts (low-carb diet trends), store changes (renovations), or neighborhood changes (new residential development).

**Monitoring Strategy:**

Implement daily automated logging of predictions versus actual sales, calculating error metrics. Track 7-day and 30-day rolling average errors. Set threshold-based alerts: Warning when error exceeds 1.5× baseline, Critical when it exceeds 2× baseline. Monitor for directional bias (consistent over-prediction or under-prediction). When critical alerts persist for 3+ days, trigger automated retraining with recent data. Maintain a visual dashboard showing performance trends and alert history.

---

#### c. Describe 1 technical challenge during deployment

**Challenge: Data Pipeline Reliability and Dependencies**

The prediction system must automatically pull data from multiple sources nightly. Failures at any point break the entire pipeline. The weather API might timeout or hit rate limits, the POS database might be under maintenance, network connectivity could fail, or data formats might change unexpectedly after system upgrades.

**Solutions:**  
Implement robust error handling with retry logic and exponential backoff. Use fallback data sources (multiple weather APIs, cached historical data). Run health checks before main processes. Validate data quality before use. Implement graceful degradation (make predictions with available data, noting reduced confidence). Log all processes comprehensively and send alerts on failures. Schedule redundant runs (10:30 PM health check, 11:00 PM main run, 11:30 PM backup, 6:00 AM final check).

---

## Part 2: Case Study Application (40 points)

### Scenario
A hospital wants an AI system to predict patient readmission risk within 30 days of discharge.

> **[TO BE FILLED]**
> 
> *This section will cover:*
> - *Problem Scope (5 points)*
> - *Data Strategy (10 points)*
> - *Model Development (10 points)*
> - *Deployment (10 points)*
> - *Optimization (5 points)*

---

## Part 3: Critical Thinking (20 points)

> **[TO BE FILLED]**
> 
> *This section will cover:*
> - *Ethics & Bias (10 points)*
> - *Trade-offs (10 points)*

---

## Part 4: Reflection & Workflow Diagram (10 points)

### Reflection (5 points)

#### What was the most challenging part of the workflow? Why?

The most challenging part of the AI development workflow was **data preprocessing and feature engineering**. This stage required careful consideration of multiple factors:

**Challenges Encountered:**

1. **Identifying and Handling Data Biases** - Recognizing stockout bias in the bread sales data was particularly challenging. We had to think critically about what the data truly represented versus what we needed it to represent. Missing sales due to stock unavailability don't indicate lack of demand, yet they appear as low sales in the data.

2. **Feature Engineering Decisions** - Determining which features would be most predictive required domain knowledge and experimentation. For instance, deciding whether to include day of week, weather conditions, holidays, or promotional events, and how to encode these features properly (categorical vs. numerical) was complex.

3. **Balancing Data Quality and Quantity** - We faced the dilemma of whether to discard incomplete records or impute missing values. Each approach has trade-offs that could impact model performance.

**Why This Was Challenging:**

- It required both technical skills (coding, statistical knowledge) and domain understanding (business context)
- Poor decisions at this stage compound through the entire pipeline, affecting model performance
- There's often no single "correct" answer, requiring judgment calls and iterative refinement
- The impact of preprocessing decisions isn't always immediately visible until model evaluation

---

#### How would you improve your approach with more time/resources?

With additional time and resources, I would implement the following improvements:

**1. Enhanced Data Collection**

- **Collect Additional Features:** Customer foot traffic data from sensors, competitor pricing and promotions, social media sentiment, local event information, and historical bread variety preferences
- **Extend Historical Data Range:** Gather 2-3 years of data to capture seasonal patterns, yearly trends, and anomalies

**2. Advanced Preprocessing Techniques**

- **Automated Feature Selection:** Implement feature importance analysis using SHAP values or permutation importance, validated by domain experts
- **Advanced Imputation Methods:** Use K-Nearest Neighbors (KNN) imputation or Multiple Imputation by Chained Equations (MICE) instead of simple averages

**3. Model Development Improvements**

- **Ensemble Approach:** Compare Random Forest with XGBoost, LightGBM, and LSTM for time series, then create an ensemble combining multiple algorithms
- **Automated Hyperparameter Tuning:** Implement grid search or Bayesian optimization with time series cross-validation

**4. Deployment and Monitoring Enhancements**

- **Real-Time Monitoring Dashboard:** Build dashboards showing predictions vs. actuals, performance metrics, and drift alerts
- **A/B Testing Framework:** Deploy in parallel with existing methods to compare outcomes before full rollout
- **Automated Retraining Pipeline:** Schedule regular retraining with automatic versioning and rollback capabilities

**5. Stakeholder Engagement**

- **Regular Feedback Loops:** Weekly meetings with store manager and bakery head for feedback on accuracy and usability
- **User-Friendly Interface:** Develop a mobile app or web interface for easy prediction viewing and manual corrections

**6. Ethical and Practical Considerations**

- **Bias Auditing:** Regular audits to ensure no unexpected biases develop over time
- **Explainability:** Implement LIME or SHAP tools so stakeholders understand prediction reasoning

**Resource Requirements:**

- **Technical Resources:** Cloud computing infrastructure, production-grade databases
- **Human Resources:** Data scientists, domain experts, software engineers
- **Time:** 3-6 months for comprehensive implementation and testing

---

### Workflow Diagram (5 points)

#### AI Development Workflow - Complete Process Flow

```mermaid
flowchart TD
    Start([AI DEVELOPMENT WORKFLOW]) --> Stage1
    
    Stage1[STAGE 1: PROBLEM DEFINITION] --> Stage2
    Stage2[STAGE 2: DATA COLLECTION] --> Stage3
    Stage3[STAGE 3: DATA PREPROCESSING & EDA] --> Stage4
    Stage4[STAGE 4: DATA SPLITTING] --> Stage5
    Stage5[STAGE 5: MODEL SELECTION] --> Stage6
    Stage6[STAGE 6: MODEL TRAINING & TUNING] --> Stage7
    Stage7[STAGE 7: MODEL EVALUATION] --> Decision
    
    Decision{Performance Satisfactory?} -->|NO| Stage6
    Decision -->|YES| Stage8
    
    Stage8[STAGE 8: MODEL DEPLOYMENT] --> Stage9
    Stage9[STAGE 9: MONITORING & MAINTENANCE] --> Stage10
    Stage10[STAGE 10: RETRAINING & OPTIMIZATION] --> Continuous
    Continuous[CONTINUOUS IMPROVEMENT] --> Stage9
    
    style Start fill:#e1f5ff,stroke:#0288d1,stroke-width:3px
    style Stage1 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage2 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage3 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage4 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage5 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage6 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage7 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage8 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage9 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage10 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Decision fill:#fff9c4,stroke:#f9a825,stroke-width:2px
    style Continuous fill:#e8f5e9,stroke:#43a047,stroke-width:2px
```

---

#### Detailed Workflow Stages

##### Stage 1: Problem Definition
- Define problem scope and objectives
- Identify stakeholders
- Establish success metrics (KPIs)
- Assess feasibility and constraints

##### Stage 2: Data Collection
- Identify data sources
- Gather relevant datasets
- Assess data quality and availability
- Address legal/ethical considerations

##### Stage 3: Data Preprocessing & EDA
- Clean data (handle missing values, outliers)
- Explore data patterns and distributions
- Feature engineering (create new features)
- Data transformation (normalization, encoding)
- Identify and mitigate biases

##### Stage 4: Data Splitting
- **Training Set (70%)** - Model learning
- **Validation Set (15%)** - Hyperparameter tuning
- **Test Set (15%)** - Final evaluation

##### Stage 5: Model Selection
- Choose appropriate algorithm(s)
- Train initial model
- Justify model choice based on problem type

##### Stage 6: Model Training & Tuning
- Train model on training data
- Tune hyperparameters using validation set
- Apply regularization techniques
- Prevent overfitting/underfitting

##### Stage 7: Model Evaluation
- Evaluate on test set using appropriate metrics
- Analyze confusion matrix, precision, recall, etc.
- Compare with baseline models
- Assess model interpretability

##### Stage 8: Model Deployment
- Integrate into production system
- Set up APIs and interfaces
- Ensure scalability and reliability
- Address technical challenges
- Comply with regulations

##### Stage 9: Monitoring & Maintenance
- Monitor model performance continuously
- Detect concept drift
- Track prediction accuracy
- Log errors and anomalies

##### Stage 10: Retraining & Optimization
- Retrain with new data periodically
- Update features and parameters
- Optimize for changing conditions
- Version control models

##### Continuous Improvement Cycle
- Iterate based on feedback
- Incorporate lessons learned
- Stay updated with new techniques
- Return to monitoring for ongoing improvement

---

#### Cross-Cutting Concerns Throughout All Stages

| Concern | Description |
|---------|-------------|
| **Ethics & Fairness** | Ensure the model doesn't discriminate or produce biased outcomes |
| **Privacy & Security** | Protect sensitive data and comply with data protection laws |
| **Transparency & Explainability** | Make model decisions interpretable and understandable |
| **Stakeholder Communication** | Keep all parties informed about progress and decisions |
| **Documentation** | Maintain comprehensive records of all processes and decisions |

---
