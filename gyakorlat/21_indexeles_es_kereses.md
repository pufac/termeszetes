Ez a konkrét notebook a **szöveges információvisszakeresés (Information Retrieval - IR)** alapjait fekteti le. A kurzus ezen része nem a bonyolult neurális hálókról szól (mint a BERT vagy GPT), hanem a "klasszikus" keresőmotorok működésének logikájáról: hogyan találjunk meg gyorsan szavakat nagy mennyiségű szövegben.

Itt van az összefoglaló az elméletről, a használt eszközökről és a megoldási stratégiáról.

---

### 1. Elméleti háttér (Mit kell tudni a vizsgához?)

A vizsgafeladatok megoldásához három fő fogalmat kell értened ebből az anyagból:

1.  **Korpusz (Corpus):** Maga a nyers szövegállomány, amiben keresni akarunk. Ezt először "tisztítani" és darabolni kell.
2.  **Tokenizálás (Tokenization):** A szöveg egységekre bontása. Itt a szöveget fejezetekre, majd a fejezeteket szavakra (*tokenekre*) bontjuk. A "szó" definíciója fontos: kezelni kell az írásjeleket (pl. a "ház," nem ugyanaz, mint a "ház"), és a kis/nagybetűket.
3.  **Invertált Index (Inverted Index):** Ez a legfontosabb elméleti elem.
    *   *Naiv módszer:* Végigolvasni az egész könyvet minden keresésnél (lassú).
    *   *Invertált index:* Egy olyan adatszerkezet (szótár), ahol a kulcs a szó, az érték pedig azon dokumentumok (vagy fejezetek) listája, ahol a szó előfordul.
    *   Példa: `{'alma': [1, 5, 8], 'körte': [2]}`. Így a keresés azonnali ($O(1)$ idejű), nem kell végigolvasni a szöveget.

---

### 2. A legfontosabb eszköz: Python `re` modul (Regular Expressions)

A képernyőképen a `re` (RegEx) könyvtár a kulcsszereplő. A vizsgán szinte biztosan kell reguláris kifejezéseket írnod szövegtisztításra.

**Mit kell tudnod fejből?**
*   `re.split(pattern, text)`: Szöveg feldarabolása a minta mentén.
*   `re.findall(pattern, text)`: Összes előfordulás kigyűjtése.
*   `\d`: Számjegy.
*   `\w`: Szóalkotó karakter (betűk, számok).
*   `\s`: Whitespace (szóköz, tab, újsor).
*   `+`: Az előzőből egy vagy több (pl. `\d+` = többjegyű szám is).
*   `[IVX]+`: Római számok karakterei (I, V, X stb.).

---

### 3. Gyakorlati megoldás lépésről-lépésre (A notebook alapján)

Így kell megoldani az ilyen típusú feladatokat a vizsgán:

#### A) Adatbetöltés és tisztítás
A kép alapján a feladat a szöveg fejezetekre bontása volt.
*   **Feladat:** "Egy olyan reguláris kifejezést kell készíteni, ami felismeri a fejezetszámokat (római számok, utánuk pont és két soremelés)".
*   **Megoldás (A `TODO` helyére):**
    A minta valószínűleg ilyesmi: `r'\n+[IVXLCDM]+\.\n+'`
    *   `\n+`: Egy vagy több soremelés (hogy biztosan új bekezdés legyen).
    *   `[IVXLCDM]+`: Római számjegyek kombinációja.
    *   `\.`: Egy konkrét pont karakter (a sima `.` mindenre illeszkedik, ezért kell a `\`).
*   **Kód:**
    ```python
    import re
    # A szöveg szétdarabolása fejezetekre
    parts = re.split(r'\n\s*[IVXLCDM]+\.\s*\n', data)
    ```

#### B) Keresőindex készítése (A `TODO` helyére)
A feladat a fejezetek szavakra bontása volt, hogy felépüljön a szótár.
*   **Feladat:** Szavakra bontani a szöveget, levágva az írásjeleket.
*   **Logika:** Egy Python `dict`-et (szótárt) építünk. `index = {}`.
*   **Megoldás (Regex):** `r'\W+'` (bármi, ami NEM szóalkotó karakter, legyen az elválasztó).
*   **Kód implementáció:**
    ```python
    index = {}
    chapter_id = 1
    
    # parts[1:] mert az első elem esetleg üres vagy előszó lehet
    for chapter_text in parts[1:]:
        # Szavakra bontás (minden ami nem betű/szám, az elválasztó)
        words = re.split(r'\W+', chapter_text)
        
        for word in words:
            # Normalizálás: kisbetűsítés, hogy a "Körte" és "körte" egynek számítson
            word = word.lower()
            
            if len(word) > 2: # Csak értelmes szavakkal foglalkozunk
                if word not in index:
                    index[word] = []
                # Ha még nincs benne ez a fejezet, hozzáadjuk
                if chapter_id not in index[word]:
                    index[word].append(chapter_id)
        
        chapter_id += 1
    ```

#### C) Keresés (Search)
Ha az index kész, a keresés már csak egy szótári lekérdezés.
*   **Alap:** `print(index.get('világ', 'Nincs találat'))`
*   **Fejlett (Adv search a 2. képen):** Itt kell kezelni a logikát.
    *   Ha a feladat több szavas keresést kér (pl. "jámbor világ"), akkor fel kell darabolni a keresett kifejezést is, külön rákeresni a szavakra, és venni a találati listák **metszetét** (AND kapcsolat).
    *   *Tipp:* Pythonban a `set` (halmaz) típusnak van `intersection` (metszet) függvénye.

---

### 4. Általános "recept" a vizsgafeladatokhoz

Ha a vizsgán ilyen notebook-alapú feladatot kapsz, ezt a folyamatot kövesd:

1.  **Értelmezd az inputot:**
    *   Szövegfájl? CSV? JSON?
    *   *Tipp:* Mindig nézz bele az első pár sorba (`print(data[:500])`), hogy lásd a struktúrát (vannak-e HTML tag-ek, furcsa karakterek, soremelések).

2.  **Pre-processing (Előfeldolgozás):**
    *   Ez a legfontosabb lépés. A "szemét" (pontok, vesszők, HTML kódok) kiszűrése.
    *   Használd a `re` modult vagy egyszerű string műveleteket (`.replace()`, `.lower()`, `.strip()`).
    *   Ha NLP feladat, mindig döntsd el: kellenek a stopwordök (és, hogy, az)? Kell a kisbetűsítés?

3.  **Adatszerkezet építése:**
    *   Szinte mindig **Dictionary-t (`{}`)** vagy **Pandas DataFrame-et** kell használnod.
    *   Ha gyors keresés a cél -> Dictionary (Hash map).
    *   Ha statisztika / táblázatos adat a cél -> Pandas.

4.  **Algoritmus implementálása:**
    *   Itt írod meg a logikát (pl. keresés, átlagszámítás, hangulatelemzés).
    *   Ne bonyolítsd túl! A vizsga általában működő prototípust kér, nem optimalizált ipari kódot.

**Összefoglalva:** Ez az anyag a *szabályalapú* (rule-based) szövegfeldolgozást tanítja. A kulcs a **RegEx** magabiztos használata a tisztításhoz, és a **Dictionary** használata az adatok tárolásához és gyors eléréséhez.