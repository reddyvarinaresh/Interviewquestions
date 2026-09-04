# Machine Learning & Deep Learning — Complete Interview Question Bank
> Sources: Glassdoor, LinkedIn, Kaggle discussions, Towards Data Science, Papers With Code, Reddit r/MachineLearning
> Companies: Deloitte, Accenture, Optum, Carelon, EPAM, Cognizant, TCS, Wipro, Infosys, Brillio, Persistent, LTIMindtree
> Level: 3–15 Years | All questions categorized by topic

---

## FREQUENCY MAP

| Topic | Frequency | Companies |
|-------|-----------|-----------|
| ML Fundamentals (Bias-Variance, Overfitting) | 🔴 Very High | All |
| Supervised Learning Algorithms | 🔴 Very High | All |
| Model Evaluation Metrics | 🔴 Very High | All |
| Feature Engineering | 🔴 Very High | All |
| Regularization | 🔴 High | All |
| Ensemble Methods | 🔴 High | All |
| Unsupervised Learning | 🟡 High | EPAM, Deloitte, Accenture |
| Deep Learning Fundamentals | 🟡 High | Deloitte AI, EPAM, Optum |
| CNNs | 🟡 Medium-High | AI/Computer Vision roles |
| RNNs / LSTMs / Transformers | 🟡 Medium-High | NLP/GenAI roles |
| Optimization (SGD, Adam) | 🟡 Medium | Senior roles |
| Transfer Learning / Fine-tuning | 🟡 Medium | GenAI roles |
| Reinforcement Learning | 🟢 Lower | Research / GenAI roles |
| MLOps / Model Deployment | 🔴 High | All senior roles |

---

## SECTION 1: ML FUNDAMENTALS

### Q1. What is the bias-variance tradeoff?
**Answer:**
- **Bias** — error from wrong assumptions; model too simple → underfitting
- **Variance** — error from sensitivity to training data fluctuations; model too complex → overfitting
- **Tradeoff:** Increasing model complexity reduces bias but increases variance
- **Sweet spot:** Balance using cross-validation, regularization, ensemble methods
- **Mathematically:** Total Error = Bias² + Variance + Irreducible Noise

```python
# Visualize using learning curves
from sklearn.model_selection import learning_curve
import numpy as np, matplotlib.pyplot as plt

def plot_learning_curve(model, X, y):
    sizes, train_scores, val_scores = learning_curve(
        model, X, y, cv=5,
        train_sizes=np.linspace(0.1, 1.0, 10),
        scoring='neg_mean_squared_error'
    )
    train_mean = -train_scores.mean(axis=1)
    val_mean   = -val_scores.mean(axis=1)
    # High train_mean → high bias (underfitting)
    # Large gap between train and val → high variance (overfitting)
    plt.plot(sizes, train_mean, label='Train Error')
    plt.plot(sizes, val_mean,   label='Val Error')
    plt.legend(); plt.show()
```

---

### Q2. What is overfitting and how do you prevent it?
**Answer:**
- **Overfitting:** Model memorizes training data, fails to generalize
- **Signs:** High train accuracy, low val/test accuracy; large gap in learning curves

**Prevention techniques:**
| Technique | How it helps |
|-----------|-------------|
| More training data | Reduces variance |
| Cross-validation | Detects overfitting early |
| Regularization (L1/L2) | Penalizes large weights |
| Dropout (DL) | Randomly zeroes neurons |
| Early stopping | Stop before overfitting |
| Reduce model complexity | Fewer parameters |
| Data augmentation | Artificially expand dataset |
| Ensemble methods | Average out individual model variance |

```python
from sklearn.linear_model import Ridge, Lasso
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

# L2 regularization (Ridge)
model = Pipeline([
    ('scaler', StandardScaler()),
    ('ridge', Ridge(alpha=1.0))  # alpha controls regularization strength
])

# Cross-validation to detect overfitting
from sklearn.model_selection import cross_val_score
scores = cross_val_score(model, X, y, cv=5, scoring='r2')
print(f"CV R²: {scores.mean():.3f} ± {scores.std():.3f}")
```

---

### Q3. Explain L1 vs L2 regularization.
**Answer:**
- **L1 (Lasso):** Adds `λ * Σ|wᵢ|` to loss → produces sparse weights (many = 0) → feature selection
- **L2 (Ridge):** Adds `λ * Σwᵢ²` to loss → shrinks all weights toward zero, no sparsity
- **Elastic Net:** Combination of L1 + L2

```python
from sklearn.linear_model import Ridge, Lasso, ElasticNet

ridge   = Ridge(alpha=1.0)       # L2 — shrink all weights
lasso   = Lasso(alpha=0.1)       # L1 — zero out unimportant features
elastic = ElasticNet(alpha=0.1, l1_ratio=0.5)  # mix

# When to use:
# L1: many irrelevant features, want automatic feature selection
# L2: all features likely relevant, just want to prevent large weights
# Elastic Net: many features, some correlated
```

---

### Q4. What is cross-validation? Types and when to use each.
**Answer:**

```python
from sklearn.model_selection import (
    KFold, StratifiedKFold, LeaveOneOut,
    TimeSeriesSplit, cross_val_score
)

# K-Fold: standard, shuffles data, k splits
kf = KFold(n_splits=5, shuffle=True, random_state=42)

# Stratified K-Fold: preserves class distribution — USE FOR CLASSIFICATION
skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

# Time Series Split: no future leakage — USE FOR TIME SERIES
tscv = TimeSeriesSplit(n_splits=5)

# Leave-One-Out: each sample is its own test set — expensive, USE FOR SMALL DATASETS
loo = LeaveOneOut()

# Usage:
scores = cross_val_score(model, X, y, cv=skf, scoring='roc_auc')
print(f"AUC: {scores.mean():.3f} ± {scores.std():.3f}")
```

---

### Q5. Explain the curse of dimensionality.
**Answer:**
- As dimensions increase, data becomes sparse; distance metrics lose meaning
- Volume of space grows exponentially → need exponentially more data
- K-NN and distance-based methods suffer most

**Solutions:**
- Feature selection (remove irrelevant features)
- Dimensionality reduction: PCA, t-SNE, UMAP
- Feature engineering (domain knowledge)
- L1 regularization (automatic feature selection)

```python
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# PCA: project to lower dimensions preserving max variance
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

pca = PCA(n_components=0.95)  # keep 95% of variance
X_reduced = pca.fit_transform(X_scaled)
print(f"Original: {X.shape[1]} → Reduced: {X_reduced.shape[1]} features")
print(f"Explained variance: {pca.explained_variance_ratio_.cumsum()[-1]:.2%}")
```

---

### Q6. What is the difference between parametric and non-parametric models?
**Answer:**

| | Parametric | Non-Parametric |
|--|-----------|----------------|
| Examples | Linear Regression, Logistic Regression, Naive Bayes | K-NN, Decision Trees, SVMs, Kernel methods |
| Parameters | Fixed number, learned from data | Grows with data |
| Data requirements | Less data needed | More data needed |
| Interpretability | High | Low to medium |
| Flexibility | Low (strong assumptions) | High |

