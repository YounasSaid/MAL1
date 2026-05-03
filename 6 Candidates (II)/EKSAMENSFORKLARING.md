# Mundtlig eksamensforklaring – Aflevering 5: Candidate Test 2022 (Part 2)

> Brug dette dokument til at forberede dig til mundtligt forsvar af afleveringen.
> Svarene er skrevet i jeg-form, som om du forklarer til en eksaminator.

---

## Overordnet formål

"Denne aflevering er en udvidelse af Aflevering 2. Vi arbejder igen med kandidattests fra DR og TV2 fra folketingsvalget 2022, men her er fokus **unsupervised learning** (clustering) i stedet for klassifikation. Vi har 867 kandidater fra 14 partier, der har besvaret i alt 49 spørgsmål på en skala fra -2 til +2. Målet er at finde naturlige holdningsklynger og analysere det politiske landskab."

---

## Del 1 – Hvilke spørgsmål er vigtigst? (PCA)

**Hvad er PCA?**

"Principal Component Analysis er en dimensionalitetsreducerende teknik. Den finder de retninger (principal components) i data hvor variansen er størst. PC1 er retningen med størst varians, PC2 den næstvigtigste — og de to er ortogonale (uafhængige). Vi projicerer 49-dimensionel data ned i 2D for at kunne plotte og forstå strukturen."

**Hvorfor standardiserer vi før PCA?**

"PCA bygger på varians. Hvis features har forskellige skalaer, vil de med stor skala dominere. Selvom alle vores spørgsmål er på -2 til +2, standardiserer vi alligevel med `StandardScaler` (mean=0, std=1) for at give hvert spørgsmål samme udgangsvægt."

**Hvad er loadings?**

"Loadings er PCA-komponenternes vægte for hvert oprindeligt spørgsmål. Et spørgsmål med høj absolut loading på PC1 er en vigtig driver for den akse. Vi fandt at PC1 forklarer 41,7% af variansen og PC2 yderligere 10,3% — sammen 52%."

**Hvad fortæller PC1 og PC2?**

"PC1 fanger den klassiske venstre-højre-akse: spørgsmål om økonomi, integration og statslig regulering. PC2 fanger en sekundær akse — typisk værdipolitik vs. økonomisk politik. De top 10 spørgsmål med højeste loadings på PC1 er dem der adskiller venstrefløjen fra højrefløjen mest."

---

## Del 2 – Partiernes gennemsnitlige placering

**Hvad gjorde vi?**

"Vi grupperede kandidater per parti med `groupby('parti')` og tog gennemsnittet af deres svar på hvert spørgsmål. Det gav en matrix på 15 partier × 49 spørgsmål."

**Hvordan visualiserede vi det?**

"To måder:
1. Et **heatmap** af hele matrixen med rødt = enig (+2) og blåt = uenig (-2). Det viser klart hvilke partier der er enige/uenige på hvilke spørgsmål.
2. **Søjlediagrammer** for de top 4 spørgsmål med højest PC1-loading, hvor hver bjælke er et parti farvet i deres officielle farve."

---

## Del 3 – Clustering analyse

**Hvad er clustering?**

"Unsupervised learning hvor vi forsøger at finde naturlige grupper i data uden at have labels. Modsat klassifikation, hvor vi ved hvilket parti hver kandidat tilhører, lader vi her algoritmen finde grupper baseret kun på besvarelserne."

### K-Means

**Hvordan virker K-Means?**

"K-Means deler data i k klynger:
1. Initialisér k tilfældige klyngecentre
2. Tildel hvert datapunkt til det nærmeste center
3. Flyt hvert center til middelværdien af de tildelte punkter
4. Gentag til konvergens

Den minimerer **inertia** = summen af kvadrerede afstande fra hvert punkt til dets klyngecenter."

**Hvordan vælger vi k?**

"To metoder:
- **Elbow-metoden**: Plot inertia mod k. Find 'albuen' hvor faldet flader ud.
- **Silhouette score**: Måler hvor godt et punkt passer i sin klynge sammenlignet med naboklyngen. Værdier fra -1 til +1; højere er bedre.

Vores silhouette toppede ved k=2 (0,293), hvilket betyder at den **stærkeste** opdeling er i to grupper — ikke 14."

### Hierarchical clustering

**Hvordan virker det?**

"Agglomerativ hierarkisk clustering bygger et træ nedefra:
1. Start med hver datapunkt som sin egen klynge
2. Find de to nærmeste klynger og flet dem
3. Gentag indtil alt er én klynge

**Ward linkage** minimerer variansen i de flettede klynger og giver kompakte, ensartede grupper. Resultatet kan visualiseres som et **dendrogram** — et træ hvor højden af en flet viser afstanden mellem klyngerne."

