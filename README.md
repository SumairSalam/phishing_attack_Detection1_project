# Phishing URL Detection Using Machine Learning

A machine learning project that classifies websites as **phishing** or **legitimate** using 30 website features. It includes a model comparison notebook, URL feature extraction code, and a Flask application scaffold.

## Project Overview

Phishing websites imitate trusted services to steal sensitive information. This project explores how machine learning can identify suspicious websites using URL structure, domain information, and webpage characteristics.

The workflow includes:

1. Loading and exploring the dataset.
2. Splitting data into training and test sets.
3. Training five classification models.
4. Comparing model performance.
5. Extracting features from submitted URLs for inference.

## Technologies

- **Language:** Python
- **Data processing:** Pandas, NumPy
- **Machine learning:** scikit-learn
- **Visualization:** Matplotlib, Seaborn
- **Web application:** Flask
- **Feature extraction:** Requests, BeautifulSoup, WHOIS
- **Development:** Jupyter Notebook

## Dataset

The included `phishing.csv` contains **11,054 records**, with:

- 30 input features
- One target column: `class`
- One index column, removed before training

### Target Labels

| Label | Meaning |
|-------|---------|
| `1` | Legitimate website |
| `-1` | Phishing website |

The notebook attributes the dataset to [Phishing Website Detector on Kaggle](https://www.kaggle.com/eswarchandt/phishing-website-detector).

### Feature Categories

| Category | Examples |
|----------|----------|
| URL structure | URL length, IP address usage, URL shortening, special symbols |
| Domain information | Subdomains, registration length, domain age, DNS records |
| Webpage content | External links, forms, iframes, pop-up windows |
| Connection and reputation | HTTPS indicators, website traffic, indexing |

## Models and Results

The notebook uses an **80/20 train-test split** with `random_state=42`:

- Training records: **8,843**
- Test records: **2,211**

The saved notebook outputs report:

| Model | Test Accuracy | Test F1 Score |
|-------|--------------:|--------------:|
| Gradient Boosting | 97.4% | 0.977 |
| Random Forest | 97.1% | 0.974 |
| Decision Tree | 95.9% | 0.963 |
| K-Nearest Neighbors | 95.6% | 0.961 |
| Gaussian Naive Bayes | 60.5% | 0.454 |

**Gradient Boosting achieved the highest recorded test accuracy.**

These are historical notebook results and have not been independently reproduced. F1 scores use scikit-learn's default positive class, `1` (legitimate websites). The notebook also explores hyperparameters against the test set, so a separate validation strategy is needed for an unbiased final evaluation.

## Repository Contents

| File | Description |
|------|-------------|
| `Phishing URL Detection.ipynb` | Data exploration, model training, evaluation, and comparison |
| `phishing.csv` | Dataset used for training and testing |
| `feature.py` | Extracts 30 features from a URL |
| `app.py` | Flask routes and model inference logic |
| `Procfile` | Gunicorn startup command |

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/SumairSalam/phishing_attack_Detection1_project.git
cd phishing_attack_Detection1_project
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

**Windows:**

```bash
.venv\Scripts\activate
```

**macOS / Linux:**

```bash
source .venv/bin/activate
```

### 3. Install Notebook Dependencies

```bash
python -m pip install numpy pandas scikit-learn matplotlib seaborn notebook
```

### 4. Run the Notebook

```bash
jupyter notebook "Phishing URL Detection.ipynb"
```

Run the cells in order to explore the data, train the classifiers, and review their performance.

The final model export cell uses a machine-specific Windows path. Replace it with a relative path before running that cell.

## Flask Application Setup

The Flask application expects two files that are **not currently included**:

- `pickle/model.pkl` — trained model
- `templates/index.html` — web interface

The web application requires these files and corrections to feature extraction before it can run successfully.

### Install Additional Dependencies

```bash
python -m pip install flask requests beautifulsoup4 python-whois googlesearch-python python-dateutil
```

Dependency versions are not pinned; compatibility should be verified.

### Export the Model

After training `gbc` in the notebook, save it using:

```python
from pathlib import Path
import pickle

Path("pickle").mkdir(exist_ok=True)

with open("pickle/model.pkl", "wb") as file:
    pickle.dump(gbc, file)
```

The existing export cell saves `forest`, the Random Forest model. Use `gbc` as shown above if you intend to serve Gradient Boosting.

### Add the HTML Template

Create `templates/index.html` with a form that:

- Sends a `POST` request to `/`
- Includes an input named `url`
- Displays the submitted `url` and returned `xx` value

The application passes the predicted legitimate-class probability as `xx`, with `-1` used for the initial page state.

### Start the Application

Once the missing files and code corrections are in place:

```bash
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000).

## Current Limitations

- The trained model and HTML template are missing.
- In `feature.py`, `BeautifulSoup(response.text, ...)` references an undefined variable; it should use `self.response.text`.
- Feature extraction depends on external websites and services, which can fail or change.
- Several exceptions silently fall back to default feature values.
- The notebook's comparison table mixes test accuracy and F1 with training recall and precision.
- The Random Forest test precision calculation references Decision Tree predictions and needs correction.
- The Flask app runs with debug mode enabled and needs additional configuration before deployment.

## Future Improvements

- Fix and test all feature extraction methods.
- Add the complete web interface and a reproducible model export workflow.
- Pin dependency versions.
- Use cross-validation and a separate final test set.
- Report phishing-class precision, recall, F1, and confusion matrices.
- Add URL validation, request timeouts, and controls against requests to private network addresses.
- Add automated tests and structured error handling.

## Responsible Use

This project is intended for education and experimentation. Predictions do not guarantee that a website is safe. Feature extraction makes network requests to submitted URLs, so untrusted URLs should be evaluated only in a controlled environment.

## Author

[Sumair Salam](https://github.com/SumairSalam)