---

### Q7. Explain Maximum Likelihood Estimation (MLE) vs MAP.
**Answer:**
- **MLE:** Find parameters θ that maximize P(data | θ) — no prior
- **MAP:** Find θ that maximizes P(θ | data) = P(data | θ) × P(θ) — with prior
- MLE can overfit small data; MAP adds regularization via prior
- Ridge = MAP with Gaussian prior; Lasso = MAP with Laplace prior

---

## SECTION 2: SUPERVISED LEARNING ALGORITHMS

### Q8. How does Linear Regression work? Assumptions?
**Answer:**
- Fits line y = Xβ + ε by minimizing sum of squared residuals (OLS)
- **Assumptions:**
  1. Linearity: relationship between X and y is linear
  2. Independence: observations are independent
  3. Homoscedasticity: constant variance of residuals
  4. Normality: residuals are normally distributed
  5. No multicollinearity among features

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error
import numpy as np

model = LinearRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(f"R²: {r2_score(y_test, y_pred):.4f}")
print(f"RMSE: {np.sqrt(mean_squared_error(y_test, y_pred)):.4f}")
print(f"Coefficients: {dict(zip(feature_names, model.coef_))}")

# Check residuals for assumptions
residuals = y_test - y_pred
# Homoscedasticity: residuals vs fitted should show no pattern
# Normality: residuals should follow normal distribution
```

---

### Q9. How does Logistic Regression work?
**Answer:**
- Models P(y=1|X) = sigmoid(Xβ) = 1 / (1 + e^(-Xβ))
- Trained via maximum likelihood → minimizes log-loss (cross-entropy)
- Output is probability [0, 1]; threshold (default 0.5) for class prediction

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, roc_auc_score

model = LogisticRegression(C=1.0, max_iter=1000, solver='lbfgs')
model.fit(X_train, y_train)

y_prob = model.predict_proba(X_test)[:, 1]
y_pred = model.predict(X_test)

print(classification_report(y_test, y_pred))
print(f"AUC-ROC: {roc_auc_score(y_test, y_prob):.4f}")

# Feature importance via coefficients (after scaling)
importance = dict(zip(feature_names, model.coef_[0]))
```

---

### Q10. How do Decision Trees work? How to prevent overfitting?
**Answer:**
- Splits data recursively by feature that maximizes information gain (or Gini impurity reduction)
- **Gini Impurity:** 1 - Σpᵢ²  (0 = pure, 0.5 = max impurity for binary)
- **Entropy:** -Σpᵢlog₂(pᵢ)

**Overfitting prevention:**
- `max_depth` — limit tree depth
- `min_samples_split` — minimum samples to split a node
- `min_samples_leaf` — minimum samples in leaf
- Post-pruning: remove branches that don't improve validation performance

```python
from sklearn.tree import DecisionTreeClassifier, export_text

model = DecisionTreeClassifier(
    max_depth=5,
    min_samples_split=20,
    min_samples_leaf=10,
    criterion='gini',
    random_state=42
)
model.fit(X_train, y_train)
print(export_text(model, feature_names=feature_names))

# Feature importances (Gini importance)
importance = dict(zip(feature_names, model.feature_importances_))
```

---

### Q11. How does Random Forest work? Why is it better than a single tree?
**Answer:**
- Ensemble of decision trees trained on bootstrap samples (bagging)
- Each split considers random subset of features (feature randomness)
- Final prediction = majority vote (classification) or mean (regression)
- **Why better:** reduces variance via averaging; each tree sees different data + features → decorrelated trees

```python
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier(
    n_estimators=200,        # number of trees
    max_depth=10,
    max_features='sqrt',     # sqrt(n_features) per split
    min_samples_leaf=5,
    n_jobs=-1,
    random_state=42
)
rf.fit(X_train, y_train)

# Feature importance (mean decrease in impurity)
importances = dict(zip(feature_names, rf.feature_importances_))
top_features = sorted(importances.items(), key=lambda x: -x[1])[:10]

# Out-of-bag score (free validation without separate val set)
rf_oob = RandomForestClassifier(n_estimators=200, oob_score=True)
rf_oob.fit(X_train, y_train)
print(f"OOB Score: {rf_oob.oob_score_:.4f}")
```

---

### Q12. XGBoost — how does it work? When to use?
**Answer:**
- Gradient Boosting: sequentially adds trees to correct residuals of previous trees
- **XGBoost improvements:** regularization (L1/L2 on leaf weights), parallel tree building, sparse-aware split finding, built-in handling of missing values
- **Key hyperparameters:**
  - `n_estimators`: number of trees
  - `learning_rate`: shrinkage (lower = better generalization, needs more trees)
  - `max_depth`: tree depth (3-6 typical)
  - `subsample`: row subsampling per tree
  - `colsample_bytree`: feature subsampling per tree

```python
import xgboost as xgb
from sklearn.model_selection import RandomizedSearchCV

model = xgb.XGBClassifier(
    n_estimators=500,
    learning_rate=0.05,
    max_depth=5,
    subsample=0.8,
    colsample_bytree=0.8,
    reg_alpha=0.1,    # L1
    reg_lambda=1.0,   # L2
    use_label_encoder=False,
    eval_metric='logloss',
    random_state=42
)

# Early stopping to prevent overfitting
model.fit(
    X_train, y_train,
    eval_set=[(X_val, y_val)],
    early_stopping_rounds=50,
    verbose=False
)
print(f"Best iteration: {model.best_iteration}")
```

---

### Q13. Gradient Boosting vs Random Forest — when to use which?
**Answer:**

| | Random Forest | Gradient Boosting (XGBoost/LightGBM) |
|--|--------------|--------------------------------------|
| Training | Parallel (fast) | Sequential (slower) |
| Overfitting | Less prone | More prone (needs tuning) |
| Noisy data | More robust | More sensitive |
| Tabular data | Good | Often better |
| Tuning effort | Low | High |
| Interpretability | Medium | Medium |
| **Use when** | Fast baseline, noisy data, limited tuning time | Competition-level accuracy on tabular data |

---

### Q14. Support Vector Machines (SVM) — explain the key concepts.
**Answer:**
- Finds hyperplane with maximum margin between classes
- **Support vectors:** data points closest to hyperplane (only these matter)
- **Kernel trick:** maps data to higher dimensions to make non-linear data separable
  - Linear, RBF (Gaussian), Polynomial, Sigmoid
- **C parameter:** controls margin vs misclassification tradeoff (low C = wider margin, more errors)
- **γ (RBF):** controls influence of single training examples

```python
from sklearn.svm import SVC
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline

# SVM requires feature scaling!
svm_pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('svm', SVC(kernel='rbf', C=10, gamma='scale', probability=True))
])
svm_pipeline.fit(X_train, y_train)
y_prob = svm_pipeline.predict_proba(X_test)[:, 1]
```

---

### Q15. How does K-Nearest Neighbors work? Pros and cons?
**Answer:**
- Classifies by majority vote of k nearest training samples (Euclidean distance)
- **Pros:** Simple, no training, works well with small data, multi-class naturally
- **Cons:** Slow at prediction O(n×d), sensitive to irrelevant features, needs scaling, memory-intensive

