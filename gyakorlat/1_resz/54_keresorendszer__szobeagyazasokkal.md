Ez a gyakorlat (5.4.) a félév egyik legösszetettebb, de leghasznosabb feladata. Itt kapcsoljuk össze a **hagyományos keresőket** (Invertált Index, amit a 2. gyakorlatban láttál) a **modern mesterséges intelligenciával** (Word Embeddings / FastText, amit az 5.3-ban).

A feladat célja: **Szemantikus Kereső** építése. Ez azt jelenti, hogy ha a felhasználó arra keres, hogy *"eb"*, a rendszer megtalálja a *"kutya"* szót tartalmazó dokumentumokat is, mert tudja, hogy a jelentésük hasonló.

Itt a részletes útmutató a vizsgához:

---

### 1. Elméleti háttér (Mit kell érteni?)

Három módszert hasonlít össze a notebook:
1.  **Kulcsszavas keresés (Keyword Search):** Pontos egyezés. Ha "autó"-t keresel, a "kocsi" szót tartalmazó cikkeket NEM találja meg. (Ez az alap `search`).
2.  **Kiterjesztett keresés (Query Expansion):** A FastText segítségével megkeressük a keresőszó szinonimáit (pl. autó -> kocsi, gépjármű), és ezekre is rákeresünk az indexben.
3.  **Rangsorolt keresés (Ranked Retrieval):** Nem csak azt mondjuk meg, hogy *benne van-e* a szó, hanem hogy *mennyire releváns* a szöveg. Ehhez a **dokumentum-vektorokat** használjuk (a szavak vektorainak átlagát).

---

### 2. A gyakorlati megvalósítás lépései (A TODO részek)

A vizsgán a következő kódblokkokat kell tudnod kiegészíteni.

#### 1. lépés: Invertált Index Építése
A `tokenizer` már megvan (spaCy alapú). A feladat az index szótár feltöltése.

*   **Logika:** Végigmegyünk minden filmen (entry). Végigmegyünk a film szavain. Minden szónál felírjuk a szótárba, hogy "ez a szó szerepel ebben a filmben (ID)".
*   **Megoldás:**
```python
index = {}
# Az 'i' változóval indexelünk, ami gondolom egy számláló vagy ID
i = 0 
for entry in tqdm(imdb['train']):
    # A szöveg tokenizálása
    tokens = tokenizer(entry['text'])
    
    for tok in tokens:
        if tok not in index:
            index[tok] = set() # Halmaz, hogy ne legyen duplikáció
        index[tok].add(i) # Hozzáadjuk a dokumentum ID-ját
    i += 1
```

#### 2. lépés: Alap keresés (`search`)
Ez a klasszikus megoldás.
*   **Megoldás:**
```python
def search(pattern):
    # Ha a szó benne van az indexben, visszaadjuk a találatokat (ID-k halmaza)
    # Ha nincs, üres halmazt (set()) adunk vissza
    return index.get(pattern, set())
```

#### 3. lépés: Kiterjesztett keresés (`search_extended`)
Ez a feladat "trükkös" része.
*   **Logika:**
    1. Vesszük az eredeti szót (`pattern`).
    2. Megkérdezzük a modelltől a hasonló szavakat (`get_nearest_neighbors`).
    3. Ezekre a hasonló szavakra is lekérjük a találatokat az indexből.
    4. Vesszük az összes találat **unióját** (bármelyik szó szerepel, az jó).
*   **Megoldás:**
```python
def search_extended(pattern, max_similar=3):
    # 1. Eredeti szó találatai
    res = search(pattern)
    
    # 2. Hasonló szavak keresése a FastText modellel
    # A get_nearest_neighbors listát ad vissza: [(hasonlóság, szó), ...]
    neighbors = model.get_nearest_neighbors(pattern, k=max_similar)
    
    for similarity, similar_word in neighbors:
        # 3. Hozzáadjuk a hasonló szavak találatait is (UNIO)
        res = res.union(search(similar_word))
        
    return res
```

*   **Válasz a kérdésre ("Miért lett nulla a különbség?"):**
    Ha lefuttatod a `search_extended("cat")` és `search_extended("cats")` parancsokat, az eredmény halmaz ugyanaz lesz. Miért? Mert a "cat" legközelebbi szomszédja a "cats", és a "cats" legközelebbi szomszédja a "cat". Így mindkét esetben ugyanazokra a szavakra (cat, cats, kitty stb.) keresünk rá, tehát a találati lista azonos lesz.

#### 4. lépés: Rangsorolás dokumentum-vektorokkal (`search_ranked`)
Ez a legszebb matematikai rész. Hogyan hasonlítunk össze egy szót egy egész szöveggel?
*   **Logika:**
    1. **Query vektor:** A keresőszó vektora (`model[pattern]`).
    2. **Dokumentum vektor:** A dokumentumban lévő összes szó vektorának **átlaga** (`np.mean`).
    3. **Távolság:** Megmérjük a query vektor és a dokumentum vektor távolságát.
*   **Megoldás:**
```python
def search_ranked(pattern):
    pattern_embedding = np.mean([model[w.lower()] for w in pattern.split()], axis=0) # Ha több szavas a query
    
    ranks = {}
    # Csak azokon a doksik futunk végig, amikben szerepel a szó (hogy gyors legyen)
    # Vagy a 'search(pattern)' eredményén iterálunk
    for i in search(pattern): 
        # Lekérjük a doksi szövegét (itt feltételezzük, hogy elérjük az imdb objektumot)
        text = imdb['train'][i]['text']
        tokens = tokenizer(text)
        
        # --- A TODO RÉSZ ITT VAN ---
        # Kigyűjtjük a szavak vektorait, ha ismeri őket a modell
        word_vectors = [model[w] for w in tokens if w in model]
        
        if not word_vectors: continue # Ha üres, átugorjuk
            
        # Dokumentum vektor = szavak átlaga
        doc_embedding = np.mean(word_vectors, axis=0)
        # ---------------------------
        
        # Távolság mérése
        ranks[i] = np.linalg.norm(pattern_embedding - doc_embedding)
    
    # Rendezés növekvő sorrendbe (a kisebb távolság a jobb!)
    sorted_ranks = sorted(ranks.keys(), key=ranks.get)
    return sorted_ranks
```

---

### 3. Összefoglaló a vizsgára (Cheat Sheet)

Ha ezt a feladatot kapod, ezek a kulcsszavak és függvények kellenek:

1.  **FastText betöltés:** `fasttext.load_model(path)`.
2.  **Hasonló szavak:** `model.get_nearest_neighbors(word)`.
3.  **Szó vektora:** `model[word]`.
4.  **Invertált index:** Egy szótár (`dict`), ahol `kulcs=szó`, `érték=set(dokumentum_idk)`.
5.  **Set műveletek:**
    *   `set.add(item)` (hozzáadás indexelésnél)
    *   `set.union(other_set)` (egyesítés kiterjesztett keresésnél).
6.  **NumPy átlag:** `np.mean(lista, axis=0)`. Ez csinál a sok szóvektorból egy darab dokumentumvektort.
7.  **NumPy távolság:** `np.linalg.norm(a - b)`.

**Tipp:** A `datasets` könyvtár és az `imdb` betöltés csak körítés. A lényeg a `model` használata az indexelés és a keresés javítására. Ha megérted, hogy a **szinonimák keresése** (extended) és az **átlagolás** (ranked) hogyan működik, megvan a feladat.