**Hvad så vi i dendrogrammet?**

"Klare blokke svarende til ideologi: venstrefløjen (Æ, F, Z, B, A) flettes tidligt sammen, og højrefløjen (V, M, C, I, D, O) ligeledes. De to hovedgrene svarer til den blå/røde blok i dansk politik."

### DBSCAN

**Hvordan virker DBSCAN?**

"Density-Based Spatial Clustering of Applications with Noise. Den definerer klynger som **tætte områder** af punkter:
- **eps**: maksimal afstand mellem to punkter for at de er naboer
- **min_samples**: minimum antal naboer for at være et 'core point'
- Punkter i tætte områder flettes; punkter uden nok naboer markeres som **noise** (-1)

DBSCAN behøver ikke et på forhånd valgt antal klynger og kan finde ikke-runde klynger."

**Hvad så vi?**

"Med eps=3 fandt DBSCAN 5 klynger men markerede 767 ud af 867 som noise — det viser at data er ret kontinuerligt, ikke skarpt opdelt i tætte grupper. Med større eps får vi én stor klynge. Dette er klassisk for hold­ningsdata: holdninger er på et spektrum, ikke i isolerede øer."

### Diskussion: er der plads til flere/færre partier?

"Datamæssigt er der **plads til konsolidering**. Silhouette topper ved 2-4 klynger, ikke 14. Mange partier (især V, M, C på borgerlig fløj og A, F på venstrefløj) overlapper i kandidaternes besvarelser. Det betyder ikke at partier skal nedlægges — det viser bare at den **politiske diskurs** er mere todelt end partilandskabet antyder."

---

## Del 4 – Det politiske landskab af valgte kandidater

**Hvordan målte vi enighed mellem kandidater?**

"Vi byggede en **Pearson-korrelationsmatrix** mellem alle 169 valgte kandidaters besvarelser. r=+1 betyder identiske svar, r=-1 betyder modsatte svar."

**Hvem var mest enige?**

"Pernille Vermund og Peter Seier Christensen (begge Nye Borgerlige) med r=0.996 — næsten identiske svar. Generelt var de mest enige par fra samme parti, hvilket er forventet."

**Hvem var mest uenige?**

"Jens Meilvang (Liberal Alliance) og Jette Gottlieb (Enhedslisten) med r=-0.714. Det giver mening — Liberal Alliance og Enhedslisten er de to mest modsatrettede partier ideologisk (LA: minimal stat, lave skatter; Æ: stor stat, høj omfordeling)."

**Intern enighed per parti?**

"Vi beregnede gennemsnit-korrelationen mellem partifæller:
- **Nye Borgerlige**: r=0.953 — meget homogene
- **Enhedslisten**: r=0.886 — også meget enige
- **Socialistisk Folkeparti**: r=0.861
- **Moderaterne**: r=0.546 — mest spredte (forventet, da partiet er nyt og samler folk fra flere lejre)
- **Socialdemokratiet**: r=0.626 — bredt parti med interne fløje"

---

## Generelle spørgsmål

**Hvad er forskellen på supervised og unsupervised learning?**

"- **Supervised**: vi har labels (fx parti) og lærer en model at forudsige dem. Eksempler: klassifikation, regression.
- **Unsupervised**: vi har ingen labels og forsøger at finde struktur i data. Eksempler: clustering, dimensionality reduction (PCA).

I Aflevering 2 brugte vi supervised (klassificere parti). I denne aflevering bruger vi unsupervised (finde naturlige grupper)."

**Hvorfor PCA før clustering?**

"To grunde:
1. **Visualisering**: vi kan plotte 2D men ikke 49D
2. **Støjreduktion**: PCA fjerner ofte støj og fokuserer på de største variationer, hvilket kan forbedre clustering"

**Hvad er silhouette score helt præcist?**

"For et punkt i:
- a(i) = gennemsnitlig afstand til andre punkter i samme klynge
- b(i) = gennemsnitlig afstand til punkter i den nærmeste anden klynge
- silhouette(i) = (b(i) - a(i)) / max(a(i), b(i))

Værdien er tæt på +1 hvis punktet er langt fra naboklyngen og tæt på sin egen klynge — altså godt placeret. Tæt på 0 betyder grænsetilfælde, og negativt betyder forkert klynge."

**Hvilke begrænsninger har analysen?**

"- Spørgsmål er fra 2022, holdninger ændrer sig
- Manglende data: 9 valgte medlemmer mangler (heriblandt Mette F. og Lars Løkke)
- DR og TV2 har forskellige spørgsmål, så ikke alle kandidater har svaret på alt
- Pearson-korrelation antager lineær sammenhæng, hvilket muligvis ikke er optimalt for ordinale (-2 til +2) data"