```python
from sklearn.neighbors import KNeighborsClassifier

knn = KNeighborsClassifier(n_neighbors=5, metric='euclidean', weights='uniform')
# Best practice: scale features first
# Choosing k: use odd k for binary, try sqrt(n) as starting point
# Cross-validate to find optimal k
```

---

### Q16. Naive Bayes — when does it work despite being "naive"?
**Answer:**
- Assumes feature independence given class (usually violated in practice)
- Still works well because: probabilities are used for ranking, not calibration; errors in probability estimates cancel out
- **Best for:** Text classification, spam detection, high-dimensional sparse data, real-time prediction (very fast)

```python
from sklearn.naive_bayes import GaussianNB, MultinomialNB, BernoulliNB

gnb  = GaussianNB()                    # continuous features
mnb  = MultinomialNB(alpha=1.0)        # count data (text, TF-IDF)
bnb  = BernoulliNB(alpha=1.0)          # binary features
```

---

## SECTION 3: MODEL EVALUATION METRICS

### Q17. Precision, Recall, F1 — when to use which?
**Answer:**
- **Precision** = TP / (TP + FP) — "Of predicted positives, how many are correct?"
  - High precision important when false positives are costly (spam filter, fraud alerts)
- **Recall** = TP / (TP + FN) — "Of actual positives, how many did we catch?"
  - High recall important when false negatives are costly (cancer screening, fraud detection)
- **F1** = 2 × (Precision × Recall) / (Precision + Recall) — harmonic mean; use when both matter
- **F-beta:** β > 1 favors recall, β < 1 favors precision

```python
from sklearn.metrics import (
    precision_score, recall_score, f1_score,
    classification_report, confusion_matrix,
    roc_auc_score, average_precision_score
)
import seaborn as sns

y_pred = model.predict(X_test)
y_prob = model.predict_proba(X_test)[:, 1]

print(classification_report(y_test, y_pred, digits=4))
print(f"AUC-ROC: {roc_auc_score(y_test, y_prob):.4f}")
print(f"PR-AUC:  {average_precision_score(y_test, y_prob):.4f}")

# Confusion matrix
cm = confusion_matrix(y_test, y_pred)
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues')
```

---

### Q18. ROC-AUC vs PR-AUC — when to use which?
**Answer:**
- **ROC-AUC:** Trade-off between TPR and FPR at all thresholds; **use when classes are balanced**
- **PR-AUC (Average Precision):** Precision-Recall tradeoff; **use when classes are imbalanced** — more informative because it focuses on the minority class
- ROC-AUC can be misleadingly high on imbalanced data (many true negatives inflate TPR)

```python
from sklearn.metrics import roc_curve, precision_recall_curve
import matplotlib.pyplot as plt

# ROC Curve
fpr, tpr, _ = roc_curve(y_test, y_prob)
plt.plot(fpr, tpr, label=f'ROC AUC={roc_auc_score(y_test, y_prob):.3f}')

# PR Curve
prec, rec, _ = precision_recall_curve(y_test, y_prob)
plt.plot(rec, prec, label=f'PR AUC={average_precision_score(y_test, y_prob):.3f}')
```

---

### Q19. How do you handle class imbalance?
**Answer:**

```python
# 1. Resampling
from imblearn.over_sampling import SMOTE
from imblearn.under_sampling import RandomUnderSampler
from imblearn.pipeline import Pipeline as ImbPipeline

pipeline = ImbPipeline([
    ('smote', SMOTE(random_state=42)),
    ('model', RandomForestClassifier())
])

# 2. Class weights
from sklearn.utils.class_weight import compute_class_weight
import numpy as np

weights = compute_class_weight('balanced', classes=np.unique(y_train), y=y_train)
class_weight_dict = dict(zip(np.unique(y_train), weights))
model = RandomForestClassifier(class_weight=class_weight_dict)

# XGBoost:
scale_pos_weight = (y_train == 0).sum() / (y_train == 1).sum()
xgb_model = xgb.XGBClassifier(scale_pos_weight=scale_pos_weight)

# 3. Adjust decision threshold
from sklearn.metrics import precision_recall_curve
prec, rec, thresholds = precision_recall_curve(y_val, y_prob_val)
# Find threshold maximizing F1
f1_scores = 2 * prec * rec / (prec + rec + 1e-10)
best_threshold = thresholds[np.argmax(f1_scores)]
y_pred_adjusted = (y_prob_test >= best_threshold).astype(int)
```

---

### Q20. Regression metrics — RMSE vs MAE vs R²?
**Answer:**

| Metric | Formula | Sensitive to Outliers | When to Use |
|--------|---------|----------------------|-------------|
| MAE | mean(|y-ŷ|) | No | Robust, interpretable |
| RMSE | √mean((y-ŷ)²) | Yes | When large errors are especially bad |
| MAPE | mean(|y-ŷ|/y) × 100 | No | Percentage errors matter |
| R² | 1 - SS_res/SS_tot | No | Model fit quality (0-1, higher better) |

```python
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
import numpy as np

mae  = mean_absolute_error(y_test, y_pred)
rmse = np.sqrt(mean_squared_error(y_test, y_pred))
r2   = r2_score(y_test, y_pred)
mape = np.mean(np.abs((y_test - y_pred) / (y_test + 1e-10))) * 100
```

---

## SECTION 4: FEATURE ENGINEERING

### Q21. What is feature engineering? Most important techniques?

```python
import pandas as pd
import numpy as np
from sklearn.preprocessing import (
    StandardScaler, MinMaxScaler, RobustScaler,
    LabelEncoder, OneHotEncoder, OrdinalEncoder
)

# 1. Handling missing values
df['age'].fillna(df['age'].median(), inplace=True)
df['category'].fillna('Unknown', inplace=True)
# Or: use KNN imputer
from sklearn.impute import KNNImputer
imputer = KNNImputer(n_neighbors=5)
X_imputed = imputer.fit_transform(X)

# 2. Encoding categorical variables
# One-hot encoding (nominal):
ohe = OneHotEncoder(sparse=False, handle_unknown='ignore')
# Label/Ordinal encoding (ordinal):
oe = OrdinalEncoder(categories=[['low','medium','high']])
# Target encoding (high-cardinality):
target_mean = df.groupby('city')['target'].mean()
df['city_encoded'] = df['city'].map(target_mean)

# 3. Scaling (required for distance-based, linear, NN models)
StandardScaler()   # mean=0, std=1 — for normally distributed
MinMaxScaler()     # [0,1] — for bounded range
RobustScaler()     # uses median/IQR — for outliers

# 4. Feature creation
df['bmi']         = df['weight'] / (df['height'] ** 2)
df['age_group']   = pd.cut(df['age'], bins=[0,18,35,60,100], labels=['youth','adult','middle','senior'])
df['log_income']  = np.log1p(df['income'])
df['is_weekend']  = df['date'].dt.dayofweek.isin([5, 6]).astype(int)

# 5. Interaction features
df['age_x_bmi'] = df['age'] * df['bmi']

# 6. Polynomial features
from sklearn.preprocessing import PolynomialFeatures
poly = PolynomialFeatures(degree=2, interaction_only=False, include_bias=False)
```

