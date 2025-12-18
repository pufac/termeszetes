Ez a gyakorlat a **szövegstatisztika** és a **Zipf-törvény** (Zipf's Law) témakörét dolgozza fel. Ez az NLP egyik legfontosabb alapvetése, ami megmagyarázza, miért kell stopword-szűrést végezni.

Itt van az elméleti háttér és a gyakorlati megvalósítás lépései a vizsgához.

---

### 1. Elméleti háttér: Zipf-törvény

Ebből az anyagból a legfontosabb, amit a vizsgán kérdezhetnek, az a **szavak eloszlása**:

*   **Zipf törvénye:** A természetes nyelvekben a szavak gyakorisága egy nagyon jellegzetes eloszlást követ (hatványtörvény).
    *   A leggyakoribb szó (angolban általában a *"the"*) kb. kétszer olyan gyakori, mint a második leggyakoribb.
    *   Háromszor olyan gyakori, mint a harmadik, és így tovább.
    *   Matematikailag: A $k$-adik leggyakoribb szó gyakorisága arányos $1/k$-val.
*   **Következmény:** Van néhány szó, ami iszonyatosan sokszor fordul elő (ezek a névelők, kötőszavak – **Stopwords**), és van rengeteg szó, ami csak egyszer-kétszer (ez a **Long Tail**).
*   **Gyakorlati haszna:** Ezért dobjuk el gyakran a leggyakoribb szavakat (stopwords removal), mert szemantikailag kevés információt hordoznak ("és", "a", "hogy"), viszont az adatfeldolgozást lassítják.

---

### 2. Gyakorlati megvalósítás (A kód kiegészítése)

A képen a `TODO` rész azt kéri, hogy **tokenizáld a szöveget** és **számold meg a szavakat**.

#### 1. lépés: Tokenizálás (Szavakra bontás)
A nyers `data` stringet szavakra kell vágni. Fontos a normalizálás!
*   **Kisbetűsítés (`.lower()`):** Hogy a "The" és a "the" ugyanannak számítson.
*   **Tisztítás:** A legegyszerűbb módszer a `re` (RegEx) modul, amit az előző gyakorlatban láttál. Ha nincs importálva `re`, akkor `data.split()` is működhet, de az benne hagyja a vesszőket (pl. "house,").

#### 2. lépés: Index építése (A `TODO` megoldása)
Kétféleképpen oldhatod meg a vizsgán:

**A) "Kézi" módszer (klasszikus Python):**
Ez a legbiztosabb, ha meg kell mutatni, hogy érted az algoritmust.
```python
import re 
# Ha nincs importálva a re, írd be a kódcellába!

# 1. Szavakra bontás (csak betűk és számok, kisbetűsítve)
words = re.findall(r'\w+', data.lower()) 

# 2. Iterálás és számlálás
for tok in words:
    if tok not in index:
        index[tok] = 0
    index[tok] += 1
```

**B) "Profi" módszer (`collections` modullal):**
Mivel a kódban lejjebb úgyis használja a `collections.Counter`-t, ezt megteheted egyből is, ha a feladat engedi a könyvtárhasználatot a ciklus helyett.
```python
import re
words = re.findall(r'\w+', data.lower())
# A ciklus helyett egy sorban:
index = dict(collections.Counter(words)) 
```
*Megjegyzés: A feladatban a `TODO` a `for tok in ...` szerkezetet sugallja, tehát valószínűleg az "A" (kézi) megoldást várják el a ciklussal.*

---

### 3. A kód többi részének magyarázata (Hogy értsd, mi történik)

A vizsgán kérdezhetik, mit csinál a vizualizációs rész a kód végén.

1.  **`collections.Counter(index).most_common(30)`**:
    *   Ez a parancs automatikusan rendezi a szótárat gyakoriság szerint csökkenő sorrendbe, és visszaadja a top 30-at.
    *   Eredménye egy lista tuple-ökkel: `[('the', 5000), ('and', 3000), ...]`

2.  **A vizualizáció (ASCII Chart):**
    ```python
    (120 * freq // index_top[0][1]) * '-'
    ```
    *   Ez egy **normalizálás (skálázás)**.
    *   `index_top[0][1]`: Ez a **leggyakoribb szó** előfordulása (a maximum érték).
    *   `freq`: Az aktuális szó gyakorisága.
    *   `freq / max`: Megadja az arányt (0 és 1 között).
    *   `120 * ...`: Felskálázza 120 karakter szélességre (hogy kiférjen a képernyőre).
    *   `* '-'`: Pythonban a string szorzása ismétlést jelent (pl. `3 * '-'` -> `---`).
    *   **Eredmény:** Egy vízszintes oszlopdiagram karakterekből kirajzolva, ami szépen mutatja a Zipf-görbét (hirtelen lecsökken, majd ellaposodik).

### Összefoglaló a megoldáshoz:
1.  Ismerd fel, hogy ez **gyakoriságelemzés**.
2.  Importáld a `re` modult, ha kell.
3.  A `TODO` helynél:
    *   Alakítsd **kisbetűssé** (`.lower()`).
    *   Szedd ki a **szavakat** (`re.findall(r'\w+', ...)`).
    *   Egy **ciklussal** számold meg őket az `index` szótárban (`index[szó] += 1`).