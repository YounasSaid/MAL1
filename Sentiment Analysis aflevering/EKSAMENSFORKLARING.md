# Mundtlig eksamensforklaring – Aflevering 4: Sentiment Analysis

> Brug dette dokument til at forberede dig til mundtligt forsvar af afleveringen.
> Svarene er skrevet i jeg-form, som om du forklarer til en eksaminator.

---

## Overordnet formål

"I denne aflevering arbejder vi med IMDb-datasættet – 25.000 filmanmeldelser med labels (positive/negative). Målet er at bygge en sentiment-classifier med et neuralt netværk der kan forudsige om en anmeldelse er positiv eller negativ."

---

## Del (a) – Data split og Bag-of-Words

**Hvordan splittede vi data?**

"Vi splittede i tre dele med `train_test_split`:
- 60% træning (15.000 reviews)
- 20% validation (5.000) – bruges til hyperparameter-tuning
- 20% test (5.000) – gemmes til endelig evaluering

Vi brugte `stratify=y` for at bevare klassefordelingen i alle splits."

**Hvad er Bag-of-Words (BoW)?**

"BoW er en måde at repræsentere tekst som tal. Vi bruger `CountVectorizer` fra sklearn med `max_features=10000`, som tæller hvor mange gange hvert af de 10.000 hyppigste ord forekommer i hver review. Resultatet er en matrix med 15.000 rækker (reviews) og 10.000 kolonner (ord)."

**Hvorfor fit kun på træningsdata?**

"Vi kalder `fit_transform` på træningsdata og kun `transform` på val/test. Hvis vi fittede på hele datasættet, ville modellen indirekte have set testdata – det er data leakage."

---

## Del (b) – Repræsentation

**Hvordan repræsenteres et enkelt ord?**

"Som en one-hot vektor af længde 10.000 – alle nuller undtagen ét indeks. Fx har ordet 'good' indeks 3835, så det er en vektor med 1 på position 3835 og 0 alle andre steder."

**Hvordan repræsenteres en hel review?**

"Som en tælle-vektor (count vector) af længde 10.000. Hvert element angiver hvor mange gange det tilsvarende ord forekommer. De fleste elementer er 0 (sparse), fordi ét review kun bruger en lille del af vocabulary. Fx havde review 0 kun 113 unikke ord ud af 10.000 mulige."

---

## Del (c) – Neuralt netværk

**Hvad er et neuralt netværk med ét skjult lag?**

"Det er en MLPClassifier (Multi-Layer Perceptron) med tre lag:
1. **Input-lag**: 10.000 neuroner (ét per ord i vocabulary)
2. **Skjult lag**: fx 64 neuroner med ReLU-aktivering
3. **Output-lag**: 1 neuron med sigmoid der giver sandsynlighed for positiv

Hvert neuron beregner en vægtet sum af inputs, tilføjer en bias, og sender resultatet gennem en aktiveringsfunktion."

**Hvad er ReLU?**

"`ReLU(x) = max(0, x)` – den sætter negative værdier til 0 og lader positive passere. Den er hurtig at beregne og undgår vanishing gradient-problemet."

**Hvad er Adam optimizer?**

"Adam er en optimizer der tilpasser learning rate individuelt for hver parameter. Den kombinerer momentum (husker retningen) og RMSprop (tilpasser størrelsen). Den konvergerer hurtigere end standard gradient descent."

**Hvilke hyperparametre tunede vi?**

"Vi tunede:
- **hidden_layer_sizes**: antal neuroner i det skjulte lag (64, 128)
- **alpha**: L2-regulariseringsstyrke (0.0001, 0.001)

Vi trænede hver konfiguration på træningsdata og evaluerede på validation. Bedste var **(64,) med alpha=0.0001** → val accuracy 88.96%."

**Hvad er early stopping?**

"Vi satte `early_stopping=True` som holder 10% af træningsdata til side og stopper træningen når validation-loss ikke forbedres i 10 epoker i træk. Det forhindrer overfitting – modellen stopper før den memorerer træningsdata."

**Hvad er alpha (L2-regularisering)?**

"Alpha tilføjer en straf på størrelsen af vægtene: `loss = data_loss + alpha * ||weights||²`. Stor alpha tvinger vægtene mod 0, hvilket giver en simplere model. Lille alpha giver mere frihed men risiko for overfitting."

---

## Del (d) – Test

**Hvad var resultatet på test-sættet?**

"Test accuracy: **87.78%** med precision, recall og F1-score alle omkring 0.88 for begge klasser (Negativ og Positiv). Confusion matrix viser en balanceret model der er nogenlunde lige god til at fange begge klasser."

**Hvorfor er test accuracy lidt lavere end validation?**

"Det er helt normalt – vi har tunet hyperparametre baseret på validation, så der er en lille optimistisk bias. Test-sættet er usete data og giver det mest ærlige estimat."

---

## Del (e) – Egne sætninger

**Hvordan klassificerer vi nye sætninger?**

"Vi transformer sætningerne med den **samme** CountVectorizer (kun `transform`, ikke `fit`), og sender dem gennem modellen. `predict_proba` giver sandsynligheden for positiv/negativ."

**Virkede det?**

"Ja – 'fantastic, I loved every minute' → Positiv (68.6%), 'terrible acting, boring plot' → Negativ (15.6%). Modellen fanger klare sentiments godt. Blandet sentiment ('not bad, but not great') er sværere – den siger Negativ (34.9%), hvilket er rimeligt."

---

## Generelle spørgsmål

**Hvad er forskellen på BoW og TF-IDF?**

"BoW tæller bare ordforekomster. TF-IDF (Term Frequency – Inverse Document Frequency) vægter ordene – hyppige ord som 'the' og 'is' nedvægtes, sjældne ord som 'masterpiece' opvægtes. TF-IDF ville sandsynligvis give bedre resultater."

**Hvad er begrænsningen ved BoW?**

"BoW mister al rækkefølge-information. 'Not good' og 'good not' giver samme vektor. Den forstår ikke kontekst eller sarkasme. Mere avancerede metoder som word embeddings (Word2Vec, GloVe) eller transformer-modeller (BERT) håndterer dette bedre."

**Hvad ville forbedre modellen?**

"- Bruge TF-IDF i stedet for rå counts
- Tilføje bigrams (to-ord-sekvenser) til vocabulary
- Bruge word embeddings (fx Word2Vec) som input
- Prøve dybere netværk (flere skjulte lag)
- Bruge dropout som regularisering
- Bruge en recurrent model (LSTM) eller transformer (BERT) der forstår rækkefølge"
