# Top 100 Data Science / ML Python Interview Questions
> Focus: 5–15 Years | Non-FAANG | Service, Data Analytics, Healthcare, Enterprise ML
> Companies: ZS Associates, Tiger Analytics, Fractal, EXL, Mu Sigma, Databricks, Snowflake, Carelon, Optum

---

## SECTION 1: Pandas & Data Processing (Q1–Q25)

### Q1
- **Question:** What are common Pandas performance pitfalls? How do you vectorize operations?
- **Level:** 5–8 Yrs | **Company:** All data companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `iterrows()` is the #1 anti-pattern — O(n) Python loop; use vectorized operations
  - `.apply()` with Python function is slow — use NumPy or built-in Pandas ops
  - String operations: use `.str` accessor methods; avoid Python `for` loops
  - `groupby().apply()` with Python function — use `.agg()` or `.transform()` with built-ins
  - Avoid chained indexing (`df['col'][0]`) — causes `SettingWithCopyWarning`
  - Use `pd.eval()` for complex expressions on large DataFrames (uses numexpr)
- **Code Fix:**
  ```python
  # Slow
  df['result'] = df.apply(lambda row: row['a'] * row['b'], axis=1)

  # Fast — vectorized
  df['result'] = df['a'] * df['b']
  ```
- **Difficulty:** Medium

---

### Q2
- **Question:** How does `groupby().agg()` differ from `groupby().transform()`? When do you use each?
- **Level:** 5–8 Yrs | **Company:** Data/Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # agg: reduces group to single row
  df.groupby('plan_code')['amount'].agg(['sum', 'mean', 'count'])

  # transform: returns same-shaped DataFrame (broadcasts back to original index)
  df['group_total'] = df.groupby('plan_code')['amount'].transform('sum')
  df['pct_of_group'] = df['amount'] / df['group_total']
  ```
- **Answer Points:** `transform` is powerful for adding group-level stats as new columns; essential for feature engineering in ML
- **Difficulty:** Medium

---

### Q3
- **Question:** How do you handle missing values in Pandas? What strategies exist?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Detection
  df.isnull().sum()
  df.isnull().mean()  # proportion missing per column

  # Drop
  df.dropna(subset=['required_col'])

  # Fill with strategy
  df['age'].fillna(df['age'].median(), inplace=True)
  df['status'].fillna('unknown', inplace=True)
  df['amount'].fillna(method='ffill', inplace=True)  # forward fill time series

  # Interpolate for time series
  df['metric'].interpolate(method='time')
  ```
- **Answer Points:** Missing at random vs not at random matters for imputation strategy; never just drop without understanding why data is missing
- **Difficulty:** Easy

---

### Q4
- **Question:** How do you read a 10GB CSV file in Pandas without running out of memory?
- **Level:** 5–8 Yrs | **Company:** All data companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Option 1: chunked reading
  chunks = []
  for chunk in pd.read_csv('large_file.csv', chunksize=100_000,
                            usecols=['col1', 'col2', 'col3'],
                            dtype={'amount': 'float32', 'status': 'category'}):
      chunk = chunk[chunk['status'] == 'active']
      chunks.append(chunk.groupby('plan')['amount'].sum())
  result = pd.concat(chunks).groupby(level=0).sum()

  # Option 2: use polars (Rust-based, 10-100x faster, lazy evaluation)
  import polars as pl
  df = pl.scan_csv('large_file.csv').filter(pl.col('status') == 'active').collect()
  ```
- **Answer Points:** `usecols` to load only needed columns; `dtype` to use smaller types; `category` for low-cardinality strings
- **Difficulty:** Medium

---

### Q5
- **Question:** What is `pd.merge()` vs `pd.join()` vs `pd.concat()`? When do you use each?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `pd.merge()` — SQL-style join on columns; flexible; handles all join types
  - `df.join()` — joins on index by default; convenient but less flexible
  - `pd.concat()` — stacks DataFrames vertically (rows) or horizontally (cols); no key matching
  - `merge` is most common in practice; use `how='left'`/`'inner'`/`'outer'`
- **Difficulty:** Easy

---

### Q6
- **Question:** How do you pivot a DataFrame? What is `melt`? Write an example for claims data.
- **Level:** 5–8 Yrs | **Company:** Healthcare, Data | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Pivot: from long to wide
  pivot = df.pivot_table(
      index='member_id',
      columns='claim_type',
      values='amount',
      aggfunc='sum',
      fill_value=0
  )

  # Melt: from wide to long (opposite of pivot)
  melted = pd.melt(
      pivot.reset_index(),
      id_vars=['member_id'],
      value_vars=['medical', 'dental', 'vision'],
      var_name='claim_type',
      value_name='total_amount'
  )
  ```
