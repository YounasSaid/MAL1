# Mundtlig eksamensforklaring – Aflevering 2: Candidate Test 2022 (Part 1)

> Brug dette dokument til at forberede dig til mundtlig forsvar af afleveringen.
> Svarene er skrevet i jeg-form, som om du forklarer til en eksaminator.

---

## Overordnet formål

"I denne aflevering analyserer vi data fra kandidattestene fra DR og TV2 til folketingsvalget 2022. Vi har svar fra 867 kandidater fordelt på 14 partier. Hver kandidat har svaret på 25 DR-spørgsmål og 24 TV2-spørgsmål på en skala fra -2 (helt uenig) til +2 (helt enig). Målet er at forstå politiske mønstre og bygge modeller der kan forudsige partimedlemskab."

---

## Krav 1 – Alder pr. parti

**Hvad gjorde vi?**

"Vi brugte `groupby('parti')['alder'].mean()` til at beregne gennemsnitsalderen for hvert parti og visualiserede det som et søjlediagram."

**Hvad er `groupby()`?**

"Det svarer til en pivot-tabel i Excel. Vi siger: 'opdel alle rækker i grupper baseret på parti, og beregn gennemsnittet af alder for hver gruppe.' Det giver os ét tal pr. parti."

**Hvad viste resultaterne?**

"Vi kan se aldersforskelle mellem partierne. Nogle partier har typisk ældre kandidater (fx etablerede partier), mens nyere partier ofte har yngre kandidater."

---

## Krav 2 – Mest selvsikre kandidater

**Hvad betyder 'selvsikker' her?**

"En selvsikker kandidat er en der svarer -2 eller +2 på mange spørgsmål — altså stærke holdninger. En kandidat der svarer 0 på alt er neutral/usikker. Vi måler det som **andelen** af svar der er -2 eller +2."

**Hvordan beregnede vi det?**

"Vi brugte `.isin([-2, 2])` til at tjekke om hvert svar er ekstremt. Det giver True/False for hver celle. `.sum(axis=1)` tæller antal True'er pr. kandidat (altså pr. række). Divideret med antal spørgsmål giver det en andel mellem 0 og 1."

**Hvad betyder `axis=0` vs `axis=1`?**

"Det er en vigtig detalje i pandas:
- `axis=0` = beregn **nedad** (pr. kolonne)
- `axis=1` = beregn **vandret** (pr. række)

Vi bruger `axis=1` fordi vi vil tælle pr. kandidat, og hver kandidat er en række."

---

## Krav 3 – Forskelle mellem kandidater

### Intern uenighed (intra-party)

**Hvad er standardafvigelse, og hvorfor bruger vi den?**

"Standardafvigelse måler spredningen i data. Hvis alle i Enhedslisten svarer +2 på et spørgsmål, er standardafvigelsen 0 — perfekt enighed. Hvis halvdelen svarer +2 og halvdelen -2, er standardafvigelsen høj — stor uenighed.

Vi beregner standardafvigelsen pr. parti for hvert spørgsmål, og tager gennemsnittet. Partier med højt gennemsnit har mest intern uenighed."

**Hvad viste resultaterne?**

"Vi kan identificere hvilke partier der har en klar fælles linje (lav standardafvigelse) og hvilke der er mere splittede internt (høj standardafvigelse). Typisk har de større partier mere intern uenighed, da de favner bredere."

### Forskelle mellem partier (inter-party)

**Hvad er en korrelationsmatrix?**

"En korrelationsmatrix viser Pearson-korrelation mellem alle par af partier. Vi beregner først gennemsnitssvar pr. parti for hvert spørgsmål, og korrelerer derefter disse gennemsnit.

- Korrelation tæt på +1 = partierne svarer ens
- Korrelation tæt på 0 = ingen sammenhæng
- Korrelation tæt på -1 = partierne svarer modsat"

**Hvad viste heatmappet?**

