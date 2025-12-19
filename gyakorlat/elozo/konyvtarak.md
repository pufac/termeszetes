Összegyűjtöttem neked tematikusan az összes Python könyvtárat, ami előkerült a PDF-ekben és a gyakorlatokon. A vizsgán ezeket kell felismerned, importálnod és használnod.

### 1. Általános segédeszközök (Adatkezelés, Tisztítás)

Ezek az "alapozó" könyvtárak, szinte minden feladat elején kellenek.

*   **`re` (Regular Expressions):**
    *   **Mire jó?** Szövegminták keresése, cseréje.
    *   **Hol használtuk?** Szövegtisztításnál (pl. fejezetek szétvágása), email címek/telefonszámok kinyerésénél.
    *   *Kulcsfüggvények:* `re.split()`, `re.findall()`, `re.sub()`.
*   **`requests`:**
    *   **Mire jó?** Adatok letöltése az internetről (HTTP kérések).
    *   **Hol használtuk?** Mikes Kelemen leveleinek vagy a Gutenberg könyvek letöltésénél.
    *   *Kulcsfüggvény:* `requests.get(url).text`.
*   **`bs4` (BeautifulSoup):**
    *   **Mire jó?** HTML kód takarítása ("Web scraping"). Kiszedi a szöveget a `<div>` és `<p>` elemek közül.
    *   **Hol használtuk?** Nyers weblapok szöveggé alakításánál.
    *   *Kulcsfüggvény:* `BeautifulSoup(html, 'html.parser').get_text()`.
*   **`pandas`:**
    *   **Mire jó?** Táblázatos adatok kezelése (mint az Excel, csak Pythonban).
    *   **Hol használtuk?** A vizsgafeladat (hotel reviews) CSV fájljának beolvasásakor.
    *   *Kulcsfüggvény:* `pd.read_csv()`, `df.head()`, `df['oszlop']`.
*   **`numpy` (Numerical Python):**
    *   **Mire jó?** Matek, vektorok, mátrixok kezelése.
    *   **Hol használtuk?** Szóbeágyazásoknál (Word Embeddings). Két szó távolságát ezzel számoljuk.
    *   *Kulcsfüggvény:* `np.linalg.norm()` (Euklideszi távolság), `np.mean()` (átlagolás).
*   **`tqdm`:**
    *   **Mire jó?** Folyamatjelző csík (progress bar) kirajzolása.
    *   **Hol használtuk?** Amikor sok adatot dolgoztunk fel (pl. beágyazások betöltése), hogy lássuk, hol tart.

---

### 2. Klasszikus NLP (Szabályalapú)

Itt még nem "gondolkodik" a gép, csak listákkal és statisztikával dolgozik.

*   **`nltk` (Natural Language Toolkit):**
    *   **Mire jó?** A svájci bicska a klasszikus NLP-hez. Tokenizálás, szótövezeés, stopszó-szűrés.
    *   **Hol használtuk?** Szinte mindenhol az elején.
    *   *Modulok:*
        *   `nltk.tokenize`: `word_tokenize`, `sent_tokenize`.
        *   `nltk.corpus`: `stopwords` (névelők kiszűrése).
        *   `nltk.stem`: `SnowballStemmer` (szótővesítés - a butábbik).
        *   `nltk.sentiment`: `SentimentIntensityAnalyzer` (VADER - hangulatelemzés).
        *   `nltk.probability`: `FreqDist` (szógyakoriság számolás).
*   **`simplemma`:**
    *   **Mire jó?** Lemmatizálás (szótári alak visszaállítása).
    *   **Hol használtuk?** Magyar nyelvű szövegeknél, mert az NLTK ebben gyenge. (Pl. "almákat" -> "alma").
*   **`collections`:**
    *   **Mire jó?** Speciális konténerek.
    *   **Hol használtuk?** `Counter` (számláló) a Zipf-törvényes feladatnál (szavak gyakorisága).

---

### 3. Modern NLP (Gépi tanulás alapú)

Itt már neurális hálók és vektorok vannak.

*   **`spacy`:**
    *   **Mire jó?** Ipari szintű, gyors NLP. Mindent egyben csinál (pipeline): tokenizál, lemmatizál, felismeri a tulajdonneveket (NER).
    *   **Hol használtuk?** Az 5.2-es gyakorlatban (entitásfelismerés, mondatelemzés).
    *   *Kulcsok:* `nlp = spacy.load(...)`, `doc = nlp(text)`, `displacy` (vizualizáció).
*   **`fasttext`:**
    *   **Mire jó?** Szóbeágyazás (Facebook fejlesztése). Hasonló szavak keresése vektorok alapján.
    *   **Hol használtuk?** Amikor szinonimákat kerestünk vagy analógiákat (Király - Férfi + Nő).
*   **`sklearn` (Scikit-learn):**
    *   **Mire jó?** Klasszikus gépi tanulás.
    *   **Hol használtuk?** `PCA` (Principal Component Analysis) - a 300 dimenziós vektorok lerombolása 2D-re, hogy ki tudjuk rajzolni őket.

---

### 4. Deep Learning & LLM (A csúcstechnológia)

*   **`tensorflow` / `tensorflow_hub`:**
    *   **Mire jó?** Mélytanuló keretrendszer.
    *   **Hol használtuk?** Az **ELMo** modell betöltésénél (kontextuális beágyazás).
*   **`torch` (PyTorch) / `transformers` (Hugging Face):**
    *   **Mire jó?** A mai legmenőbb AI modellek (BERT, GPT) futtatása.
    *   **Hol használtuk?** A **BERT** modell használatánál.
*   **`openai`:**
    *   **Mire jó?** Hozzáférés a GPT-3.5 / GPT-4 modellekhez API-n keresztül.
    *   **Hol használtuk?** Chatbot építésnél, promptolásnál.
*   **`tiktoken`:**
    *   **Mire jó?** Megszámolja, hány tokenből (szótöredékből) áll a szöveg az OpenAI modellek számára.
    *   **Hol használtuk?** Hogy lássuk, hogyan darabolja a gép a szöveget.
*   **`ollama`:**
    *   **Mire jó?** Helyi (saját gépen futó) LLM-ek kezelése Pythonból.
    *   **Hol használtuk?** Amikor nem akartunk az OpenAI-nak fizetni, és Llama3-at futtattunk.

---

### 5. Vizualizáció (Grafikonok)

*   **`matplotlib.pyplot`:**
    *   **Mire jó?** Grafikonok rajzolása.
    *   **Hol használtuk?** Zipf-törvény ábrázolása, szóbeágyazások (PCA) pöttyeinek kirajzolása.
*   **`wordcloud`:**
    *   **Mire jó?** Szófelhő generálása (gyakori szavak nagyobbak).
    *   **Hol használtuk?** A Mikes Kelemen levelek elemzésénél.

### Összefoglaló táblázat a vizsgához:

| Feladat típus | Mit használj? (Könyvtár) |
| :--- | :--- |
| **HTML letöltés/tisztítás** | `requests`, `bs4` |
| **Szófelhő / Gyakoriság** | `nltk` (tokenizálás, stopwords), `wordcloud` |
| **Hangulatelemzés (gyors)** | `nltk.sentiment` (VADER) vagy `TextBlob` |
| **Lemmatizálás (Magyar)** | `simplemma` |
| **Nyelvtan / Entitások (NER)** | `spacy` |
| **Hasonló szavak / Analógia** | `fasttext`, `numpy` |
| **Modern AI (BERT/GPT)** | `transformers`, `openai` |