---

### Q22. How do you select features?
```python
# 1. Univariate statistical tests
from sklearn.feature_selection import SelectKBest, f_classif, chi2, mutual_info_classif

selector = SelectKBest(score_func=mutual_info_classif, k=20)
X_selected = selector.fit_transform(X, y)

# 2. Recursive Feature Elimination (RFE)
from sklearn.feature_selection import RFECV
rfe = RFECV(estimator=RandomForestClassifier(), cv=5, scoring='roc_auc')
rfe.fit(X, y)
selected = [f for f, s in zip(feature_names, rfe.support_) if s]

# 3. Feature importance from tree models
rf.fit(X_train, y_train)
importances = pd.Series(rf.feature_importances_, index=feature_names).sort_values(ascending=False)
top_20 = importances.head(20).index.tolist()

# 4. SHAP values — model-agnostic, most reliable
import shap
explainer = shap.TreeExplainer(rf)
shap_values = explainer.shap_values(X_test)
shap.summary_plot(shap_values[1], X_test, feature_names=feature_names)

# 5. Variance Inflation Factor (VIF) — detect multicollinearity
from statsmodels.stats.outliers_influence import variance_inflation_factor
vif = pd.DataFrame({
    'Feature': feature_names,
    'VIF': [variance_inflation_factor(X, i) for i in range(X.shape[1])]
})
# Drop features with VIF > 10 (severe multicollinearity)
```

---

### Q23. How do you handle outliers?
```python
import numpy as np, pandas as pd
from scipy import stats

# 1. Detect with Z-score
z_scores = np.abs(stats.zscore(df[numeric_cols]))
outliers_z = (z_scores > 3).any(axis=1)

# 2. Detect with IQR
Q1, Q3 = df['feature'].quantile([0.25, 0.75])
IQR = Q3 - Q1
outliers_iqr = (df['feature'] < Q1 - 1.5*IQR) | (df['feature'] > Q3 + 1.5*IQR)

# 3. Treat
df['feature_capped'] = df['feature'].clip(lower=Q1 - 1.5*IQR, upper=Q3 + 1.5*IQR)
df['feature_log'] = np.log1p(df['feature'])  # log transform reduces outlier impact

# 4. Isolation Forest — multivariate outlier detection
from sklearn.ensemble import IsolationForest
iso = IsolationForest(contamination=0.05, random_state=42)
outlier_labels = iso.fit_predict(X)  # -1 = outlier, 1 = normal
```

---

## SECTION 5: UNSUPERVISED LEARNING

### Q24. K-Means Clustering — how it works, limitations?
**Answer:**
- Initialize k centroids randomly → assign each point to nearest centroid → update centroids → repeat until convergence
- **Limitations:** must specify k; sensitive to initialization; assumes spherical clusters; sensitive to outliers; fails on non-convex shapes

```python
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
import matplotlib.pyplot as plt

# Find optimal k using Elbow + Silhouette
inertias, silhouettes = [], []
K_range = range(2, 11)
for k in K_range:
    km = KMeans(n_clusters=k, n_init=10, random_state=42)
    km.fit(X_scaled)
    inertias.append(km.inertia_)
    silhouettes.append(silhouette_score(X_scaled, km.labels_))

# Silhouette score: 1=perfect, 0=overlapping, -1=wrong cluster
best_k = K_range[np.argmax(silhouettes)]
print(f"Best k by silhouette: {best_k}")

# Final model
km = KMeans(n_clusters=best_k, n_init=10, random_state=42)
labels = km.fit_predict(X_scaled)
```

---

### Q25. DBSCAN vs K-Means — when to use which?
**Answer:**

| | K-Means | DBSCAN |
|--|---------|--------|
| Cluster shape | Spherical only | Any shape |
| Outliers | Assigned to cluster | Marked as noise (-1) |
| k required | Yes | No |
| Scalability | High | Medium |
| Use when | Known k, spherical clusters | Unknown k, irregular shapes, outlier detection |

```python
from sklearn.cluster import DBSCAN

dbscan = DBSCAN(eps=0.5, min_samples=5)
labels = dbscan.fit_predict(X_scaled)
n_clusters = len(set(labels)) - (1 if -1 in labels else 0)
n_noise = (labels == -1).sum()
```

---

### Q26. PCA — what does it do and when do you use it?
```python
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# Always scale before PCA
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Find number of components to retain 95% variance
pca = PCA()
pca.fit(X_scaled)
cumvar = pca.explained_variance_ratio_.cumsum()
n_components = np.searchsorted(cumvar, 0.95) + 1

# Apply
pca = PCA(n_components=n_components)
X_pca = pca.fit_transform(X_scaled)

# Biplot to visualize
pca2 = PCA(n_components=2)
X_2d = pca2.fit_transform(X_scaled)
plt.scatter(X_2d[:, 0], X_2d[:, 1], c=y, cmap='viridis')
plt.xlabel(f'PC1 ({pca2.explained_variance_ratio_[0]:.1%})')
plt.ylabel(f'PC2 ({pca2.explained_variance_ratio_[1]:.1%})')
```

---

## SECTION 6: HYPERPARAMETER TUNING

### Q27. GridSearchCV vs RandomizedSearchCV vs Optuna?
```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
import optuna

# GridSearch — exhaustive, small parameter spaces
param_grid = {'max_depth': [3, 5, 7], 'n_estimators': [100, 200], 'learning_rate': [0.01, 0.1]}
gs = GridSearchCV(xgb.XGBClassifier(), param_grid, cv=5, scoring='roc_auc', n_jobs=-1)
gs.fit(X_train, y_train)
print(f"Best params: {gs.best_params_}, Best AUC: {gs.best_score_:.4f}")

# RandomizedSearch — faster for large spaces
rs = RandomizedSearchCV(
    xgb.XGBClassifier(), param_distributions={
        'max_depth': [3,4,5,6,7],
        'learning_rate': [0.01, 0.05, 0.1, 0.2],
        'n_estimators': [100, 200, 500],
        'subsample': [0.7, 0.8, 0.9],
    },
    n_iter=50, cv=5, scoring='roc_auc', n_jobs=-1
)

# Optuna — Bayesian optimization (best for complex spaces)
def objective(trial):
    params = {
        'max_depth': trial.suggest_int('max_depth', 3, 7),
        'learning_rate': trial.suggest_float('learning_rate', 0.01, 0.3, log=True),
        'n_estimators': trial.suggest_int('n_estimators', 100, 1000),
        'subsample': trial.suggest_float('subsample', 0.6, 1.0),
        'colsample_bytree': trial.suggest_float('colsample_bytree', 0.6, 1.0),
    }
    model = xgb.XGBClassifier(**params)
    return cross_val_score(model, X_train, y_train, cv=5, scoring='roc_auc').mean()

study = optuna.create_study(direction='maximize')
study.optimize(objective, n_trials=100)
print(f"Best: {study.best_params}")
```

