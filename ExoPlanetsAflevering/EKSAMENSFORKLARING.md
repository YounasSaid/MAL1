# Mundtlig eksamensforklaring – Aflevering 3: Detecting Exoplanets

> Brug dette dokument til at forberede dig til mundtligt forsvar af afleveringen.
> Svarene er skrevet i jeg-form, som om du forklarer til en eksaminator.

---

## Overordnet formål

"I denne aflevering arbejder vi med et datasæt fra NASA's Kepler-mission. Det indeholder 9.564 observationer af mulige exoplaneter med 49 features. Hver observation har en disposition: enten **FALSE POSITIVE**, **CANDIDATE** eller **CONFIRMED**. Målet er at preprocessere og feature-engineere data, og derefter træne klassifikationsmodeller (Logistic Regression og SVM) der kan forudsige om en observation er en rigtig exoplanet eller ej."

---

## Del 1 – Data Exploration

**Hvad gjorde vi først?**

"Vi indlæste datasættet og brugte `df.info()` for at se kolonnernes datatyper og antal non-null værdier. Vi visualiserede fordelingen af dispositioner med en `countplot` for at se balancen mellem klasserne."

**Hvordan håndterede vi missing values?**

"Vi byggede et DataFrame der for hver kolonne viser:
- Antal missing values
- Procent missing

Sorteret faldende. To kolonner – `koi_teq_err1` og `koi_teq_err2` – var 100% missing og blev droppet helt. De øvrige missing rows blev fjernet med `dropna()`."

**Hvad er IQR-metoden til outlier detection?**

"IQR (Interquartile Range) er Q3 - Q1. En outlier defineres som en værdi udenfor `[Q1 - 1.5·IQR ; Q3 + 1.5·IQR]`. Vi loopede over alle numeriske kolonner og talte hvor mange outliers hver havde. Vi valgte at **beholde** outliers fordi astronomiske data naturligt har ekstreme værdier (fx meget lange orbital perioder), men vi log-transformerede skæve features for at dæmpe deres effekt."

---

## Del 2 – Feature Engineering

**Hvilke kolonner droppede vi og hvorfor?**

"- **100% missing**: `koi_teq_err1`, `koi_teq_err2`
- **Irrelevante ID/navne-kolonner**: KepID, KOIName, KeplerName, TCEDeliver – disse er identifikatorer der ikke har prædiktiv værdi
- **Usikkerhedskolonner**: alle kolonner der indeholder 'err' eller 'Unc' – disse er måleusikkerheder, ikke selve målingerne, og giver redundant information"

**Hvad er log-transformation og hvorfor bruger vi den?**

"Vi brugte `np.log1p(x) = log(1+x)` på features med stærk højreskævhed: OrbitalPeriod, TransitDepth, InsolationFlux, PlanetaryRadius og TransitSignal-to-Noise. Log-transformation komprimerer store værdier og spreder små værdier, så fordelingen bliver mere symmetrisk. Det hjælper lineære modeller (som Logistic Regression) der antager nogenlunde normalfordelte features. `log1p` bruges i stedet for `log` for at håndtere nul-værdier sikkert."

**Hvordan encodede vi target-variablen?**

"Disposition-kolonnen er kategorisk med tre værdier:
- FALSE POSITIVE → 0
- CANDIDATE → 1
- CONFIRMED → 2

Vi lavede derefter en binær target: `y = (disposition >= 1)` – altså 0 hvis falsk positiv, 1 hvis kandidat eller bekræftet exoplanet. Det forenkler problemet til binær klassifikation: 'er det en rigtig exoplanet eller ej?'"

**Hvorfor skalerer vi features?**

"Vi bruger `StandardScaler` som standardiserer hver feature til mean=0 og std=1. Det er kritisk for:
- **Logistic Regression** med regularisering – ellers rammer L2-straffen skævt på features med store enheder
- **SVM** – afstandsbaseret, så features med store værdier ville dominere helt"

---

## Del 3 – Train/Test Split

**Hvordan splittede vi data?**

"`train_test_split(X, y, test_size=0.2, stratify=y, random_state=42)`
- 80% træning, 20% test
- `stratify=y` sikrer at klassefordelingen er den samme i træning og test
- `random_state=42` for reproducerbarhed"

**Hvorfor stratify?**

