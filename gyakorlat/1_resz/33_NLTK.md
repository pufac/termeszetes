Ez a feladatcsoport az **NLTK (Natural Language Toolkit)** könyvtár „Hello World”-jének számít. Ha vizsgán vagy beadandóban ezzel találkozol, az a cél, hogy demonstráld: képes vagy betölteni szöveges adatokat, statisztikákat készíteni róluk, és érted a nyelvészeti alapfogalmakat (pl. n-gramok, eloszlások).

Mivel Google Colab környezetet kértek, a megoldást pontosan abban a formában írtam le, ahogy ott futtatnod kell.

Íme a lépésről lépésre útmutató és a hozzá tartozó magyarázatok:

---

### 1. NLTK Telepítése és Adatok letöltése

A Google Colabban az NLTK alapból telepítve van, de az adatok (korpuszok) nincsenek. Ezeket le kell tölteni.

**Kód:**
```python
import nltk
# A könyv példáihoz szükséges népszerű csomagok letöltése
nltk.download('book')

# Teszt: betöltjük a könyv beépített szövegeit
from nltk.book import *
```

**Magyarázat:**
*   Az `nltk.download('book')` letölti a legfontosabb szöveggyűjteményeket (Gutenberg, Brown, stb.), amikkel a könyv 1. és 2. fejezete dolgozik.
*   A `from nltk.book import *` parancs automatikusan betölt 9 szöveget (`text1`, `text2`...), amikkel azonnal lehet kísérletezni.

---

### 2. Ismerkedés a korpuszokkal (2. fejezet 1. pont)

A korpusz egy nagy, strukturált szövegállomány.

**Kód:**
```python
# Megnézzük, milyen szövegeink vannak (az előző import után)
print(text1)  # Moby Dick
print(text4)  # Inaugural Address Corpus (Elnöki beiktatási beszédek)

# Válasszuk ki a Moby Dicket (text1) a továbbiakra
selected_text = text1
print(f"\nKiválasztott szöveg hossza (szavak + írásjelek): {len(selected_text)}")
```

---

### 3. Konkordancia és Keresés (1. fejezet 1.3 és 3.3)

Ez az NLP egyik legrégebbi eszköze (KWIC - Key Word In Context). Azt mutatja meg, milyen környezetben fordul elő egy szó.

**Kód:**
```python
print("--- Konkordancia (Milyen kontextusban szerepel a szó?) ---")
selected_text.concordance("monstrous")

print("\n--- Hasonló szavak (Amik hasonló környezetben fordulnak elő) ---")
# Megkeresi, mely szavak szerepelnek ugyanabban a kontextusban, mint a "monstrous"
selected_text.similar("monstrous")

print("\n--- Közös kontextus ---")
# Milyen környezetben fordul elő mindkettő?
selected_text.common_contexts(["monstrous", "very"])
```

**Elmélet:**
*   **Concordance:** Segít megérteni a szó szemantikáját (jelentését) a környezete alapján.
*   **Similar:** Disztribúciós hipotézis – a szavak jelentését a szomszédaik határozzák meg. Ha "A kutya ugat" és "A róka ugat" is helyes, akkor a kutya és a róka hasonló (szemantikailag közeli) szavak.

---

### 4. Gyakoriságvizsgálat és Kollokációk (1. fejezet 3.1, 3.2, 3.3)

Itt jön be a statisztika: melyek a legfontosabb szavak?

**Kód:**
```python
from nltk import FreqDist

# 1. Gyakorisági eloszlás készítése
fdist = FreqDist(selected_text)

print("--- Leggyakoribb 10 szó ---")
print(fdist.most_common(10))

print(f"\nA 'whale' szó előfordulása: {fdist['whale']}")

# 2. Vizuális ábrázolás (Cumulative Plot)
# Ez megmutatja, hogy a leggyakoribb 50 szó mennyit fed le a teljes szövegből
fdist.plot(50, cumulative=True)

# 3. Hapax Legomenon (Csak egyszer előforduló szavak)
print(f"\nEgyszer előforduló szavak száma: {len(fdist.hapaxes())}")

# 4. Kollokációk (Gyakori szókapcsolatok)
print("\n--- Kollokációk (Szavak, amik szeretnek együtt járni) ---")
selected_text.collocations()
```

