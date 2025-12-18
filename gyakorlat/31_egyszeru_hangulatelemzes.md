Ez a gyakorlat a **Szótáralapú Hangulatelemzésről (Lexicon-based Sentiment Analysis)** szól. Ez a legegyszerűbb módszer arra, hogy szövegekről eldöntsük, pozitív vagy negatív hangvételűek-e, anélkül, hogy bonyolult mesterséges intelligenciát (Machine Learning) használnánk.

Itt van az elméleti összefoglaló és a gyakorlati lépések a `TODO` részek kitöltéséhez.

---

### 1. Elméleti háttér (Mit kell tudni a vizsgához?)

#### A) A módszer: "Bag of Words" és Szótárak
Ez a megközelítés nem "érti" a mondatot, csak szavakat számol.
*   **Alapelv:** Ha egy szövegben több a pozitív szó (pl. "jó", "szuper", "szeretem"), mint a negatív (pl. "rossz", "borzalmas", "hiba"), akkor a szöveg valószínűleg pozitív.
*   **Lexikonok (Szótárak):** Neked kell létrehoznod két listát:
    *   `pos`: pozitív szavak listája.
    *   `neg`: negatív szavak listája.

#### B) Kiértékelés (Evaluation Metrics)
Hogyan tudjuk, hogy jól működik-e a program? Összehasonlítjuk a **becslésünket** (szószámlálás eredménye) a **valósággal** (a felhasználó által adott csillagok száma, a `score`).

Ehhez a **Tévesztési Mátrix (Confusion Matrix)** elemeit használjuk:
*   **TP (True Positive):** A program **Pozitívnak** tippelte, és a valóságban is **Jó** volt az értékelés (>3 csillag). -> *Siker*
*   **TN (True Negative):** A program **Negatívnak** tippelte, és a valóságban is **Rossz** volt (<3 csillag). -> *Siker*
*   **FP (False Positive):** A program **Pozitívnak** hitte, de valójában **Rossz** volt. (Vaklárma). -> *Hiba*
*   **FN (False Negative):** A program **Negatívnak** hitte, de valójában **Jó** volt. (Nem vette észre a jót). -> *Hiba*

**F1-Score (A végső pontszám):**
Ez egy mérőszám, ami egyesíti a pontosságot (Precision) és a visszahívást (Recall). A vizsgán ezt a képletet be kell írnod a kódba:
$$F1 = 2 \cdot \frac{Precision \cdot Recall}{Precision + Recall}$$

---

### 2. Gyakorlati megvalósítás (A kód kiegészítése)

A vizsgán a `TODO` részeket kell megírnod. Íme a lépések:

#### 1. lépés: Szólisták definiálása
A kód elején meg kell adnod a szavakat.
```python
pos = ["good", "great", "excellent", "amazing", "love", "best", "perfect"]
neg = ["bad", "terrible", "awful", "worst", "hate", "useless", "broken", "slow"]
```
*Tipp:* Érdemes csupa kisbetűvel írni őket, és a szöveget is kisbetűsíteni.

#### 2. lépés: Tokenizálás (Szavakra bontás)
A `for d in data:` cikluson belül fel kell darabolni a mondatot.
*   **Feladat:** A `d['review']` szöveget szavakra bontani.
*   **Megoldás:**
```python
# Kisbetűsítés (.lower) és darabolás (.split)
# A 're' modullal profibb lenne (írásjelek eltávolítása), de alap szinten ez is elég:
words = d['review'].lower().split()
```

#### 3. lépés: Számlálás (A `pcount` és `ncount` kiszámolása)
Meg kell számolni, hány szó van a `pos` és hány a `neg` listában.
*   **Megoldás:**
```python
pcount = 0
ncount = 0
for w in words:
    if w in pos:
        pcount += 1
    if w in neg:
        ncount += 1
```

#### 4. lépés: F1-pontszám kiszámítása
A ciklus végén, a kiíratásnál (`print`) ki kell számolni a metrikákat.
*   **Elmélet:**
    *   $Precision = TP / (TP + FP)$
    *   $Recall = TP / (TP + FN)$
*   **Kódmegoldás (ügyelve a 0-val osztásra!):**
```python
# Precision és Recall számítása
precision = tp / (tp + fp) if (tp + fp) > 0 else 0
recall = tp / (tp + fn) if (tp + fn) > 0 else 0

# F1 Score
f1 = 2 * (precision * recall) / (precision + recall) if (precision + recall) > 0 else 0

print(f"F1-pontszám: {f1:.2%}")
```

---

### 3. Összefoglaló a megoldás menetéről (Algoritmus)

Ezt a logikát kell követned a vizsgafeladatban:

1.  **Adatbetöltés:** A megadott kódrészlet (`csv` olvasó) ezt általában megcsinálja, neked csak érteni kell, hogy a `data` egy lista, amiben dictionary-k vannak (`{'review': '...', 'score': 5}`).
2.  **Szótárkészítés:** Írj be 5-10 jellemző angol (vagy magyar, feladattól függő) pozitív és negatív szót.
3.  **Iteráció:** Végigmegyünk minden egyes értékelésen.
4.  **Tisztítás:** A szöveget kisbetűssé alakítjuk (`.lower()`), hogy a "Good" és a "good" ugyanaz legyen.
5.  **Matching:** Megnézzük, a szöveg szavai benne vannak-e a listáinkban.
6.  **Döntés (Rule-based logic):**
    *   Ha `pos_szavak_száma > neg_szavak_száma` -> A gép szerint **POZITÍV**.
    *   Egyébként -> A gép szerint **NEGATÍV**.
7.  **Validálás:**
    *   Összevetjük a gép döntését a valódi `score`-ral.
    *   Növeljük a megfelelő számlálót (`tp, tn, fp, fn`).
8.  **Eredmény:** A végén kiszámoljuk az F1 metrikát, ami megmondja, mennyire volt jó a szólistánk.

**Mire figyelj a vizsgán?**
*   A `3`-as osztályzat (score) gyakran semleges. A kódban lévő feltétel (`>3` és `<3`) kihagyja a 3-asokat. Ez helyes, ne lepődj meg rajta.
*   Ha a feladat azt kéri, hogy *javítsd* az eredményt, akkor a `pos` és `neg` listákat kell bővítened olyan szavakkal, amiket a szövegekben látsz (pl. ha sokszor szerepel a "laggy" és azt nem ismerte fel negatívnak, add hozzá a listához).