---

## SECTION 7: DEEP LEARNING FUNDAMENTALS

### Q28. How does backpropagation work?
**Answer:**
- Forward pass: compute prediction and loss
- Backward pass: compute gradient of loss w.r.t. each weight using chain rule
- Update weights: w = w - learning_rate × ∂Loss/∂w

```python
import torch
import torch.nn as nn

class SimpleNet(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.ReLU(),
            nn.Dropout(0.3),
            nn.Linear(hidden_dim, hidden_dim // 2),
            nn.ReLU(),
            nn.Linear(hidden_dim // 2, output_dim)
        )

    def forward(self, x):
        return self.net(x)

model = SimpleNet(100, 256, 1)
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3, weight_decay=1e-4)
criterion = nn.BCEWithLogitsLoss()

# Training loop
for epoch in range(100):
    model.train()
    optimizer.zero_grad()
    output = model(X_batch).squeeze()
    loss = criterion(output, y_batch.float())
    loss.backward()          # compute gradients
    torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)  # gradient clipping
    optimizer.step()         # update weights
```

---

### Q29. Activation Functions — ReLU vs Sigmoid vs Tanh vs GELU?

| Function | Formula | Range | Pros | Cons | Use |
|----------|---------|-------|------|------|-----|
| Sigmoid | 1/(1+e⁻ˣ) | (0,1) | Probability output | Vanishing gradient, not zero-centered | Output layer (binary) |
| Tanh | (eˣ-e⁻ˣ)/(eˣ+e⁻ˣ) | (-1,1) | Zero-centered | Vanishing gradient | RNN hidden states |
| ReLU | max(0,x) | [0,∞) | Fast, no vanishing grad | Dying ReLU | Hidden layers (CNN/MLP) |
| Leaky ReLU | max(0.01x,x) | (-∞,∞) | Fixes dying ReLU | Extra hyperparameter | Alternative to ReLU |
| GELU | x·Φ(x) | (-∞,∞) | Smooth, stochastic | Slower | Transformers (BERT, GPT) |
| Softmax | eˣⁱ/Σeˣʲ | (0,1) | Probability distribution | — | Output layer (multi-class) |

```python
import torch.nn.functional as F

# Dying ReLU problem: neurons can get stuck outputting 0
# Fix: LeakyReLU or PReLU or initialize weights with He initialization

# He initialization (for ReLU)
nn.init.kaiming_normal_(layer.weight, mode='fan_in', nonlinearity='relu')
# Xavier/Glorot (for tanh/sigmoid)
nn.init.xavier_uniform_(layer.weight)
```

---

### Q30. Vanishing and Exploding Gradients — causes and fixes?
**Answer:**
- **Vanishing:** Gradients become tiny in deep networks → early layers don't learn
  - Causes: sigmoid/tanh activations, deep networks
  - Fixes: ReLU activations, batch normalization, residual connections, gradient clipping, LSTM/GRU

- **Exploding:** Gradients become very large → unstable training
  - Causes: large initial weights, RNNs with long sequences
  - Fixes: gradient clipping, weight regularization, careful initialization

```python
# Gradient clipping
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)

# Batch Normalization — normalizes layer inputs, reduces internal covariate shift
nn.BatchNorm1d(hidden_dim)  # for MLP
nn.BatchNorm2d(channels)    # for CNN

# Layer Normalization — used in Transformers (normalizes across features, not batch)
nn.LayerNorm(hidden_dim)

# Residual connection (ResNet) — enables gradient flow
class ResidualBlock(nn.Module):
    def __init__(self, dim):
        super().__init__()
        self.block = nn.Sequential(nn.Linear(dim, dim), nn.ReLU(), nn.Linear(dim, dim))
        self.norm = nn.LayerNorm(dim)

    def forward(self, x):
        return self.norm(x + self.block(x))   # skip connection
```

---

### Q31. Optimizers — SGD vs Adam vs AdamW?

```python
import torch.optim as optim

# SGD with momentum
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9, weight_decay=1e-4)

# Adam — adaptive learning rates per parameter
# β1=0.9 (momentum), β2=0.999 (RMS), ε=1e-8
optimizer = optim.Adam(model.parameters(), lr=1e-3, betas=(0.9, 0.999))

# AdamW — Adam + decoupled weight decay (preferred for Transformers)
optimizer = optim.AdamW(model.parameters(), lr=1e-4, weight_decay=0.01)

# Learning rate scheduling
scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=100)
# Or warmup + cosine decay (standard for Transformers):
scheduler = optim.lr_scheduler.OneCycleLR(
    optimizer, max_lr=1e-3,
    steps_per_epoch=len(train_loader), epochs=50
)

# When to use:
# SGD: image classification (often achieves best final accuracy)
# Adam/AdamW: default choice, NLP, fast convergence
# AdamW: Transformers and BERT-style models
```

---

### Q32. Dropout — how it works and when to use?
```python
# Dropout: randomly zero out neurons with probability p during training
# During inference: multiply by (1-p) or use inverted dropout (PyTorch default)
# Effect: prevents co-adaptation, acts as ensemble of 2^n subnetworks

model = nn.Sequential(
    nn.Linear(512, 256),
    nn.ReLU(),
    nn.Dropout(p=0.5),      # 50% dropout — typical for fully connected layers
    nn.Linear(256, 128),
    nn.ReLU(),
    nn.Dropout(p=0.3),      # 30% closer to output
    nn.Linear(128, 1)
)

# Important: always call model.train() and model.eval()
model.train()   # dropout active
model.eval()    # dropout disabled
with torch.no_grad():
    preds = model(X_test)
```

---

## SECTION 8: CONVOLUTIONAL NEURAL NETWORKS (CNNs)

### Q33. How does a CNN work? Key components?
**Answer:**
- **Conv Layer:** Applies filters (kernels) to detect spatial features; each filter learns a different feature (edges, textures, shapes)
- **Pooling:** Reduces spatial dimensions; Max pooling (most common) takes max in window
- **Batch Norm:** Normalizes outputs of each layer → faster, more stable training
- **Fully Connected:** Final layers for classification/regression

```python
import torch.nn as nn

class ConvNet(nn.Module):
    def __init__(self, n_classes):
        super().__init__()
        self.features = nn.Sequential(
            # Block 1
            nn.Conv2d(3, 32, kernel_size=3, padding=1),   # 3 channels in, 32 out
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.MaxPool2d(2, 2),        # halve spatial dims
            # Block 2
            nn.Conv2d(32, 64, kernel_size=3, padding=1),
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.MaxPool2d(2, 2),
            # Block 3
            nn.Conv2d(64, 128, kernel_size=3, padding=1),
            nn.BatchNorm2d(128),
            nn.ReLU(),
            nn.AdaptiveAvgPool2d((4, 4))  # fixed output size regardless of input
        )
        self.classifier = nn.Sequential(
            nn.Flatten(),
            nn.Linear(128 * 4 * 4, 512),
            nn.ReLU(),
            nn.Dropout(0.5),
            nn.Linear(512, n_classes)
        )

    def forward(self, x):
        return self.classifier(self.features(x))
```