"Heatmappet bekræfter det politiske spektrum:
- Venstreorienterede partier (Enhedslisten, SF) korrelerer positivt med hinanden
- Højreorienterede partier (Liberal Alliance, Nye Borgerlige) korrelerer positivt med hinanden
- Men venstre- og højrefløjen korrelerer negativt — de er politiske modsætninger"

---

## Krav 4 – Klassifikationsmodeller

### De tre modeller

**Hvad er en Decision Tree?**

"Et beslutningstræ er som et flowchart. Det stiller ja/nej-spørgsmål om dine data: 'Svarede kandidaten +2 på spørgsmål 530?' → Ja → 'Svarede de -1 på spørgsmål 1a?' → osv., indtil det når en konklusion om partiet. Det er simpelt og nemt at fortolke, men kan overfitte — det lærer træningsdataen udenad."

**Hvad er en Random Forest?**

"En Random Forest er en 'skov' af mange Decision Trees (vi brugte 100). Hvert træ trænes på et tilfældigt udsnit af data og features. Når vi skal forudsige, stemmer alle 100 træer, og flertallet vinder. Det reducerer overfitting fordi de individuelle træers fejl udligner hinanden."

**Hvad er Gradient Boosted Trees?**

"Gradient Boosting bygger træer sekventielt. Det første træ gætter. Det andet træ fokuserer specifikt på de fejl det første lavede. Det tredje fokuserer på fejlene fra de to første. Osv. Hvert nyt træ 'booster' de forrige. Det er ofte den stærkeste metode, men kan overfitte hvis man bruger for mange træer."

### Resultater

**Hvad var accuracy?**

"- Decision Tree: 75%
- Random Forest: 94%
- Gradient Boosted: 88%

Random Forest var bedst med 94% accuracy — det vil sige at den korrekt forudsiger partimedlemskab for 94% af kandidaterne."

**Er 94% godt?**

"Ja, meget godt. Med 14 partier ville en tilfældig gætter kun ramme ca. 7%. At vi opnår 94% viser at kandidaternes politiske holdninger er stærkt knyttet til deres parti — de fleste kandidater svarer i overensstemmelse med deres partilinje."

**Hvad med de 6% der gættes forkert?**

"Det er de interessante tilfælde — kandidater hvis svar bedre matcher et andet parti. Det kan betyde:
- De er moderat i forhold til deres parti
- De har niche-holdninger der ligner et andet parti
- De er muligvis i det 'forkerte' parti politisk set"

### Hvad er `fit()`, `predict()` og `accuracy_score()`?

"- `.fit(X_train, y_train)` = Træn modellen. Den lærer sammenhængen mellem svar og parti.
- `.predict(X_test)` = Brug modellen til at gætte partierne for nye kandidater.
- `accuracy_score(y_test, y_pred)` = Sammenlign gættene med de rigtige svar. Andel korrekte."

---

## Generelle spørgsmål

**Hvorfor splitter vi data i træning og test?**

"Hvis vi tester på de samme data vi trænede på, er det som at give svarene til en eksamen og derefter teste med de samme spørgsmål — vi ved ikke om eleven virkelig har lært noget. Train/test split sikrer at vi evaluerer på data modellen aldrig har set."

**Hvad er forskellen på regression og klassifikation?**

"Regression forudsiger et **tal** (fx bilpris). Klassifikation forudsiger en **kategori** (fx parti). I Aflevering 1 lavede vi regression (forudsige pris i DKK). I Aflevering 2 laver vi klassifikation (forudsige partimedlemskab)."

**Hvad er overfitting?**

"Overfitting er når modellen lærer træningsdataen udenad i stedet for at lære generelle mønstre. Forestil dig en elev der memorerer alle eksamensopgaver — de klarer sig godt på præcis de opgaver, men dårligt på nye. Decision Tree er mest udsat for overfitting (75% accuracy), mens Random Forest er mere robust (94%)."

**Hvad er ensemble learning?**

"Ensemble learning kombinerer flere modeller for at få et bedre resultat. Random Forest og Gradient Boosting er begge ensemble-metoder:
- Random Forest = mange træer der stemmer (bagging)
- Gradient Boosting = træer der lærer af hinandens fejl (boosting)"
