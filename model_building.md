# Model Building Instructions

This section outlines the complete workflow for building predictive models or analytical pipelines using the Vietnam Weather Dataset (2021–2025). The steps cover data understanding, preprocessing, feature engineering, model selection, training, evaluation, and deployment considerations.

---

## 1. Objective Definition

Before building any model, clearly define the prediction or analysis goal. Possible tasks include:

- **Regression:**  
  - Predict average temperature, humidity, or wind speed for a future date.
  - Forecast multi-step weather trends (e.g., next 7 days).

- **Classification:**  
  - Predict the weather category (sunny, rainy, cloudy, etc.) from features.
  - Map weather codes → inferred weather descriptions.

- **Recommendation:**  
  - Suggest best travel months for each province based on weather patterns.

- **Clustering:**  
  - Group provinces by climate similarity.

Define your task first — it determines preprocessing and algorithms.

---

## 2. Data Understanding

Before modeling, explore:

- Value ranges of temperature, humidity, wind speed  
- Distribution differences across regions (North, Central, South)
- Weather seasonality patterns  
- Correlation patterns between features  
- Missing or erroneous data (dataset currently has 0 missing, 0 duplicates)

Recommended EDA charts:
- Time-series plots per province  
- Heatmaps of correlations  
- Boxplots of temperature by month / region  
- Line plots showing humidity or wind speed across years

---

## 3. Data Preprocessing

### 3.1. Handle Data Types
Ensure correct datatypes:
- Convert `date` → datetime
- Convert `location` → categorical or encoded
- Ensure numeric fields are float and int as needed

### 3.2. Derive Calendar Features
Extract time-based features to improve model performance:
- `year`, `month`, `day`
- `week_of_year`
- `season` (Winter / Spring / Summer / Autumn)
- `is_holiday` (optional, if tourism model)

### 3.3. Encode Categorical Variables
For machine learning models:
- **Label Encoding** for `location`
- **One-Hot Encoding** if models require non-ordinal encoding
- **Embedding** if using neural networks

### 3.4. Normalization / Scaling
Apply scaling to continuous variables depending on the model:
- **StandardScaler** (recommended for linear or neural networks)
- **MinMaxScaler** (useful for gradient boosting or neural nets)

Applies to:
- temp, temp_max, temp_min  
- wind_speed  
- humidity

---

## 4. Feature Engineering

### 4.1. Rolling Statistics
Weather has strong temporal dependence. Add rolling windows such as:
- 3-day / 7-day moving average of temperature
- Rolling max/min temperatures
- Humidity trend indicator

### 4.2. Weather Category Encoding
Convert `weather_code` → high-level category (sunny, rainy, cloudy).

Useful for both prediction and interpretation.

### 4.3. Interaction Features
- Temperature × Humidity (heat index proxy)
- Wind × Rainy category (storm likelihood)

### 4.4. Geographic Features (Optional)
Merge with external geographic data:
- Latitude / Longitude  
- Elevation  
- Distance to coast  

These significantly improve climate predictability.

---

## 5. Model Selection

Depending on the task:

### **Regression Models**
- Linear Regression (baseline)
- Random Forest Regressor
- XGBoost / LightGBM
- LSTM / GRU (for time series forecasting)
- Temporal Fusion Transformer (TFT)
- Prophet (for per-province forecasting)

### **Classification Models**
- Logistic Regression
- Random Forest / XGBoost
- LSTM / 1D-CNN (if using sequences)
- Transformer-based sequence models

### **Clustering Models**
- KMeans (with scaled variables)
- DBSCAN (if spatial features included)
- Hierarchical Clustering

Choose models based on:
- Interpretability needs
- Real-time performance
- Data volume (113k rows supports tree models and light neural nets easily)

---

## 6. Train/Validation/Test Strategy

Weather is **time-dependent**, so use time-aware splits.

### Recommended:
- **Train:** 2021–2024  
- **Validation:** Jan–Jun 2025  
- **Test:** Jul–Dec 2025  

Avoid random splitting because it leaks future information.

For LSTM or Transformer models:
- Create sliding windows (e.g., 7 days input → next day prediction)

---

## 7. Model Training

### 7.1. Regression Example (Predict Temperature)
1. Select features (temp_min, temp_max, humidity, wind_speed, month, location encoding)
2. Standardize numeric features
3. Train model (e.g., LightGBM)
4. Tune hyperparameters using Optuna or GridSearch:
   - num_leaves  
   - max_depth  
   - learning_rate  
   - n_estimators  

### 7.2. Classification Example (Predict Weather Category)
1. Convert weather_code → label class
2. Train model (XGBoost or RandomForest)
3. Use F1-score or accuracy for evaluation

### 7.3. Time-Series Example (LSTM Forecasting)
1. Sort by location + date  
2. Generate normalized sequences  
3. Split by time  
4. Train LSTM/GRU with:
   - sequence length = 7–30  
   - hidden size = 64–256  
   - batch size = 32–64  
   - learning rate = 1e-3  

---

## 8. Model Evaluation

Use the right metric for your task.

### Regression Metrics
- MAE (recommended for temperature)
- RMSE
- MAPE (careful with humidity near zero)
- R² score

### Classification Metrics
- Accuracy
- Precision / Recall
- F1-score
- Confusion matrix
- ROC–AUC (if binary or multi-class via averaging)

### Time Series Metrics
- sMAPE
- MASE
- Temporal MSE on last 30 days

Evaluate per province and overall.

---

## 9. Model Interpretation

Use model explainability tools:
- Feature importance (tree models)
- SHAP values (global and local explanations)
- Partial dependence plots
- Attention maps (for Transformers)

This helps identify:
- Seasonal drivers  
- Weather factors affecting predictions  
- Province-level climate patterns  

---

## 10. Deployment Considerations

### 10.1. Real-Time API
Expose model as a service:
- FastAPI / Flask endpoint
- Input: location + date + recent weather window
- Output: predicted weather metrics

### 10.2. Batch Forecasting
Generate daily or weekly forecast files per province.

### 10.3. Monitoring
Track:
- Model drift (seasonal changes)
- Feature distribution shifts
- Prediction error increases

### 10.4. Model Updating
Retrain monthly or quarterly as new 2025–2026 data arrive.

---

## 11. Documentation and Versioning
- Use MLflow or Weights & Biases  
- Track parameters, metrics, model artifacts  
- Record dataset version + preprocessing steps  

---

## 12. Reproducibility
Ensure:
- Random seeds fixed  
- Data pipeline deterministic  
- Preprocessing stored as a scikit-learn Pipeline or Transformer  

This ensures other researchers can replicate results.

---

## Appendix
Example ML pipeline file structure:

