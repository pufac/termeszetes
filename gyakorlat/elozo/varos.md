Ez a feladat egy **helyszín-alapú aggregált hangulatelemzés** (Location-based Aggregated Sentiment Analysis). Mivel a linket nem látom, de a feladat "Hotel Reviews" jellegű (a tippekben említik a "szálloda neve" és "cím" dolgokat), nagy valószínűséggel a népszerű **"515k Hotel Reviews Data in Europe"** vagy egy ehhez hasonló Kaggle adatbázisról van szó.

A feladat lényege: Van egy nagy táblázatod véleményekkel és címekkel. A felhasználó beírja, hogy "London", neked pedig ki kell számolnod, hogy Londonban átlagosan mennyire elégedettek az emberek (0 és 1 között).

Itt van a **stratégia**, a **javított elvi működés**, és a **kódvázlat**.

---

### 1. A megoldás logikája (Ezt írd a szövegmezőbe)

A képen látható saját jegyzeteid ("Kézileg beállítjuk...", "spacy segítségével megtaláljuk a földrajzi helyet") kicsit pontatlanok vagy túl bonyolultak. A helyszín kereséshez nem kell szóbeágyazás (embedding), sima szövegegyeztetés vagy fuzzy keresés jobb. A hangulatelemzéshez pedig nem érdemes kézi listát írni, ha tanultatok automatikus eszközöket (VADER, TextBlob).

**Javasolt szöveg a "Megoldás elvi működése" mezőbe:**

> "A feladatot Python nyelven, a Pandas és NLTK/TextBlob könyvtárak segítségével oldottam meg. A megoldás fő lépései:
> 1. **Adatok előkészítése:** A CSV fájlt Pandas DataFrame-be töltöttem. Mivel az adatbázis nagy, a fejlesztés során a `head()` függvénnyel csak egy kisebb részhalmazon dolgoztam.
> 2. **Helyszín szűrés (Filtering):** A felhasználó által megadott földrajzi helyet (pl. városnév) a szállodák címeit tartalmazó oszlopban kerestem. A kisebb írásmódbeli eltérések kezelésére a `FuzzyWuzzy` könyvtárat (vagy string tartalmazás vizsgálatot) alkalmaztam, így a keresés robusztusabb. Nem a szálloda nevét, hanem a címét vettem alapul.
> 3. **Hangulatelemzés (Sentiment Analysis):** A szűrt sorok szöveges értékeléseit egy előre tanított hangulatelemző modellel (VADER vagy TextBlob) vizsgáltam meg. Ez a modell minden véleményhez egy -1 (negatív) és +1 (pozitív) közötti polaritási értéket rendelt.
> 4. **Normalizálás és Aggregálás:** A kapott -1..+1 értékeket lineáris transzformációval a kért 0..1 skálára vetítettem `(score + 1) / 2`. Végül az adott helyszínhez tartozó összes vélemény pontszámát átlagoltam, így megkaptam a helyszín végső megítélését."

---

### 2. Gyakorlati megvalósítás (Kódvázlat)

Íme a lépések, amiket a Colab-ban meg kell írnod.

#### A) Könyvtárak és Adatbetöltés
```python
import pandas as pd
import numpy as np
from nltk.sentiment.vader import SentimentIntensityAnalyzer
import nltk
from fuzzywuzzy import fuzz # Ha a fuzzy keresés kell

# NLTK kiegészítők letöltése (VADER-hez kell)
nltk.download('vader_lexicon')

# Adatok betöltése
# Tipp: A 'Hotel_Address' és 'Review_Text' oszlopnevek csak feltételezések, 
# nézd meg a CSV fejlécét (print(df.columns))!
df = pd.read_csv("data.csv") 

# FEJLESZTÉSHEZ: Csak az első 1000 sorral dolgozz, hogy gyors legyen!
df_small = df.head(1000).copy()
```

#### B) Helyszín keresése (Földrajzi szűrés)
A feladat kéri a "kisebb eltérések" kezelését. Erre a legjobb a `fuzzywuzzy` vagy a Levenshtein távolság, de egy sima `str.contains` is elfogadható lehet, ha kisbetűsíted.

