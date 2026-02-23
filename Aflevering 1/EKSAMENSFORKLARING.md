# Mundtlig eksamensforklaring – Aflevering 1: EV Car Prices

> Brug dette dokument til at forberede dig til den mundtlige forsvar af afleveringen.
> Svarene er skrevet i jeg-form, som om du forklarer til en eksaminator.

---

## Overordnet formål

"I denne aflevering arbejder vi med et datasæt af 6.226 brugte elbiler scraped fra bilbasen.dk. Målet er at forudsige bilernes salgspris ud fra 14 features, og til sidst klassificere dem som enten billige eller dyre. Vi bruger lineær algebra, regressionsteknikker og k-Nearest Neighbors til dette."

---

## Del 1 – Lineær Algebra og den Normale Ligning

**Hvad er den normale ligning, og hvorfor bruger vi den?**

"Den normale ligning er en analytisk løsning til lineær regression:

β = (XᵀX)⁻¹ · Xᵀy

Den finder direkte den koefficient-vektor β, der minimerer summen af de kvadrerede fejl – altså SSE (Sum of Squared Errors). Fordelen er at vi ikke behøver en iterativ optimering som gradient descent; vi får svaret i ét trin.

I koden tilføjer vi en kolonne af 1'ere til X-matricen, så interceptet β₀ automatisk indgår i koefficient-vektoren. Herefter bruger vi `np.linalg.lstsq`, som er numerisk mere stabil end at invertere XᵀX direkte – fordi XᵀX kan være singulær eller tæt på singulær ved multikollinearitet."

**Hvad fik vi ud af det?**

"Modellen opnår RMSE ≈ 52.673 DKK og R² ≈ 0,864. Det betyder at modellen i gennemsnit rammer inden for ca. 53.000 kr. af den faktiske pris, og at den forklarer ca. 86% af variationen i bilpriser. De 14% den ikke forklarer skyldes faktorer vi ikke har i datasættet – fx bilens stand, farve, geografisk placering."

**Bemærk til eksaminatoren:**
"Sklearn's LinearRegression giver præcis samme resultat som den normale ligning – de er matematisk identiske. Vi verificerede dette ved at sammenligne RMSE og R², som er identiske."

---

## Del 2 – Korrelation og OLS

**Hvad viser korrelationsmatricen?**

"Korrelationsmatricen viser den parvise Pearson-korrelation mellem alle variable. De vigtigste observationer:

- **Original Price (DKK)** har den stærkeste positive korrelation med salgsprisen – biler med høj nypris holder typisk en høj markedsværdi
- **Model Year** korrelerer positivt – nyere biler er dyrere
- **Mileage (km)** korrelerer negativt – mere brugt = lavere pris
- Jeg ser også **multikollinearitet** mellem Battery Capacity og Electric Range, da de måler relaterede ting. Det kan destabilisere OLS-koefficienterne."

**Hvad betyder RMSE og R² i praksis?**

"RMSE er i samme enhed som responsvariablen – DKK. RMSE ≈ 52.673 DKK siger: typisk er modellens fejl omtrent 53.000 kr. Det er ret acceptabelt på et brugtbilsmarked, men det afhænger af bilens priskategori – for en bil til 150.000 kr. er det en stor fejl, men for en bil til 700.000 kr. er det meget rimeligt.

R² = 0,864 fortæller at 86,4% af prisdifferencerne fanges af modellen. Det er et stærkt resultat for lineær regression."

---

## Del 2 – Ridge, Lasso og Elastic Net

**Hvorfor skal vi skalere data til regulariserede modeller?**

"Regularisering pålægger en straf på store koefficienter. Hvis features ikke er skalerede, er koefficienter til store features (fx Original Price i hundredetusinder) naturligt store, mens koefficienter til fx Number of Doors er små – ikke fordi de er uvigtige, men fordi enhederne er forskellige. Straffen ville dermed ramme skævt. Med StandardScaler standardiserer vi alle features til mean=0 og std=1, så alle koefficienter er på samme skala og straffen rammer fair."

**Hvad er forskellen på Ridge og Lasso?**

"Ridge bruger L2-straf: `||β||²` – summen af kvadrerede koefficienter. Den krymper alle koefficienter mod nul, men sætter ingen præcist til nul. Det er godt ved multikollinearitet.

Lasso bruger L1-straf: `||β||₁` – summen af absolutte koefficienter. Den *kan* trykke koefficienter præcist til nul, fordi dens geometri er et diamantformet areal med spidse hjørner der rammer koordinatakserne. Det svarer til automatisk feature-selektion.

I vores resultater: Lasso med alpha=0,01 satte fx ingen koefficienter til nul ved lave alpha-værdier, men ved høje alpha-værdier begynder irrelevante features at falde bort."