---

### Q34. Transfer Learning — how to use pre-trained models?
```python
import torchvision.models as models
import torch.nn as nn

# Strategy 1: Feature extraction (freeze backbone, train only head)
model = models.resnet50(pretrained=True)
for param in model.parameters():
    param.requires_grad = False  # freeze all layers

# Replace final classifier
n_classes = 5
model.fc = nn.Sequential(
    nn.Linear(model.fc.in_features, 256),
    nn.ReLU(),
    nn.Dropout(0.3),
    nn.Linear(256, n_classes)
)
# Only model.fc parameters will update

# Strategy 2: Fine-tuning (unfreeze some layers)
for param in model.layer4.parameters():
    param.requires_grad = True  # unfreeze last block

# Use different learning rates per layer group
optimizer = optim.AdamW([
    {'params': model.layer4.parameters(), 'lr': 1e-4},  # lower for pretrained
    {'params': model.fc.parameters(), 'lr': 1e-3}        # higher for new head
])
```

---

### Q35. Popular CNN Architectures — quick comparison?

| Model | Year | Key Idea | Use Case |
|-------|------|----------|----------|
| AlexNet | 2012 | Deep CNN + ReLU + Dropout | Historical baseline |
| VGG16/19 | 2014 | Deep stack of 3×3 convs | Simple, reliable |
| ResNet | 2015 | Residual skip connections (solve vanishing gradient) | Standard backbone |
| InceptionNet | 2014 | Multi-scale conv (1×1, 3×3, 5×5 parallel) | Efficient |
| EfficientNet | 2019 | Compound scaling (depth + width + resolution) | SOTA efficiency |
| ViT | 2020 | Vision Transformer, patches as tokens | Large data, SOTA |

---

## SECTION 9: RECURRENT NEURAL NETWORKS & NLP

### Q36. RNN vs LSTM vs GRU — key differences?
**Answer:**
- **RNN:** Processes sequences, but suffers from vanishing gradient over long sequences
- **LSTM:** Adds cell state + 3 gates (input, forget, output) → can learn long-range dependencies
- **GRU:** Simplified LSTM with 2 gates (reset, update) → fewer parameters, often similar performance

```python
import torch.nn as nn

# LSTM
lstm = nn.LSTM(
    input_size=128,
    hidden_size=256,
    num_layers=2,
    dropout=0.3,
    bidirectional=True,  # processes sequence in both directions
    batch_first=True
)

# GRU (faster, fewer params)
gru = nn.GRU(input_size=128, hidden_size=256, batch_first=True)

# Usage
output, (hn, cn) = lstm(x)   # output: (batch, seq, hidden*2 if bidirectional)
# hn: final hidden state — use for classification
# output: all hidden states — use for sequence labeling
```

---

### Q37. Attention Mechanism — how it works?
**Answer:**
- Allows the model to focus on relevant parts of input when producing each output
- **Steps:**
  1. Compute Query (Q), Key (K), Value (V) from input
  2. Attention scores = softmax(QKᵀ / √d_k)
  3. Output = scores × V (weighted sum of values)

```python
import torch
import torch.nn.functional as F
import math

def scaled_dot_product_attention(Q, K, V, mask=None):
    d_k = Q.size(-1)
    scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(d_k)
    if mask is not None:
        scores = scores.masked_fill(mask == 0, float('-inf'))
    attn_weights = F.softmax(scores, dim=-1)
    return torch.matmul(attn_weights, V), attn_weights

# Multi-head attention: run h attention heads in parallel
class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.mha = nn.MultiheadAttention(d_model, n_heads, batch_first=True)

    def forward(self, q, k, v, mask=None):
        out, weights = self.mha(q, k, v, attn_mask=mask)
        return out
```

---

### Q38. Transformer Architecture — key components?
**Answer:**
```
Encoder Block (BERT-style):
  Input Embeddings + Positional Encoding
  → Multi-Head Self-Attention
  → Add & Norm (residual)
  → Feed-Forward Network (2-layer MLP)
  → Add & Norm

Decoder Block (GPT-style):
  Same as encoder + cross-attention to encoder output (seq2seq)
  GPT uses only decoder (causal/masked self-attention)
```

```python
class TransformerBlock(nn.Module):
    def __init__(self, d_model=512, n_heads=8, d_ff=2048, dropout=0.1):
        super().__init__()
        self.attn   = nn.MultiheadAttention(d_model, n_heads, dropout=dropout, batch_first=True)
        self.ff     = nn.Sequential(
            nn.Linear(d_model, d_ff), nn.GELU(), nn.Dropout(dropout),
            nn.Linear(d_ff, d_model)
        )
        self.norm1  = nn.LayerNorm(d_model)
        self.norm2  = nn.LayerNorm(d_model)
        self.drop   = nn.Dropout(dropout)

    def forward(self, x, mask=None):
        # Self-attention with residual connection
        attn_out, _ = self.attn(x, x, x, attn_mask=mask)
        x = self.norm1(x + self.drop(attn_out))
        # Feed-forward with residual connection
        x = self.norm2(x + self.drop(self.ff(x)))
        return x
```

---

### Q39. BERT vs GPT — key differences?

| | BERT | GPT |
|--|------|-----|
| Architecture | Encoder only | Decoder only |
| Training objective | Masked Language Modeling + NSP | Causal Language Modeling |
| Context | Bidirectional | Unidirectional (left to right) |
| Use case | Understanding tasks (classification, NER, QA) | Generation tasks |
| Fine-tuning | Add task-specific head + fine-tune | Prompt engineering or fine-tune |

```python
from transformers import (
    BertTokenizer, BertForSequenceClassification,
    AutoTokenizer, AutoModelForSequenceClassification,
    Trainer, TrainingArguments
)

# BERT fine-tuning for classification
tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased')
model = AutoModelForSequenceClassification.from_pretrained('bert-base-uncased', num_labels=2)

inputs = tokenizer(texts, padding=True, truncation=True, max_length=512, return_tensors='pt')
training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=3,
    per_device_train_batch_size=16,
    learning_rate=2e-5,           # typical for fine-tuning: 1e-5 to 5e-5
    warmup_ratio=0.1,
    weight_decay=0.01,
)
trainer = Trainer(model=model, args=training_args, train_dataset=train_ds, eval_dataset=val_ds)
trainer.train()
```

---

### Q40. Word Embeddings — Word2Vec vs GloVe vs FastText vs Transformer embeddings?

| | Word2Vec | GloVe | FastText | BERT embeddings |
|--|---------|-------|---------|----------------|
| Type | Predictive | Count-based | Character n-grams | Contextual |
| OOV words | No | No | Yes (subword) | Tokenizes subwords |
| Context | No | No | No | Yes (changes per context) |
| Training | Local window | Global matrix | Local window | Full document |
| Use | Fast, simple NLP | NLP tasks | Morphological languages | State of the art |