```python
def filter_by_location(dataframe, location_name):
    """
    Visszaadja azokat a sorokat, ahol a címben szerepel a location_name.
    """
    # 1. módszer: Egyszerű tartalmazás (Case insensitive)
    # return dataframe[dataframe['Hotel_Address'].str.lower().str.contains(location_name.lower())]

    # 2. módszer (Profi, a tippek miatt): Fuzzy keresés
    # Ez megengedi, hogy pl. "Lodon" írásra is megtalálja "London"-t.
    def is_match(address):
        # Ha a hasonlóság > 80%, akkor találatnak vesszük
        return fuzz.partial_ratio(location_name.lower(), address.lower()) > 80
    
    # Apply használata a szűréshez
    mask = dataframe['Hotel_Address'].apply(is_match)
    return dataframe[mask]

# Teszteljük
target_city = "London"
filtered_df = filter_by_location(df_small, target_city)
print(f"{len(filtered_df)} értékelés található itt: {target_city}")
```

#### C) Hangulatelemzés (Sentiment)
Ne írj kézi szótárat ("jó" = 1, "rossz" = 0), mert az pontatlan. Használd a **VADER**-t (NLTK), amit pont erre találtak ki.

```python
sia = SentimentIntensityAnalyzer()

def get_sentiment_score(text):
    # A VADER egy szótárat ad vissza: {'neg': 0.0, 'neu': 0.5, 'pos': 0.5, 'compound': 0.8}
    # Nekünk a 'compound' kell, ami -1 és 1 között van.
    score = sia.polarity_scores(str(text))['compound']
    
    # Normalizálás 0 és 1 közé:
    # -1 -> 0
    #  0 -> 0.5
    # +1 -> 1
    normalized_score = (score + 1) / 2
    return normalized_score

# Alkalmazzuk a szűrt adatokra
if not filtered_df.empty:
    # Létrehozunk egy új oszlopot a pontszámoknak
    filtered_df['calculated_score'] = filtered_df['Review_Text'].apply(get_sentiment_score)
    
    # Átlagolás
    final_rating = filtered_df['calculated_score'].mean()
    
    print(f"A(z) {target_city} helyszín megítélése: {final_rating:.4f} (0-1 skálán)")
else:
    print("Nincs találat erre a helyre.")
```

---

### 3. Mire figyelj a tippek alapján?

1.  **"Ne a szálloda neve alapján":** A kódban a `Hotel_Address` (vagy hasonló cím) oszlopot használd a szűrésnél, NE a `Hotel_Name`-et.
2.  **"Kisebb eltérések kezelése":** Ezért írtam a `fuzz.partial_ratio`-t. Ha ez túl lassú vagy nem tudsz könyvtárat telepíteni, használd a `.str.contains(..., case=False)` megoldást, de írd le a dokumentációba, hogy miért.
3.  **"Adatkészlet elég nagy... lassú lehet":**
    *   A fejlesztés alatt (amíg írod a kódot) használd a `df.head(500)`-at.
    *   A beadandó kódba írd bele, hogy a teljes adaton futtatáshoz vedd ki a limitet, vagy használj `chunksize`-ot a `read_csv`-nél (de az bonyolultabb). A vizsga szempontjából a `head()` használata a teszteléshez, majd a végén a teljes `df` használata a futtatáshoz a legjobb stratégia.

### Összefoglalva a teendőid:

1.  **Ne** használd a kézi szótáras módszert, amit a szövegdobozba írtál (túl egyszerű).
2.  **Ne** használj szóbeágyazást (embeddings/spacy) a helyszín keresésre (túl bonyolult és lassú ehhez). Használd a Spacy-t vagy NLTK-t a *hangulatelemzésre*, vagy a helyszín nevek *kinyerésére* (NER), de a legegyszerűbb a sima szöveges keresés a címben.
3.  Használd a **VADER**-t (NLTK) a pontozáshoz, mert az kezeli a legjobban a rövid review-kat, és alapból benne van a tananyagban.