**Hvad er Elastic Net?**

"Elastic Net er en kombination af Ridge og Lasso: `α·l1_ratio·||β||₁ + α·(1-l1_ratio)·||β||²`. l1_ratio=1 er ren Lasso, l1_ratio=0 er ren Ridge. Det er nyttigt når vi har grupper af korrelerede features – Lasso vil tilfældigt vælge én fra gruppen, mens Elastic Net kan beholde alle."

**Hvilken model var bedst?**

"Lasso med alpha=0,01 opnåede lavest MSE (0,1229) og højest R² (0,867) på de skalerede data. Forbedringen over OLS er marginal – hvilket er forventeligt, da vi har 6.226 observationer og kun 14 features. Regularisering hjælper mest ved høj dimensionalitet eller begrænset data."

**Er de tre modeller enige om vigtigste features?**

"Ja, alle tre modeller er enige om top-5: Original Price (DKK), Model Year, Mileage (km), Electric Range/0-100 km/h, Annual Road Tax. Det giver god validitet – vi kan have tillid til at netop disse features er de reelt vigtigste for prissætning."

**Hvad betyder koefficienterne på skalerede data?**

"Fordi alt er standardiseret, kan vi direkte sammenligne koefficienternes størrelse. En koefficient på 0,4 for Original Price betyder: én standardafvigelse stigning i nypris giver 0,4 standardafvigelse stigning i markedspris. Det gælder alt andet lige."

---

## Del 3 – kNN Klassifikation

**Hvordan omdanner vi regressionsproblemet til klassifikation?**

"Vi beregner medianen af alle bilpriser og bruger den som grænseværdi. Biler over medianen mærkes som 'Dyre' (1), resten som 'Billige' (0). Det giver en perfekt balanceret datasæt med ca. 50/50 fordeling – vigtigt for at undgå bias mod majoritetsklassen."

**Hvordan virker kNN?**

"k-Nearest Neighbors er en ikke-parametrisk algoritme. Når vi skal klassificere en ny bil, finder algoritmen de k nærmeste naboer i feature-rummet (baseret på afstandsmål), og returnerer majoritetsklassen blandt disse naboer. Der er ingen 'træning' i klassisk forstand – modellen husker blot alle træningspunkter."

**Hvad er trade-off'et ved valg af k?**

"Det er bias-varians afvejningen:

- Lille k (fx k=1): Lav bias, høj varians. Modellen er ekstremt sensitiv – én outlier kan ændre klassifikationen. Høj trænings-accuracy men risiko for overfitting.
- Stor k (fx k=51): Høj bias, lav varians. Modellen midler over mange naboer og bliver 'glat'. Risiko for underfitting – ved meget stor k nærmer vi os blot majoritetsklassens frekvens.

Det optimale k fandt vi ved grid search: k=3 med Euclidean afstand gav bedst accuracy på 93,98%."

**Hvilke afstandsmål brugte vi, og hvorfor?**

"Vi testede tre:
- **Euclidean**: Standard geometrisk afstand. Fungerer bedst med standardiserede data.
- **Manhattan**: Sum af absolutte afstande. Mere robust over for outliers i enkeltdimensioner.
- **Chebyshev**: Maksimum-afstanden i én dimension. Nyttigt hvis én feature dominerer.

Da vi skalerede data med StandardScaler, forventes Euclidean og Manhattan at præstere ens – og det bekræfter vores resultater."

**93,98% accuracy – er det godt?**

"Ja, det er meget tilfredsstillende. Vi skal dog huske at datasættet er balanceret (50/50), så en naiv model der altid gætter 'dyr' ville kun opnå 50%. 94% er altså en markant forbedring. Classification report viser at precision og recall er høje for begge klasser, så vi underfitter ikke den ene klasse."

---

## Mulige ekstra spørgsmål

**"Hvad er en begrænsning ved lineær regression?"**
"Lineær regression antager at sammenhængen mellem features og respons er lineær, at fejlene er normalfordelte og homoskedastiske (konstant varians). I praksis kan bilpriser have ikke-lineære sammenhænge – fx kan effekten af mileage aftage ved høje kilometertal."

**"Hvorfor bruger vi 80/20 split og random_state=42?"**
"80/20 er en standardfordeling der giver modellen tilstrækkeligt data til at lære, og tilstrækkeligt til at evaluere. random_state=42 sikrer reproducerbarhed – alle der kører koden får præcis samme split."

**"Hvad sker der hvis vi ikke skalerer data til kNN?"**
"kNN bruger afstandsmål. Uden skalering vil features med store absolutte værdier (fx Original Price i hundredetusinder) dominere fuldstændig over features med små værdier (fx Number of Doors). Resultatet ville være en model der i praksis kun kigger på prisen."
