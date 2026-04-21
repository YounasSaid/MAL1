# MAL1 – Machine Learning

**4. semester · VIA University College**

Dette repository indeholder afleveringer og noter fra faget **MAL1 (Machine Learning 1)**. Faget dækker grundlæggende machine learning-teknikker med fokus på lineær algebra, regression og klassifikation i Python.

---

## Indhold

```
MAL1/
├── Aflevering 1/          # Lineær regression & kNN – EV Car Prices
│   ├── 1. Car prices.ipynb
│   ├── EKSAMENSFORKLARING.md
│   └── Linear_regression.pdf
├── Aflevering 2/
│   └── 2 Candidates (I)/  # Kandidattest 2022 – Klassifikation
│       ├── 2. Candidate Test 2022 I.ipynb
│       ├── EKSAMENSFORKLARING.md
│       └── image-1.png
├── ExoPlanetsAflevering/  # Aflevering 3 – Detecting Exoplanets
│   ├── 4. Preprocessing and Feature Engineering - Detecting Exoplanets.ipynb
│   └── EKSAMENSFORKLARING.md
├── Sentiment Analysis aflevering/  # Aflevering 4 – Sentiment Analysis
│   ├── 5. Neural networks I - Sentiment analysis.ipynb
│   └── EKSAMENSFORKLARING.md
├── Lektions noter/
└── README.md
```

---

## Aflevering 1 – EV Car Prices

**Emne:** Forudsigelse og klassifikation af brugte elbilers priser (data fra bilbasen.dk)

**Dataset:** 6.226 observationer · 14 features

### Hvad er løst

| Del | Indhold | Metode |
|-----|---------|--------|
| **Part 1** | Lineær regression fra bunden | Normal Equation · NumPy |
| **Part 2** | Korrelationsanalyse + regression med biblioteker | OLS · Ridge · Lasso · Elastic Net |
| **Part 3** | Binær klassifikation (billig/dyr) | k-Nearest Neighbors |

### Nøgleresultater

- **OLS / Normal Equation:** RMSE ≈ 52.673 DKK · R² ≈ 0,864
- **Bedste regulariseret model:** Lasso (α=0,01) · R² ≈ 0,867
- **kNN klassifikation:** Bedste accuracy ≈ 93,98% (k=3, Euclidean)
- **Vigtigste features:** Original Price (DKK), Model Year, Mileage (km), Electric Range (km)

---

## Aflevering 2 – Candidate Test 2022 (Part 1)

**Emne:** Analyse af kandidattests fra DR og TV2 til folketingsvalget 2022

**Dataset:** 867 kandidater · 14 partier · 49 spørgsmål (25 DR + 24 TV2)

### Hvad er løst

| Del | Indhold | Metode |
|-----|---------|--------|
| **Krav 1** | Alder pr. parti | groupby + visualisering |
| **Krav 2** | Mest selvsikre kandidater | Andel ekstreme svar (-2/+2) |
| **Krav 3** | Inter- og intra-party forskelle | Standardafvigelse + korrelationsmatrix |
| **Krav 4** | Klassifikation af partimedlemskab | Decision Tree · Random Forest · Gradient Boosted |

### Nøgleresultater

- **Decision Tree:** 75% accuracy
- **Random Forest:** 94% accuracy (bedste model)
- **Gradient Boosted:** 88% accuracy
- Modellen identificerer kandidater der politisk matcher et andet parti bedre

---

## Aflevering 3 – Detecting Exoplanets

**Emne:** Preprocessing og feature engineering på NASA Kepler-data for at klassificere exoplaneter

**Dataset:** 9.564 observationer · 49 features · 3 dispositioner (FALSE POSITIVE, CANDIDATE, CONFIRMED)

### Hvad er løst

| Del | Indhold | Metode |
|-----|---------|--------|
| **Explore** | Missing values, outliers | IQR · isnull · countplot |
| **Feature Engineering** | Drop irrelevante kolonner, log-transform, encoding | np.log1p · StandardScaler |
| **Split** | Stratified train/test split | train_test_split (stratify) |
| **Modeller** | Hyperparameter tuning | GridSearchCV · Logistic Regression · SVM |
| **Evaluering** | Confusion matrix, precision/recall | classification_report |

### Teknikker
- Drop af 100% missing kolonner (`koi_teq_err1`, `koi_teq_err2`)
- Log-transformation af skæve features (OrbitalPeriod, TransitDepth, m.fl.)
- Binær target: FALSE POSITIVE vs. (CANDIDATE ∪ CONFIRMED)
- GridSearchCV over `C` for Logistic Regression og over `C`/`kernel` for SVM

---

## Aflevering 4 – Sentiment Analysis (Neural Networks)

**Emne:** Sentimentklassifikation af IMDb-filmanmeldelser med et neuralt netværk

**Dataset:** 25.000 reviews · binær label (positive / negative)

### Hvad er løst

| Del | Indhold | Metode |
|-----|---------|--------|
| **(a)** | Train/val/test split + tekst til tal | CountVectorizer (BoW, 10.000 ord) |
| **(b)** | Udforsk repræsentationen | One-hot, count-vektor, sparse matrix |
| **(c)** | Træn neuralt netværk, tune hyperparametre | MLPClassifier · early stopping · alpha-tuning |
| **(d)** | Evaluer på test-sættet | Confusion matrix · classification report |
| **(e)** | Klassificér egne sætninger | predict_proba |

### Nøgleresultater

- **Bedste model:** MLP med 64 neuroner, alpha=0.0001
- **Validation accuracy:** 88.96%
- **Test accuracy:** 87.78%
- **Precision / Recall / F1:** ≈ 0.88 for begge klasser

---

### Teknologier

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)
![NumPy](https://img.shields.io/badge/NumPy-1.x-orange?logo=numpy)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-f89939?logo=scikit-learn)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter)

---