**Elmélet:**
*   **Zipf-törvény:** Látni fogod a grafikonon. Kevés szó nagyon gyakori (the, of, and), rengeteg szó nagyon ritka.
*   **Kollokáció:** Olyan szópárok, amelyek gyakrabban fordulnak elő együtt, mint véletlenszerűen várnánk (pl. "red wine" vs "the wine"). Az NLTK a *Pointwise Mutual Information* (PMI) segítségével keresi ezeket.

---

### 5. Kategóriák szerinti eloszlás (2. fejezet 2.2)

Ehhez a **Brown korpuszt** kell használni, mert az műfajok (categories) szerint van címkézve (hírek, vallás, hobbi, stb.). Ez a feladat legszebb része: összehasonlító nyelvészet.

**Kód:**
```python
from nltk.corpus import brown

# Kategóriák listázása
print("Kategóriák a Brown korpuszban:", brown.categories())

# Készítünk egy feltételes gyakorisági eloszlást (Conditional Frequency Distribution)
# Azt vizsgáljuk: hogyan függ a szavak használata a műfajtól?
cfd = nltk.ConditionalFreqDist(
    (genre, word)
    for genre in brown.categories()
    for word in brown.words(categories=genre)
)

# Kérdés: A módbeli segédigék (modals) hogyan oszlanak meg a Hírek és a Romantikus szövegek között?
genres = ['news', 'romance']
modals = ['can', 'could', 'may', 'might', 'must', 'will']

print("\n--- Módbeli segédigék eloszlása műfajok szerint ---")
cfd.tabulate(conditions=genres, samples=modals)
```

**Mit kell látni?**
Azt fogod látni, hogy a hírekben (tényközlés) és a romantikus regényekben (feltételezés, érzelmek) teljesen más a *could* vagy a *may* gyakorisága. Ez a **stilisztikai elemzés** alapja.

---

### 6. Generatív képességek (Bigram modellek)

Hogyan tudunk "szöveget írni" a statisztika alapján?

**Kód:**
```python
# 1. Bigramok (Szópárok) készítése
text_words = list(selected_text)[:100] # Csak az első 100 szót nézzük a példához
bi_grams = list(nltk.bigrams(text_words))
print(f"--- Első 5 bigram a Moby Dickből ---\n{bi_grams[:5]}")

# 2. Szöveggenerálás (Egyszerűsített bemutatás)
# Régi NLTK verziókban volt text1.generate(), az újakból kivették.
# Helyette a logikát kell érteni:
# A modell megnézi az utolsó szót, és választ mellé egyet, ami gyakran követi.

print("\n--- Véletlenszerű szöveggenerálás (Trigram modell alapján) ---")
# Az NLTK LM (Language Model) modulját nehézkes telepíteni Colabban,
# ezért használjuk a beépített egyszerű generátort a 'text1' objektumon, ha elérhető (verziófüggő),
# vagy írunk egy mini-generátort:

def simple_generate(text, num_words=20):
    # Építünk egy szótárat: szó -> lehetséges következő szavak listája
    word_dict = nltk.defaultdict(list)
    for w1, w2 in nltk.bigrams(text):
        word_dict[w1].append(w2)
    
    # Generálás
    import random
    current_word = "The" # Kezdő szó
    result = [current_word]
    
    for _ in range(num_words):
        if current_word in word_dict:
            next_word = random.choice(word_dict[current_word])
            result.append(next_word)
            current_word = next_word
        else:
            break
    return " ".join(result)

print(simple_generate(selected_text))
```

**Elmélet:**
*   **BoW (Bag of Words):** Csak bedobáljuk a szavakat gyakoriság szerint. Eredmény: értelmetlen katyvasz.
*   **Bigram modell:** Mindig az előző szót nézi ($P(w_n | w_{n-1})$). Eredmény: lokálisan (2-3 szó erejéig) értelmesnek tűnő, de globálisan zagyva szöveg. Ez a modern LLM-ek (mint a GPT) őse.

### Összefoglaló a vizsgához
Ha ezt a feladatot kapod:
1.  **Import:** `nltk`, `nltk.download('book')`.
2.  **Elemzés:** Használd a `concordance`-t a kontextushoz, a `FreqDist`-et a statisztikához.
3.  **Összehasonlítás:** Használd a `brown` korpuszt és a `ConditionalFreqDist`-et, hogy megmutasd, hogyan változik a nyelvhasználat kategóriánként.
4.  **Generálás:** Demonstráld, hogy a `bigram` (szópár) alapú generálás már kezd hasonlítani az igazi nyelvre, ellentétben a véletlen szavakkal.