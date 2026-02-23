# MAL1 – Machine Learning

**4. semester · VIA University College**

Dette repository indeholder afleveringer og noter fra faget **MAL1 (Machine Learning 1)**. Faget dækker grundlæggende machine learning-teknikker med fokus på lineær algebra, regression og klassifikation i Python.

---

## Indhold

```
MAL1/
├── Aflevering 1/          # Lineær regression & kNN – EV Car Prices
│   ├── 1. Car prices.ipynb        # Komplet notebook med løsning
│   ├── EKSAMENSFORKLARING.md      # Mundtlig forsvarsnotes (dansk)
│   └── Linear_regression.pdf      # Kursusmateriale
├── Lektions noter/        # Noter fra undervisningen
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

### Teknologier

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)
![NumPy](https://img.shields.io/badge/NumPy-1.x-orange?logo=numpy)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-f89939?logo=scikit-learn)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter)

---
