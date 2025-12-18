Ez a notebook (5.3. gyakorlat) a **Szóbeágyazásokkal (Word Embeddings)**, konkrétan a **GloVe** és **FastText** modellekkel foglalkozik. Ez a vizsga egyik legizgalmasabb, de kicsit "matekosabb" része.

Itt nem stringekkel, hanem **vektorokkal** (számsorozatokkal) dolgozunk. A gép a szavakat térbeli pontokként értelmezi.

---

### 1. Elméleti háttér (Mit kell érteni?)

#### A) Szóvektorok (Word Vectors)
A korábbi módszerek (pl. One-hot encoding) nem tudták, hogy a "király" és a "királynő" hasonló szavak.
*   **Word Embedding:** Minden szót egy $N$ dimenziós vektorrá (pl. 50, 100 vagy 300 számjegy) alakítunk.
*   **Szemantikai közelség:** Ha két szó jelentése hasonló, a vektoraik a térben **közel** vannak egymáshoz.
*   **Távolságmérés:** Általában **Euklideszi távolságot** (két pont távolsága) vagy **Koszinusz hasonlóságot** (két vektor szöge) használunk. A notebookban az euklideszi van (`np.linalg.norm`).

#### B) Vektoraritmetika (Analógiák)
Ez a legfontosabb elméleti elem! A jelentésviszonyok megmaradnak a vektorműveletekben is.
*   **A híres példa:** $Király - Férfi + Nő \approx Királynő$
*   Matematikailag: A "Férfi -> Király" irányvektor (ami a hatalmat/uralkodást jelenti) ugyanaz, mint a "Nő -> Királynő" vektor.
*   Feladat a vizsgán: **"Kicsi úgy aránylik a kisebbhez, mint a nagy a X-hez."** Ezt kivonással és összeadással kell megoldani.

#### C) PCA (Dimenziócsökkentés)
A 300 dimenziós teret nem tudjuk ábrázolni.
*   **PCA (Principal Component Analysis):** Egy statisztikai módszer, ami a 300 dimenzióból csinál 2-t (X és Y koordináta), úgy, hogy a lehető legtöbb információ megmaradjon. Így kirajzolható a térkép.

---

### 2. Gyakorlati megvalósítás (Hogyan oldd meg a feladatokat?)

A vizsgán valószínűleg a **betöltést**, a **távolságszámítást** és az **analógia-keresést** kérik.

#### 1. lépés: A modell betöltése (File I/O)
A GloVe fájl egy sima szövegfájl: minden sor egy szóval kezdődik, utána rengeteg szám.
Ezt egy Python szótárba (`dict`) kell betölteni, ahol:
*   Kulcs: a szó (string).
*   Érték: a vektor (`numpy` tömb).

**Kód:**
```python
import numpy as np

embeddings = {}
with open("glove.6B.50d.txt", "r", encoding="utf-8") as f:
    for line in f:
        values = line.split()
        word = values[0]
        # A többi (1-től a végéig) a számok, ezeket float-tá konvertáljuk
        vector = np.array(values[1:], dtype='float32')
        embeddings[word] = vector
```

#### 2. lépés: Távolságszámítás
Két szó hasonlóságának mérése. Minél kisebb a szám, annál hasonlóbbak.
**Kód:**
```python
# Távolság = a vektorok különbségének hossza (normája)
dist = np.linalg.norm(embeddings['cat'] - embeddings['dog'])
print(dist)
```

#### 3. lépés: Az "Analógia" függvény megírása (A fő TODO feladat)
A notebookban lévő `embed_associate` függvényt így kell megírni.
A feladat: `token1` (small) -> `token2` (smaller) :: `token3` (big) -> `X`?

**Matematikai logika:**
$$X \approx (token2 - token1) + token3$$
Példa: $smaller - small + big \approx bigger$

**Implementáció:**
```python
def embed_associate(token1, token2, token3):
    # 1. Kiszámoljuk a célvektort a képlet alapján
    # (smaller - small + big)
    target_vector = embeddings[token2] - embeddings[token1] + embeddings[token3]
    
    closest_word = None
    min_dist = float('inf') # Végtelen, hogy az első biztosan kisebb legyen
    
    # 2. Végigmegyünk az összes szón a szótárban
    for word, vec in embeddings.items():
        # Fontos: Ne önmagukat találjuk meg (pl. ne a 'big' legyen az eredmény)
        if word in [token1, token2, token3]:
            continue
            
        # 3. Megmérjük a távolságot a célvektortól
        dist = np.linalg.norm(vec - target_vector)
        
        # 4. Megkeressük a legkisebbet (minimum keresés)
        if dist < min_dist:
            min_dist = dist
            closest_word = word
            
    return closest_word

# Teszt
print(embed_associate('small', 'smaller', 'big')) # Eredmény: 'bigger' vagy 'larger'
```

#### 4. lépés: Vizualizáció (PCA)
Ehhez általában a `sklearn` könyvtárat használjuk. Ezt ritkábban kell fejből írni, de érteni kell.
1.  Kigyűjtjük a kirajzolandó szavak vektorait egy listába.
2.  `pca.fit_transform(vektorok)` -> Ebből kapunk egy listát, ahol minden vektornak már csak 2 koordinátája van.
3.  `plt.scatter` -> Pöttyök kirajzolása.
4.  `plt.annotate` -> Feliratok (szavak) ráírása a pöttyökre.

---

### 3. Mire figyelj a vizsgán?

*   **Típusok:** A vektorok `numpy` array-ek legyenek, különben nem működik a kivonás (`-`).
*   **Túlilleszkedés (Trivial solution):** Az analógia keresésnél (3. lépés) gyakori hiba, hogy a program visszaadja a bemeneti szót (pl. a `big`-et). Ezért kell a `if word in [...] continue` feltétel.
*   **Nyelv:** A GloVe általában angol. Ha magyar feladat van, akkor **FastText** modellt kell betölteni (a notebook végén van erre utalás). A betöltés logikája (1. lépés) ugyanez, csak a fájlnév más.
*   **Grep:** A notebook elején lévő `!grep` parancs linuxos parancs, nem Python. A vizsgán Python kódot szoktak kérni, úgyhogy inkább a `if 'king' in embeddings:` logikát használd keresésre.

**Összefoglalva:**
A kulcs a **vektorművelet**. Tudd betölteni a fájlt, és tudd, hogy az analógia nem más, mint vektorok összeadása és kivonása, majd a legközelebbi szomszéd megkeresése.