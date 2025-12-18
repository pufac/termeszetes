Ez a gyakorlat (4.1.) a **Morfológiai Elemzésről** (alaktan) szól. A vizsgán ez a lépcsőfok a sima szószámolás és a mélyebb jelentéselemzés között. Itt már nem csak karakterhalmazként kezeljük a szavakat, hanem megpróbáljuk megtalálni a tövüket és a szófajukat.

Itt a részletes összefoglaló az elméletről és a gyakorlati megvalósításról.

---

### 1. Elméleti háttér (A legfontosabb fogalmak)

A vizsgán tudnod kell a különbséget a **Stemming** és a **Lemmatization** között. Ez a notebook fő tanulsága.

#### A) Szótővesítés (Stemming)
*   **Működés:** Ez a "buta" módszer. Algoritmikusan levágja a szó végét (a ragokat), remélve, hogy megmarad a tő. Nem használ szótárat, csak szabályokat (pl. "ha -ok a vége, vágd le").
*   **Eredmény:** Gyakran nem valódi szót kapunk, csak egy csonkot.
*   **Példa:** "alszok" -> "alsz" (ez nem létező szó, a szótári alak az "alszik" lenne).
*   **Eszköz:** `SnowballStemmer`.

#### B) Lemmatizálás (Lemmatization)
*   **Működés:** Ez az "okos" módszer. Szótárat és nyelvtani elemzést használ, hogy visszaadja a szó szótári alapalakját (lemma).
*   **Eredmény:** Valós, értelmes szó.
*   **Példa:** "alszok" -> "alszik" (vagy "olvassátok" -> "olvas").
*   **Eszköz:** `WordNetLemmatizer` (angolhoz), `simplemma` (magyarhoz).

#### C) Szófaji Címkézés (POS Tagging - Part-of-Speech)
*   **Működés:** Minden szóhoz hozzárendeli a nyelvtani szerepét (főnév, ige, melléknév stb.).
*   **Címkék:** Általában rövidítések (pl. NN = Noun/Főnév, VB = Verb/Ige, JJ = Adjective/Melléknév).
*   **Eszköz:** `nltk.pos_tag`.

---

### 2. Gyakorlati megvalósítás lépései

A vizsgán az alábbi sorrendben kell haladnod az eszközökkel:

#### 1. lépés: Adatbetöltés és Tisztítás
Ez a szokásos rutin (Requests -> BeautifulSoup -> Tokenize -> Stopwords).
*   *Fontos:* A stopszavak listáját bővítsd ki az írásjelekkel (`string.punctuation`), ahogy a kódban látható (`union`), mert a morfológiai elemzésnél a vesszők csak zavarnak.

#### 2. lépés: Stemming (A gyors, de pontatlan módszer)
Magyar nyelvre a `SnowballStemmer`-t használjuk az NLTK-ból.

```python
from nltk.stem import SnowballStemmer

# Inicializálás magyar nyelvre
stemmer = SnowballStemmer('hungarian')

# Használat (egyesével a szavakon)
word = "ablakok"
stemmed_word = stemmer.stem(word) 
# Eredmény: "ablak"
```
*Megjegyzés:* A notebook bemutatja, hogy ez nem tökéletes ("vagyok" -> "vagy", ami mást jelent).

#### 3. lépés: Lemmatizálás (A pontos módszer)
Itt két utat mutat a notebook, figyelj, melyiket kérik!

*   **A) WordNetLemmatizer (CSAK ANGOLRA):**
    Ha angol szöveged van, ezt használd. Magyarra NEM működik jól (a notebookban látszik is, hogy a "vagyok" marad "vagyok").
    ```python
    from nltk.stem import WordNetLemmatizer
    nltk.download('wordnet')
    lemmatizer = WordNetLemmatizer()
    print(lemmatizer.lemmatize("cars")) # -> car
    ```

*   **B) Simplemma (MAGYARRA):**
    Ez egy külső könyvtár, vizsgán lehet, hogy telepíteni kell (`!pip install simplemma`). Ez a legjobb megoldás magyar szövegre.
    ```python
    !pip install simplemma
    from simplemma import lemmatize
    
    # Fontos a nyelvi kód megadása!
    print(lemmatize("olvassátok", lang='hu')) # -> olvas
    ```

#### 4. lépés: Vizualizáció (WordCloud összehasonlítás)
A notebook végén lévő szófelhők azt demonstrálják, **miért van értelme lemmatizálni**.
*   *Nyers szöveg:* Külön szóként szerepel az "ablak", "ablakok", "ablakot". Ez torzítja a statisztikát.
*   *Lemmatizált szöveg:* Mindegyikből "ablak" lesz. Így a szófelhőben az "ablak" szó sokkal nagyobban (reális gyakorisággal) jelenik meg.

#### 5. lépés: POS Tagging (Szófaji elemzés)
Ez az NLTK-ban alapvetően angolul működik jól.

```python
import nltk
nltk.download('averaged_perceptron_tagger') # Modell letöltése

text = "This new phone is awesome"
words = nltk.word_tokenize(text)

# Címkézés
pos_tags = nltk.pos_tag(words)
print(pos_tags) 
# [('This', 'DT'), ('new', 'JJ'), ('phone', 'NN')...]
```
*Tipp:* Ha a vizsgán megkérdezik, mit jelent a 'DT' vagy 'JJ', használd a súgót:
`nltk.download('tagsets'); nltk.help.upenn_tagset('JJ')`

---

### 3. Összefoglaló a vizsgához (Recept)

Ha azt kérik, hogy **"Elemezze a szöveg szavait nyelvészetileg"** vagy **"Készítsen pontosabb szógyakorisági ábrát"**:

1.  **Tokenizálj** és szűrd ki a **stopszavakat**.
2.  Döntsd el a nyelvet:
    *   **Ha MAGYAR:** Használd a `simplemma` könyvtárat (`lang='hu'`) a lemmatizáláshoz. Ha nem lehet telepíteni, akkor a `SnowballStemmer('hungarian')`-t.
    *   **Ha ANGOL:** Használd a `WordNetLemmatizer`-t.
3.  A lemmatizált szavakból készíts **új listát** (`lemmas = [lemmatize(w) for w in words]`).
4.  Ezen az új listán futtasd le a **WordCloud**-ot vagy a **FreqDist**-et. Így sokkal pontosabb képet kapsz a szöveg tartalmáról.