- **Difficulty:** Medium

---

### Q7–Q25 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 7 | How do you detect and remove outliers in a Pandas DataFrame? | 5–8 Yrs | High | T1 |
| 8 | How do you work with datetime columns in Pandas? Resample to monthly? | 5–8 Yrs | High | T1 |
| 9 | What is `pd.Categorical`? How does it save memory and improve performance? | 5–8 Yrs | Medium | T2 |
| 10 | How do you chain Pandas operations readably? (`pipe`, method chaining) | 5–8 Yrs | Medium | T2 |
| 11 | How do you apply a function to multiple columns at once in Pandas? | 5–8 Yrs | High | T1 |
| 12 | How does `pd.read_sql()` work? How do you use it with SQLAlchemy? | 5–8 Yrs | High | T1 |
| 13 | What is `pd.get_dummies()`? How do you use it for categorical encoding? | 5–8 Yrs | High | T1 |
| 14 | How do you compute a rolling average in Pandas for time series? | 5–8 Yrs | High | T1 |
| 15 | How do you use `pd.cut()` and `pd.qcut()` for binning continuous variables? | 5–8 Yrs | Medium | T2 |
| 16 | What is `SettingWithCopyWarning`? How do you fix it? | 5–8 Yrs | High | T1 |
| 17 | How do you compare two DataFrames and find differences? | 5–8 Yrs | High | T1 |
| 18 | How does Polars differ from Pandas? When would you switch? | 8–12 Yrs | Medium | T2 |
| 19 | How do you profile Pandas performance? (`%timeit`, `pd.options.mode.chained_assignment`) | 8–12 Yrs | Medium | T2 |
| 20 | What is `pd.eval()` and `df.query()`? When are they faster? | 8–12 Yrs | Medium | T2 |
| 21 | How do you handle time zones in Pandas datetime operations? | 5–8 Yrs | High | T1 |
| 22 | What is Arrow-backed Pandas (pandas 2.0)? How does it improve performance? | 8–12 Yrs | Medium | T2 |
| 23 | How do you read Parquet files with Pandas? Why is Parquet better than CSV? | 5–8 Yrs | High | T1 |
| 24 | How do you implement a sliding window feature in Pandas? | 8–12 Yrs | Medium | T2 |
| 25 | How do you read data from an API and process it into a DataFrame? | 5–8 Yrs | High | T1 |

---

## SECTION 2: NumPy & Scientific Computing (Q26–Q40)

### Q26
- **Question:** Why is NumPy faster than Python lists? Explain vectorization.
- **Level:** 5–8 Yrs | **Company:** All ML companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - NumPy arrays: contiguous memory, homogeneous dtype, SIMD operations
  - Operations implemented in C/Fortran; no Python overhead per element
  - Broadcasting: apply ops across arrays of different shapes without loops
  - Vectorization: replace `for` loops with array ops that run in compiled code
- **Difficulty:** Easy

---

