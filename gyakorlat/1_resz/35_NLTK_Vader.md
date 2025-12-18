Ez a feladat a **szabályalapú hangulatelemzés** „profi” szintje. Míg az előző (3.1-es) feladatban te írtál kézzel egy listát pozitív/negatív szavakról, itt egy előre megírt, validált és nagyon okos eszközt, a **VADER**-t (Valence Aware Dictionary and sEntiment Reasoner) kell használnod.

A vizsgán ez egy tipikus feladat: "Csináld meg ugyanazt, mint eddig, csak most egy könyvtári eszközzel, és hasonlítsd össze az eredményt."

Itt van az elméleti és gyakorlati összefoglaló a megoldáshoz.

---

### 1. Elmélet: Miben más a VADER?

A kézi szólistás módszerrel (amit a 3.1-ben csináltál) az a baj, hogy buta. Nem érti a fokozást ("nagyon jó"), a tagadást ("nem rossz") vagy az írásjeleket ("JÓ!!!").

**A VADER (NLTK) előnyei:**
1.  **Ismeri a kontextust:** Azt mondja, a *"not bad"* az pozitív (mert a "bad"-et tagadja), míg a te kézi listád negatívnak hinné a "bad" szó miatt.
2.  **Érti az intenzitást:** A *"GOOD!!!"* magasabb pontszámot kap, mint a *"good"*.
3.  **Kezeli az Emojikat és Szlengszavakat:** Kifejezetten közösségi média szövegekre (Twitter/X, review-k) találták ki.
4.  **Skálázott kimenet:** Nem csak annyit mond, hogy jó/rossz, hanem ad egy összetett mutatót (**Compound Score**) -1 (nagyon negatív) és +1 (nagyon pozitív) között.

---

### 2. A megvalósítás lépései (Step-by-Step)

A vizsgán ezt a logikai láncot kell leprogramoznod:

#### 1. lépés: Könyvtárak betöltése
A VADER az NLTK része, de külön le kell tölteni a lexikonját (`vader_lexicon`).

```python
import nltk
from nltk.sentiment import SentimentIntensityAnalyzer

# 1. Letöltés (vizsgán ezt mindig futtasd le egyszer)
nltk.download('vader_lexicon')

# 2. Az elemző inicializálása
sia = SentimentIntensityAnalyzer()
```

#### 2. lépés: Adatbetöltés
Ez ugyanaz, mint a 3.1-es feladatban. A lényeg, hogy legyen egy listád, amiben a szövegek (`review`) és a valódi pontszámok (`score`) vannak.

#### 3. lépés: Elemzés és Osztályozás (A ciklus)
Itt jön a lényeg. Minden sorra lefuttatjuk a VADER-t, és a kapott számot átkonvertáljuk a mi logikánkra (Pozitív/Negatív).

*   **Bemenet:** A nyers szöveg. **Fontos:** VADER-nél *nem* kell kisbetűsíteni és *nem* kell kivenni az írásjeleket/szavakat, mert azok hordozzák az érzelmi töltetet (pl. a CAPS LOCK számít!).
*   **A VADER hívása:** `sia.polarity_scores(text)`
*   **Kimenet értelmezése:** Ez egy szótárat ad vissza, pl.: `{'neg': 0.0, 'neu': 0.4, 'pos': 0.6, 'compound': 0.8}`. Nekünk a **compound** (összetett) érték kell.

**Kódvázlat a ciklusra:**
```python
# Inicializáljuk a tévesztési mátrix változóit
tp, tn, fp, fn = 0, 0, 0, 0

for row in data:
    text = row['review']
    real_score = row['score'] # Pl. 1-től 5-ig
    
    # --- VADER MŰKÖDÉSE ---
    scores = sia.polarity_scores(text)
    compound = scores['compound'] # Ez az érték -1 és 1 között van
    
    # --- DÖNTÉSHOZATAL (Klasszifikáció) ---
    # Mikor mondjuk azt, hogy a gép szerint pozitív?
    # Általában ha a compound > 0 (vagy > 0.05 a szigorúbb határértékhez)
    predicted_positive = (compound > 0.05)
    
    # --- VALÓSÁG ---
    # Mikor pozitív a valóságban? (A feladat szerint pl. 3 csillag felett)
    actually_positive = (real_score > 3)
    
    # --- ÖSSZEHASONLÍTÁS (Tévesztési mátrix) ---
    if predicted_positive and actually_positive:
        tp += 1
    elif not predicted_positive and not actually_positive:
        tn += 1
    elif predicted_positive and not actually_positive:
        fp += 1  # Gép szerint jó, valóságban rossz
    elif not predicted_positive and actually_positive:
        fn += 1  # Gép szerint rossz, valóságban jó
```

#### 4. lépés: Kiértékelés (F1 Score)
Ez ugyanaz a képlet, amit már használtál. A lényeg, hogy lásd, javult-e az eredmény a kézi szólistához képest.

```python
precision = tp / (tp + fp) if (tp + fp) > 0 else 0
recall = tp / (tp + fn) if (tp + fn) > 0 else 0
f1 = 2 * (precision * recall) / (precision + recall) if (precision + recall) > 0 else 0

print(f"VADER F1-pontszám: {f1:.2%}")
```

---

### 3. Mire figyelj a vizsgán? (Tippek)

1.  **Ne tisztítsd túl a szöveget!** A 3.1-es feladatban (kézi lista) hasznos volt a `.lower()` és a szűrés. A VADER-nél ez **hiba**! A VADER-nek szüksége van a *"!"* jelekre és a *"NAGYBETŰKRE"*, mert ezek növelik az érzelmi pontszámot.
2.  **A küszöbérték (Threshold):** A VADER dokumentációja szerint a pozitív érzelem küszöbe általában `compound >= 0.05`. De a vizsgán egyszerűsíthetsz `compound > 0`-ra is, ha nincs külön kikötve.
3.  **Összehasonlítás:** A feladat kéri, hogy hasonlítsd össze a saját (előző) megoldásoddal.
    *   *Elvárt válasz:* "A VADER valószínűleg jobb eredményt (magasabb F1-et) ért el, mert kezeli a tagadást (pl. 'not bad') és a fokozást, amit a szókeresős módszerem nem tudott."

**Összefoglalva:**
A feladat lényege, hogy lecseréled a saját `if word in pos_list` logikádat a `sia.polarity_scores(text)['compound']` hívásra, minden más (adatbetöltés, TP/TN számolás) változatlan marad.