```python
from gensim.models import Word2Vec, FastText

# Word2Vec
sentences = [["the", "cat", "sat"], ["on", "the", "mat"]]
w2v = Word2Vec(sentences, vector_size=100, window=5, min_count=1, sg=1)  # sg=1: skip-gram
vector = w2v.wv['cat']
similar = w2v.wv.most_similar('king', topn=5)

# BERT contextual embeddings
from transformers import AutoTokenizer, AutoModel
import torch

tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased')
model = AutoModel.from_pretrained('bert-base-uncased')

inputs = tokenizer("The bank by the river", return_tensors='pt')
with torch.no_grad():
    outputs = model(**inputs)
# outputs.last_hidden_state: (batch, seq_len, 768) — contextual embeddings
# Use [CLS] token for sentence embedding: outputs.last_hidden_state[:, 0, :]
```

---

## SECTION 10: MLOPS & MODEL DEPLOYMENT

### Q41. How do you deploy an ML model to production?
```python
# Step 1: Save model
import joblib, mlflow

# sklearn
joblib.dump(model, 'model.pkl')

# PyTorch
torch.save(model.state_dict(), 'model.pt')

# MLflow — experiment tracking + model registry
with mlflow.start_run():
    mlflow.log_params({"max_depth": 5, "n_estimators": 200})
    mlflow.log_metrics({"auc": 0.92, "f1": 0.85})
    mlflow.sklearn.log_model(model, "model")
    mlflow.set_tag("version", "v1.0")

# Step 2: Serve with FastAPI
from fastapi import FastAPI
from pydantic import BaseModel
import joblib, numpy as np

app = FastAPI()
model = joblib.load('model.pkl')
scaler = joblib.load('scaler.pkl')

class PredictRequest(BaseModel):
    features: list[float]

@app.post("/predict")
def predict(req: PredictRequest):
    X = np.array(req.features).reshape(1, -1)
    X_scaled = scaler.transform(X)
    prob = model.predict_proba(X_scaled)[0, 1]
    return {"probability": round(float(prob), 4), "prediction": int(prob >= 0.5)}

# Step 3: Containerize
# FROM python:3.11-slim
# COPY requirements.txt .
# RUN pip install -r requirements.txt
# COPY model.pkl scaler.pkl app.py .
# CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

### Q42. What is data drift and model drift? How to detect and handle?
```python
# Data drift: input feature distribution changes over time
# Model drift (concept drift): relationship between features and target changes

# Detection with Evidently AI
from evidently.test_suite import TestSuite
from evidently.tests import TestNumberOfDriftedColumns
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=df_train, current_data=df_production)
report.save_html("drift_report.html")

# Simple statistical approach — KS test for numerical, chi-squared for categorical
from scipy.stats import ks_2samp, chi2_contingency

def detect_drift(train_col, prod_col, threshold=0.05):
    stat, p_value = ks_2samp(train_col, prod_col)
    return {"stat": stat, "p_value": p_value, "drifted": p_value < threshold}

# Model performance monitoring
def monitor_model(y_true, y_pred_prob, window='7d'):
    auc = roc_auc_score(y_true, y_pred_prob)
    # Alert if AUC drops > 5% from baseline
    if auc < baseline_auc * 0.95:
        trigger_retraining_pipeline()
```

---

### Q43. What is model explainability? SHAP vs LIME?
```python
# SHAP (SHapley Additive exPlanations) — game theory based, consistent
import shap

# Tree-based models (fast)
explainer = shap.TreeExplainer(xgb_model)
shap_values = explainer.shap_values(X_test)

# Global feature importance
shap.summary_plot(shap_values, X_test, feature_names=feature_names)

# Local explanation (single prediction)
shap.waterfall_plot(shap.Explanation(
    values=shap_values[0],
    base_values=explainer.expected_value,
    data=X_test[0],
    feature_names=feature_names
))

# For neural nets / model-agnostic
explainer = shap.KernelExplainer(model.predict_proba, shap.sample(X_train, 100))
shap_values = explainer.shap_values(X_test[:10])

# LIME — local linear approximation around single prediction
from lime.lime_tabular import LimeTabularExplainer
lime_exp = LimeTabularExplainer(X_train, feature_names=feature_names, class_names=['0','1'], mode='classification')
explanation = lime_exp.explain_instance(X_test[0], model.predict_proba, num_features=10)
explanation.show_in_notebook()

# SHAP vs LIME:
# SHAP: theoretically grounded, consistent, global + local — preferred
# LIME: faster for complex models, approximate, local only
```

---

### Q44. How do you build a complete ML pipeline with sklearn?
```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score
import joblib

numeric_features = ['age', 'bmi', 'blood_pressure']
categorical_features = ['gender', 'region', 'plan_type']

numeric_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler()),
])
categorical_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='constant', fill_value='Unknown')),
    ('encoder', OneHotEncoder(handle_unknown='ignore', sparse=False)),
])

preprocessor = ColumnTransformer([
    ('num', numeric_pipeline, numeric_features),
    ('cat', categorical_pipeline, categorical_features),
])

full_pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('model', RandomForestClassifier(n_estimators=200, random_state=42))
])

full_pipeline.fit(X_train, y_train)
scores = cross_val_score(full_pipeline, X, y, cv=5, scoring='roc_auc')
print(f"CV AUC: {scores.mean():.4f} ± {scores.std():.4f}")

# Save entire pipeline
joblib.dump(full_pipeline, 'pipeline.pkl')
# Load and predict (no separate scaler needed)
loaded = joblib.load('pipeline.pkl')
preds = loaded.predict_proba(X_new)[:, 1]
```

---

## SECTION 11: ADVANCED TOPICS

### Q45. What is the EM (Expectation-Maximization) Algorithm?
**Answer:**
- Used for models with latent (hidden) variables (e.g., GMM, HMM)
- **E-step:** Compute expected value of log-likelihood w.r.t. latent variables given current parameters
- **M-step:** Maximize expected log-likelihood to update parameters
- Guaranteed to converge to local optimum

```python
from sklearn.mixture import GaussianMixture

# GMM — soft clustering via EM
gmm = GaussianMixture(n_components=3, covariance_type='full', max_iter=200)
gmm.fit(X)
labels = gmm.predict(X)           # hard assignment
probs  = gmm.predict_proba(X)     # soft assignment (posterior)
```

---

### Q46. Explain Ensemble Methods — Bagging, Boosting, Stacking?

```python
# Bagging (Bootstrap Aggregating): parallel, reduces variance
# → Random Forest, Extra Trees

# Boosting: sequential, reduces bias
# → AdaBoost, GBM, XGBoost, LightGBM, CatBoost

# Stacking: use predictions of base models as features for a meta-model
from sklearn.ensemble import StackingClassifier

estimators = [
    ('rf', RandomForestClassifier(n_estimators=100)),
    ('xgb', xgb.XGBClassifier(n_estimators=100)),
    ('svm', SVC(probability=True))
]
stack = StackingClassifier(
    estimators=estimators,
    final_estimator=LogisticRegression(),
    cv=5,
    stack_method='predict_proba'
)
stack.fit(X_train, y_train)
```

---

### Q47. What is Batch Normalization vs Layer Normalization?
```python
# Batch Norm: normalizes across batch dimension (per feature)
# → Used in CNNs; sensitive to batch size; doesn't work well with small batches
nn.BatchNorm2d(num_features=64)