### Q27
- **Question:** What is NumPy broadcasting? Write an example.
- **Level:** 5–8 Yrs | **Company:** All ML | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Without broadcasting (slow)
  result = np.array([a[i] + b for i in range(len(a))])

  # With broadcasting (fast)
  a = np.array([[1, 2, 3], [4, 5, 6]])  # shape (2, 3)
  b = np.array([10, 20, 30])             # shape (3,)
  result = a + b                          # broadcasts b across rows → shape (2, 3)
  ```
- **Difficulty:** Easy

---

### Q28–Q40 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 28 | What is `np.where()`? Give a real use case for conditional array operations. | 5–8 Yrs | High | T1 |
| 29 | How do you use `np.vectorize()`? When is it NOT faster than a Python loop? | 8–12 Yrs | Medium | T2 |
| 30 | What is `np.einsum()`? When would you use it? | 8–12 Yrs | Low | T3 |
| 31 | How do you compute cosine similarity between two NumPy vectors? | 5–8 Yrs | High | T1 |
| 32 | How do you implement matrix multiplication in NumPy? | 5–8 Yrs | High | T1 |
| 33 | What is the difference between `np.copy()` and view (`arr[:]`)? | 5–8 Yrs | Medium | T2 |
| 34 | How do you generate random arrays with fixed seeds for reproducibility? | 5–8 Yrs | High | T1 |
| 35 | How do you reshape and flatten NumPy arrays? | 5–8 Yrs | High | T1 |
| 36 | How do you use `np.linalg` for PCA (principal component analysis)? | 8–12 Yrs | Medium | T2 |
| 37 | How does NumPy handle integer overflow vs Python? | 8–12 Yrs | Medium | T2 |
| 38 | How do you use structured arrays in NumPy? | 8–12 Yrs | Low | T3 |
| 39 | How do you save and load NumPy arrays efficiently? (`np.save`, `np.memmap`) | 8–12 Yrs | Medium | T2 |
| 40 | What is `np.memmap`? How do you use it for large arrays? | 8–12 Yrs | Low | T3 |

---

## SECTION 3: scikit-learn & ML Engineering (Q41–Q70)

### Q41
- **Question:** What is a scikit-learn pipeline? How does it prevent data leakage?
- **Level:** 5–8 Yrs | **Company:** All ML companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from sklearn.pipeline import Pipeline
  from sklearn.preprocessing import StandardScaler
  from sklearn.impute import SimpleImputer
  from sklearn.ensemble import RandomForestClassifier

  pipeline = Pipeline([
      ('imputer', SimpleImputer(strategy='median')),
      ('scaler', StandardScaler()),
      ('classifier', RandomForestClassifier(n_estimators=100, random_state=42))
  ])

  # Fit only on training data
  pipeline.fit(X_train, y_train)
  # Transform uses training statistics on test data
  y_pred = pipeline.predict(X_test)
  ```
- **Answer Points:** Pipeline prevents leakage by ensuring `fit_transform` only on train set; `transform` on test; cross-validation inside pipeline also correct
- **Difficulty:** Medium

---

### Q42
- **Question:** What is cross-validation? How do you use `cross_val_score` correctly?
- **Level:** 5–8 Yrs | **Company:** All ML | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from sklearn.model_selection import StratifiedKFold, cross_val_score

  cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
  scores = cross_val_score(pipeline, X, y, cv=cv, scoring='roc_auc', n_jobs=-1)
  print(f"AUC: {scores.mean():.4f} ± {scores.std():.4f}")
  ```
- **Answer Points:** Stratified for class imbalance; use pipeline not raw estimator (to include preprocessing in each fold); `n_jobs=-1` for parallel
- **Difficulty:** Medium

---

### Q43
- **Question:** What is feature drift? How do you monitor it in production?
- **Level:** 8–12 Yrs | **Company:** ML companies, Healthcare ML | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Feature drift: distribution of input features changes over time vs training data
  - Covariate shift: input X distribution changes; concept drift: P(Y|X) changes
  - Detection: PSI (Population Stability Index), KS test, Jensen-Shannon divergence
  - Production: monitor feature distribution statistics per time window; alert on PSI > 0.2
  - Tools: `evidently`, `whylogs`, `great_expectations`
- **Difficulty:** Hard

---

### Q44
- **Question:** How do you handle class imbalance in a classification problem?
- **Level:** 5–8 Yrs | **Company:** Healthcare ML | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `class_weight='balanced'` in scikit-learn classifiers
  - Oversampling: SMOTE (`imbalanced-learn`)
  - Undersampling: RandomUnderSampler
  - Use appropriate metric: not accuracy — use precision/recall/F1/AUC-PR
  - Threshold tuning: optimize decision threshold for business cost function
- **Difficulty:** Medium

---

### Q45
- **Question:** How do you explain a machine learning model's predictions? (SHAP, LIME)
- **Level:** 8–12 Yrs | **Company:** Healthcare, ZS Associates | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import shap

  explainer = shap.TreeExplainer(model)
  shap_values = explainer.shap_values(X_test)

  # Summary plot — global feature importance
  shap.summary_plot(shap_values, X_test)

  # Force plot — single prediction explanation
  shap.force_plot(explainer.expected_value, shap_values[0], X_test.iloc[0])
  ```