"Hvis vi har skæv klassefordeling (fx 60/40), vil et tilfældigt split kunne give skæve fordelinger i test. Stratify garanterer at både træning og test har 60/40 – så evaluering bliver fair."

---

## Del 4 – Modeller med GridSearchCV

**Hvad er GridSearchCV?**

"Grid Search prøver alle kombinationer af hyperparametre, og Cross-Validation evaluerer hver kombination ved at splitte træningsdataen i fx 5 folds. Vi træner på 4 og validerer på 1, og roterer. Det giver et robust estimat af hvilken hyperparameter der virker bedst – uden at røre testdata."

### Logistic Regression

**Hvordan virker Logistic Regression?**

"Det er en lineær model til klassifikation. Den beregner en lineær kombination `z = β₀ + β₁x₁ + ... + βₙxₙ` og presser den gennem en sigmoid-funktion `σ(z) = 1/(1+e⁻ᶻ)`, der giver en sandsynlighed mellem 0 og 1. Hvis sandsynligheden > 0.5, klassificerer vi som klasse 1."

**Hvilke hyperparametre tunede vi?**

"`C` styrer regularisering – det er inverse af regulariseringsstyrken. Lille C = stærk regularisering (simplere model), stor C = svag regularisering. Vi prøvede C ∈ {0.01, 0.1, 1, 10}."

### SVM (Support Vector Machine)

**Hvordan virker SVM?**

"SVM finder den hyperplan der separerer klasserne med størst muligt **margin** (afstand til nærmeste punkter, kaldet support vectors). Med en kernel-funktion kan SVM håndtere ikke-lineære grænser ved at projicere data ind i et højere-dimensionelt rum."

**Hvad er forskellen på linear og rbf kernel?**

"- **Linear kernel**: Finder en lige linje/hyperplan. Hurtig, fungerer godt hvis data er lineært separabelt.
- **RBF (Radial Basis Function)**: `K(x, x') = exp(-γ||x-x'||²)` – kan modellere komplekse, kurvede grænser. Mere fleksibel men langsommere."

**Hvilke hyperparametre tunede vi?**

"`C ∈ {0.1, 1, 10}` og `kernel ∈ {rbf, linear}`."

---

## Del 5 – Evaluering

**Hvilke metrics brugte vi?**

"- **Accuracy**: andel korrekte forudsigelser
- **Precision**: af dem vi siger er exoplaneter, hvor mange er det rigtig?
- **Recall**: af de rigtige exoplaneter, hvor mange fanger vi?
- **F1-score**: harmonisk gennemsnit af precision og recall
- **Confusion Matrix**: viser True Positives, False Positives, True Negatives, False Negatives"

**Hvorfor er precision og recall vigtige – ikke kun accuracy?**

"Hvis klasserne var skæve (fx 90% falsk positiv), kunne en naiv model der altid gætter 'falsk positiv' opnå 90% accuracy uden at lære noget. Precision og recall viser hvor godt modellen faktisk fanger den positive klasse."

**Hvad viser confusion matrix?**

"En 2x2 tabel:
```
              Predicted 0   Predicted 1
Actual 0        TN            FP
Actual 1        FN            TP
```
- TN: korrekt afvist falsk positiv
- FP: vi sagde exoplanet, men det var falsk
- FN: vi missede en rigtig exoplanet
- TP: korrekt fanget exoplanet"

---

## Generelle spørgsmål

**Hvad er forskellen på preprocessing og feature engineering?**

"- **Preprocessing**: rense data – håndtere missing values, outliers, encoding, skalering
- **Feature engineering**: skabe eller transformere features så modellen bedre kan lære – fx log-transformation, kombinere kolonner, droppe redundante features"

**Hvorfor er Logistic Regression og SVM gode valg her?**

"Begge er klassiske binære klassifikatorer der virker godt på medium-store, skalerede datasæt. Logistic Regression er hurtig og fortolkelig. SVM med RBF-kernel kan fange ikke-lineære mønstre, hvilket er nyttigt når klassegrænserne ikke er lige."

**Hvad ville næste skridt være?**

"- Prøve ensemble-modeller (Random Forest, Gradient Boosting, XGBoost)
- Lave 3-klasse klassifikation i stedet for binær (skelne CANDIDATE fra CONFIRMED)
- Feature importance analyse for at forstå hvilke astronomiske målinger der betyder mest"