# Layer Norm: normalizes across feature dimension (per sample)
# → Used in Transformers, RNNs; independent of batch size
nn.LayerNorm(normalized_shape=512)

# Instance Norm: normalizes across spatial dimensions per channel per sample
# → Used in style transfer
nn.InstanceNorm2d(num_features=64)

# Group Norm: normalizes within groups of channels
# → Useful when batch size is very small
nn.GroupNorm(num_groups=32, num_channels=64)
```

---

### Q48. What is the difference between generative and discriminative models?

| | Discriminative | Generative |
|--|---------------|-----------|
| Models | P(y|x) | P(x,y) = P(x|y)P(y) |
| Examples | Logistic Regression, SVM, Neural Networks | Naive Bayes, GMM, VAE, GAN, LLMs |
| Task | Classification/regression | Generation, density estimation |
| Data needed | Less | More |
| Handles missing features | No | Yes (can sample) |

---

### Q49. Explain GANs — how they work and training challenges?
```python
import torch.nn as nn

class Generator(nn.Module):
    def __init__(self, latent_dim, output_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(latent_dim, 256), nn.LeakyReLU(0.2),
            nn.Linear(256, 512),        nn.LeakyReLU(0.2),
            nn.Linear(512, output_dim), nn.Tanh()
        )
    def forward(self, z): return self.net(z)

class Discriminator(nn.Module):
    def __init__(self, input_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, 512), nn.LeakyReLU(0.2), nn.Dropout(0.3),
            nn.Linear(512, 256),       nn.LeakyReLU(0.2), nn.Dropout(0.3),
            nn.Linear(256, 1)
        )
    def forward(self, x): return self.net(x)

# Training challenges:
# - Mode collapse: G produces same output for all z
# - Training instability: D too strong → G gets no gradient
# - Solutions: WGAN-GP (Wasserstein + gradient penalty), feature matching,
#              spectral normalization, two timescale updates
```

---

### Q50. VAEs (Variational Autoencoders) — architecture and loss?
```python
class VAE(nn.Module):
    def __init__(self, input_dim, latent_dim):
        super().__init__()
        self.encoder = nn.Sequential(nn.Linear(input_dim, 256), nn.ReLU())
        self.mu_layer  = nn.Linear(256, latent_dim)
        self.log_var   = nn.Linear(256, latent_dim)
        self.decoder = nn.Sequential(nn.Linear(latent_dim, 256), nn.ReLU(), nn.Linear(256, input_dim))

    def reparameterize(self, mu, log_var):
        std = torch.exp(0.5 * log_var)
        eps = torch.randn_like(std)
        return mu + eps * std  # reparameterization trick

    def forward(self, x):
        h = self.encoder(x)
        mu, log_var = self.mu_layer(h), self.log_var(h)
        z = self.reparameterize(mu, log_var)
        return self.decoder(z), mu, log_var

def vae_loss(recon_x, x, mu, log_var):
    recon_loss = F.mse_loss(recon_x, x, reduction='sum')
    kl_loss = -0.5 * torch.sum(1 + log_var - mu.pow(2) - log_var.exp())
    return recon_loss + kl_loss  # ELBO loss
```

---

## PART 2: QUICK REFERENCE — TOP 50 MOST ASKED QUESTIONS

| # | Question | Topic | Frequency |
|---|----------|-------|-----------|
| 1 | Bias-variance tradeoff | Fundamentals | 🔴 |
| 2 | Overfitting and how to prevent | Fundamentals | 🔴 |
| 3 | L1 vs L2 regularization | Regularization | 🔴 |
| 4 | Precision vs Recall vs F1 | Metrics | 🔴 |
| 5 | Handle class imbalance | Imbalanced data | 🔴 |
| 6 | Random Forest how it works | Ensemble | 🔴 |
| 7 | XGBoost vs Random Forest | Ensemble | 🔴 |
| 8 | Cross-validation types | Evaluation | 🔴 |
| 9 | Feature importance methods | Feature Eng | 🔴 |
| 10 | How to handle missing values | Feature Eng | 🔴 |
| 11 | ROC-AUC vs PR-AUC | Metrics | 🔴 |
| 12 | When to use which algorithm | Algorithms | 🔴 |
| 13 | Linear vs Logistic Regression difference | Algorithms | 🔴 |
| 14 | Decision Tree — Gini vs Entropy | Algorithms | 🔴 |
| 15 | K-Means — choosing k | Clustering | 🔴 |
| 16 | Backpropagation — how it works | Deep Learning | 🟡 |
| 17 | Vanishing gradient — causes and fixes | Deep Learning | 🟡 |
| 18 | ReLU vs Sigmoid | Deep Learning | 🟡 |
| 19 | Batch Norm vs Layer Norm | Deep Learning | 🟡 |
| 20 | Adam vs SGD | Optimization | 🟡 |
| 21 | Dropout — how and when | Regularization DL | 🟡 |
| 22 | CNN architecture components | CNN | 🟡 |
| 23 | Transfer learning strategies | Transfer Learning | 🟡 |
| 24 | LSTM vs GRU | RNN | 🟡 |
| 25 | Attention mechanism | NLP / Transformer | 🟡 |
| 26 | BERT vs GPT | NLP | 🟡 |
| 27 | Word2Vec vs BERT embeddings | NLP | 🟡 |
| 28 | SHAP vs LIME | Explainability | 🟡 |
| 29 | Data drift detection | MLOps | 🟡 |
| 30 | Model deployment best practices | MLOps | 🟡 |
| 31 | PCA — when and how | Unsupervised | 🟡 |
| 32 | DBSCAN vs K-Means | Clustering | 🟡 |
| 33 | SVM kernel trick | Algorithms | 🟡 |
| 34 | Ensemble — bagging vs boosting vs stacking | Ensemble | 🟡 |
| 35 | Naive Bayes — when it works | Algorithms | 🟡 |
| 36 | Curse of dimensionality | Fundamentals | 🟡 |
| 37 | Hyperparameter tuning methods | Tuning | 🟡 |
| 38 | Handling outliers | Feature Eng | 🟡 |
| 39 | Categorical encoding methods | Feature Eng | 🟡 |
| 40 | Regularization in neural networks | Deep Learning | 🟡 |
| 41 | Transformer architecture | Deep Learning | 🟡 |
| 42 | Fine-tuning a pre-trained model | Transfer Learning | 🟡 |
| 43 | GANs — training challenges | Generative | 🟢 |
| 44 | VAE vs GAN | Generative | 🟢 |
| 45 | Reinforcement Learning basics | RL | 🟢 |
| 46 | Graph Neural Networks | GNN | 🟢 |
| 47 | A/B testing for ML models | Experimentation | 🟡 |
| 48 | Calibration of probability estimates | Metrics | 🟡 |
| 49 | Class imbalance in deep learning | Deep Learning | 🟡 |
| 50 | Learning rate scheduling | Optimization | 🟡 |