- **Answer Points:** SHAP: game-theoretic feature attribution; consistent + locally accurate; works for tree models (fast) and model-agnostic; required for model governance in regulated industries
- **Difficulty:** Medium

---

### Q46–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 46 | What metrics do you use for a binary classification problem? When is AUC-ROC vs AUC-PR better? | 5–8 Yrs | High | T1 |
| 47 | What is hyperparameter tuning? Compare GridSearchCV vs RandomizedSearchCV vs Optuna. | 8–12 Yrs | High | T1 |
| 48 | How do you prevent overfitting in a machine learning model? | 5–8 Yrs | High | T1 |
| 49 | What is feature selection? How do you use `SelectFromModel` in scikit-learn? | 5–8 Yrs | Medium | T2 |
| 50 | How do you deploy a scikit-learn model as a FastAPI service? | 8–12 Yrs | High | T1 |
| 51 | What is MLflow? How do you use it for experiment tracking and model registry? | 8–12 Yrs | High | T1 |
| 52 | How do you implement model versioning and promotion in production? | 8–12 Yrs | High | T1 |
| 53 | What is A/B testing for ML models? How do you implement it? | 8–12 Yrs | High | T1 |
| 54 | How do you implement shadow mode for a new ML model before rollout? | 12–15 Yrs | Medium | T2 |
| 55 | How do you detect model degradation in production? | 8–12 Yrs | High | T1 |
| 56 | What is `ColumnTransformer`? How do you apply different preprocessing to different columns? | 5–8 Yrs | High | T1 |
| 57 | How do you implement a time-series split for cross-validation? | 8–12 Yrs | High | T1 |
| 58 | What is target encoding? When is it better than one-hot encoding? | 8–12 Yrs | Medium | T2 |
| 59 | How do you handle high-cardinality categorical features? | 8–12 Yrs | High | T1 |
| 60 | How do you build a fraud detection model for insurance claims in Python? | 12–15 Yrs | High | T1 |
| 61 | What is `joblib.Parallel`? How do you use it for parallel ML? | 8–12 Yrs | Medium | T2 |
| 62 | How do you serialize and load a scikit-learn model? (`joblib`, `pickle`, ONNX) | 5–8 Yrs | High | T1 |
| 63 | What is feature store? How do you implement one in Python? | 12–15 Yrs | Medium | T2 |
| 64 | What is `great_expectations`? How do you validate data quality in ML pipelines? | 8–12 Yrs | Medium | T2 |
| 65 | How do you build a retraining pipeline for a production ML model? | 8–12 Yrs | High | T1 |
| 66 | What is XGBoost? How do you tune it in Python? | 5–8 Yrs | High | T1 |
| 67 | What is LightGBM? How does it differ from XGBoost? | 8–12 Yrs | Medium | T2 |
| 68 | How do you implement a recommendation engine using collaborative filtering? | 8–12 Yrs | Medium | T2 |
| 69 | What is t-SNE? How do you use it for high-dimensional data visualization? | 8–12 Yrs | Medium | T2 |
| 70 | How do you build a Python NLP pipeline (tokenize, vectorize, classify)? | 5–8 Yrs | High | T1 |

---

## SECTION 4: Data Engineering & Pipelines (Q71–Q100)

### Q71
- **Question:** What is PySpark? How does it differ from Pandas?
- **Level:** 8–12 Yrs | **Company:** Data engineering companies, Databricks | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - PySpark: distributed computing framework; splits data across cluster nodes
  - Pandas: single-machine, in-memory; simpler API
  - PySpark lazy: builds execution plan; optimizes via Catalyst optimizer
  - Use PySpark: data > single machine memory (100GB+); Databricks/EMR clusters
  - PySpark DataFrames have SQL-like API similar to Pandas
- **Difficulty:** Medium

---

### Q72
- **Question:** How do you optimize a PySpark job? What are common pitfalls?
- **Level:** 8–12 Yrs | **Company:** Databricks, Data companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Avoid `collect()` on large datasets — brings everything to driver
  - Use `cache()`/`persist()` wisely — don't cache what you won't reuse
  - Partition pruning: filter early; push predicates down
  - Avoid wide transformations (joins, groupBy) on skewed data
  - `broadcast()` small DataFrames in joins
  - Use Parquet not CSV; predicate pushdown works with Parquet
  - Monitor via Spark UI; look for long stages, skewed partitions
