# Phishing Website Detection

A machine learning project that detects phishing websites using a **Random Forest Regressor** trained on the UCI/Kaggle Phishing Websites dataset.

## Repository contents

| File | Description |
|------|-------------|
| `phishing_detection.ipynb` | Complete notebook: data loading, preprocessing, training, evaluation and prediction |
| `data/dataset.csv` | Phishing websites dataset (11,055 rows, 30 website features + `Result` label) |
| `requirements.txt` | Python libraries needed to run the notebook |
| `models/phishing_random_forest_regressor.joblib` | Trained model (Random Forest + imputer + selected features) |

## Dataset

Each row describes a website using 30 encoded features (for example, IP address in URL, URL length, SSL state, domain age, web traffic).
The original label `Result` uses `-1` for phishing and `1` for legitimate websites.

## Workflow

1. Import libraries
2. Load and understand the dataset
3. Check for missing values (median imputation)
4. Remove duplicate rows and create the target (`1` = phishing, `0` = legitimate)
5. Stratified 80/20 train–test split
6. Feature selection – top 20 features by correlation with the target (training data only)
7. Train a Random Forest Regressor (300 trees, `min_samples_leaf=2`, `random_state=42`)
8. Predict phishing scores and convert them to classes using a 0.5 threshold
9. Evaluate with MAE, RMSE, R², accuracy, classification report and confusion matrix
10. Plot feature importance
11. Save the model with `joblib`
12. Reload the model and predict on a new record

## Results

| Metric | Value |
|--------|-------|
| Classification accuracy | 96.79% |
| MAE | 0.0595 |
| RMSE | 0.1664 |
| R² | 0.8879 |
| Precision / Recall (phishing) | 0.97 / 0.96 |

## How to run

```bash
git clone https://github.com/LIGNESHWER/Phishing-Website-Detection.git
cd Phishing-Website-Detection
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Linux / macOS
pip install -r requirements.txt
```

Open `phishing_detection.ipynb` in Jupyter or VS Code and run all cells.
Running the notebook retrains the model and overwrites `models/phishing_random_forest_regressor.joblib`.

### Using the saved model

```python
import joblib, pandas as pd
art = joblib.load("models/phishing_random_forest_regressor.joblib")
row = pd.read_csv("data/dataset.csv").drop(columns=["index", "Result"]).iloc[[0]]
x = pd.DataFrame(art["imputer"].transform(row[art["all_features"]]), columns=art["all_features"])[art["features"]]
score = art["model"].predict(x)[0]
print("phishing" if score >= art["threshold"] else "legitimate", round(score, 4))
```

### Google Colab

Upload `phishing_detection.ipynb`, create a `data` folder, and upload `dataset.csv` into it, then run all cells.

## Tech stack

Python · pandas · NumPy · scikit-learn · Matplotlib · joblib
