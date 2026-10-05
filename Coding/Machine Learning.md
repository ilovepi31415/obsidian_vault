
## QuickLinks
[[Perceptrons]]
## Syntax

### Data Inspection

```python
# Separate features and target
X = df[feature_names].copy()
y = df[target_name].copy()

# Missing values summary
summary = pd.DataFrame({
    "dtype": X.dtypes.astype(str),
    "missing": X.isna().sum(),
    "missing_%": (100 * X.isna().mean()),
})
```

- `copy` - Creates an independent copy of the DataFrame or Series so subsequent modifications do not alter the original data

- `isna` - Detects missing values, returning a boolean object of the same size

- `sum` - Sums the boolean values to count the total missing entries per column

- `mean` - Calculates the proportion of missing values when applied to a Boolean DataFrame

### Train-Test Split

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.20, random_state=42
)
```

- `train_test_split` - Splits arrays or matrices into random train and test subsets to isolate evaluation data

### Pipeline Setup & Training

```python
# Build leakage-safe pipeline
model = Pipeline(steps=[
    ("imputer", SimpleImputer(strategy="median")),
    ("scalar", StandardScaler()),
    ("regression", LinearRegression())
])

# Fit on training data only
model.fit(X_train, y_train)

# Predict on unseen test features
y_pred = model.predict(X_test)
```

- `Pipeline` - Chains multiple steps sequentially so that transformations and modeling are bundled safely, preventing test set leakage
    
- `SimpleImputer` - Fills missing numeric values using a specified strategy, such as the median learned from the training data
    
- `StandardScaler` - Standardizes features to a unit range so all features have the same effect
    
- `LinearRegression` - Fits an ordinary least squares linear regression model
    
- `fit` - Trains a model on the training set
    
- `predict` - Predicts on the testing data based on the pipeline model
    

### Evaluation Metrics

```python
def regression_metrics(y_true, predictions):
    return {
        "MAE": mean_absolute_error(y_true, predictions),
        "RMSE": np.sqrt(mean_squared_error(y_true, predictions)),
        "R2": r2_score(y_true, predictions)
    }

metrics = regression_metrics(y_test, y_pred)
```

- `mean_absolute_error` - Calculates the average absolute difference between actual and predicted values
    
- `mean_squared_error` - Calculates the average squared difference between actual and predicted values
    
- `np.sqrt` - Computes the non-negative square root, used here to convert mean squared error into root mean squared error (RMSE)
    
- `r2_score` - Computes the coefficient of determination to measure how much better the model performs compared to predicting the baseline mean
    

### Residuals & Plots

```python
# Calculate residuals
residuals = y_test.to_numpy() - y_pred

# Scatter plots: Actual vs Predicted & Residuals vs Predicted
fig, axes = plt.subplots(1, 2, figsize=(12, 4.5))

# Actual vs. Predicted (diagonal represents perfect predictions)
axes[0].scatter(y_test, y_pred, alpha=0.65)
low, high = min(y_test.min(), y_pred.min()), max(y_test.max(), y_pred.max())
axes[0].plot([low, high], [low, high], "--", color="black")

# Residuals vs. Predicted (ideal is an unstructured cloud around 0)
axes[1].scatter(y_pred, residuals, alpha=0.65)
axes[1].axhline(0, linestyle="--", color="black")
plt.tight_layout()
plt.show()
```

- `to_numpy` - Converts a pandas Series to a NumPy array
    
- `plt.subplots` - Creates a figure and a grid of subplots for side-by-side visualization
    
- `scatter` - Creates a scatter plot taking `x, y, alpha`
    
- `plot` - Plots lines and/or markers, used here to draw the diagonal reference line representing perfect predictions
    
- `axhline` - Adds a horizontal line across the axis
    

### Accessing Pipeline Steps & Coefficients

```python
# Extract coefficients from the named estimator inside the pipeline
coefficients = pd.Series(
    model.named_steps["regression"].coef_,
    index=feature_names,
    name="coefficient"
).sort_values(key=np.abs, ascending=False)
```

- `named_steps` - A dictionary-like attribute that accesses a specific step within a fitted Pipeline using its assigned string name
    
- `sort_values` - Sorts a pandas Series by its values, used here with `np.abs` to rank feature coefficients by their absolute magnitude