- **Difficulty:** Hard

---

### Q73
- **Question:** What is Apache Airflow? How do you define a Python DAG?
- **Level:** 8–12 Yrs | **Company:** Data engineering companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from airflow import DAG
  from airflow.operators.python import PythonOperator
  from datetime import datetime, timedelta

  def extract(**context):
      data = read_from_source()
      context['task_instance'].xcom_push('data', data)

  def transform(**context):
      data = context['task_instance'].xcom_pull('data', task_ids='extract')
      return process(data)

  with DAG(
      'claims_etl',
      schedule_interval='0 2 * * *',  # daily at 2am
      start_date=datetime(2024, 1, 1),
      catchup=False,
      default_args={'retries': 2, 'retry_delay': timedelta(minutes=5)}
  ) as dag:
      extract_task = PythonOperator(task_id='extract', python_callable=extract)
      transform_task = PythonOperator(task_id='transform', python_callable=transform)
      extract_task >> transform_task
  ```
- **Difficulty:** Medium

---

### Q74–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 74 | What is Delta Lake? How does it add ACID transactions to data lakes? | 8–12 Yrs | High | T1 |
| 75 | What is dbt? How do you use it for SQL-based transformations in Python? | 8–12 Yrs | High | T1 |
| 76 | How do you implement data validation in a Python ETL pipeline? | 8–12 Yrs | High | T1 |
| 77 | How do you implement incremental data loading vs full refresh? | 8–12 Yrs | High | T1 |
| 78 | What is data lineage? How do you track it in Python pipelines? | 8–12 Yrs | Medium | T2 |
| 79 | How do you handle schema evolution in a data pipeline? | 8–12 Yrs | High | T1 |
| 80 | What is `Prefect`? How does it differ from Airflow? | 8–12 Yrs | Medium | T2 |
| 81 | How do you implement a CDC (Change Data Capture) pipeline with Python? | 8–12 Yrs | Medium | T2 |
| 82 | How do you process Parquet files efficiently in Python? | 5–8 Yrs | High | T1 |
| 83 | How do you implement data quality checks with `great_expectations`? | 8–12 Yrs | Medium | T2 |
| 84 | How do you use `dask` for parallel Pandas-like operations? | 8–12 Yrs | Medium | T2 |
| 85 | How do you handle late-arriving data in a streaming pipeline? | 8–12 Yrs | Medium | T2 |
| 86 | How does Kafka Python producer/consumer work? | 8–12 Yrs | High | T1 |
| 87 | What is `avro` schema for Kafka messages? How do you use `fastavro`? | 8–12 Yrs | Medium | T2 |
| 88 | How do you implement a Python consumer group for Kafka? | 8–12 Yrs | High | T1 |
| 89 | How do you implement backpressure in a Python data pipeline? | 8–12 Yrs | Medium | T2 |
| 90 | How do you implement a file-based ETL with error tracking and recovery? | 8–12 Yrs | High | T1 |
| 91 | What is `pyarrow`? How does it improve data interchange? | 8–12 Yrs | Medium | T2 |
| 92 | How do you implement SCD (Slowly Changing Dimensions) in Python? | 8–12 Yrs | Medium | T2 |
| 93 | How do you orchestrate ML model training with Airflow + MLflow? | 8–12 Yrs | Medium | T2 |
| 94 | How do you monitor a data pipeline for data quality in production? | 8–12 Yrs | High | T1 |
| 95 | How do you implement a Python-based feature engineering pipeline for tabular ML? | 8–12 Yrs | High | T1 |
| 96 | How does `cuDF` (RAPIDS) accelerate Pandas operations on GPUs? | 12–15 Yrs | Low | T3 |
| 97 | How do you implement a streaming feature pipeline for real-time ML inference? | 12–15 Yrs | Medium | T2 |
| 98 | How do you implement model serving at scale with Python? (Triton, vLLM, BentoML) | 12–15 Yrs | Medium | T2 |
| 99 | How do you implement responsible AI practices in a Python ML pipeline? | 12–15 Yrs | High | T1 |
| 100 | Design a complete Python ML platform for healthcare claims fraud detection: data pipeline, feature store, model training, serving, monitoring, and retraining. | 12–15 Yrs | High | T1 |
