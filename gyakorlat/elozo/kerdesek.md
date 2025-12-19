Íme a kidolgozott válaszok a mintakérdésekre és a feladatokhoz tartozó útmutatók. Ezek lefedik a vizsga elméleti és gyakorlati részét is.

---

## I. Tesztkérdések (Elmélet)

### 1. Mi a felidézés (recall)?
A **Recall (Visszahívás)** azt mutatja meg, hogy a rendszer a **valóságban létező összes releváns elem közül mennyit talált meg**.
*   **Képlet:** $Recall = \frac{TP}{TP + FN}$
    *   *TP (True Positive):* Helyesen megtalált elemek.
    *   *FN (False Negative):* A rendszer által kihagyott, de valójában releváns elemek.
*   **Példa:** Van 100 spam levél. A szűrőm megtalált 80-at. A Recall = 80%. (Nem számít, hogy közben hány nem-spam levelet dobott ki tévesen, az a Precision lenne).

### 2. Az elemzési fa gyökerében egy nemterminális szimbólum áll. Igaz vagy hamis?
**Válasz: Igaz.**
*   **Magyarázat:** Az elemzési fa (parse tree) gyökere mindig a kiinduló szimbólum (pl. `S` vagy `Sentence`), ami egy **nemterminális** (tovább bontható) szimbólum. A fa levelei (az alja) a terminálisok (a konkrét szavak).

### 3. Mi a szóbeágyazás-vektor elemeinek értékkészlete?
**Válasz: Valós számok (Floating point numbers).**
*   **Magyarázat:** Egy szóbeágyazás (pl. `[0.25, -0.11, 0.89...]`) elemei nem egész számok (0/1), hanem folytonos valós számok, általában -1 és 1 között, vagy normalizált tartományban.

### 4. Mik a LinkedDB (LOD - Linked Open Data) ajánlás elemei?
Ez Tim Berners-Lee "5 csillagos" ajánlása:
1.  **OL (Open License):** Az adat legyen elérhető a weben nyílt licensszel (bármilyen formátumban).
2.  **RE (Readable):** Legyen gépileg olvasható strukturált adat (pl. Excel az szkennelt PDF helyett).
3.  **OF (Open Format):** Nyílt formátum használata (pl. CSV az Excel helyett).
4.  **URI:** Az entitásoknak legyen egyedi azonosítója (URI) és RDF szabványt használjon.
5.  **LD (Linked Data):** Tartalmazzon linkeket más adatokra, hogy a hálózat bejárható legyen.

### 5. Soroljon fel 4 szemantikus web technológiát a kapcsolódó nyelvek kifejezőerejének sorrendjében!
(A leggyengébbtől a legerősebbig):
1.  **XML:** Csak a szintaxist (fát) adja meg, jelentést nem.
2.  **RDF:** Gráf alapú adatmodell (Alany-Állítmány-Tárgy), kapcsolatokat ír le.
3.  **RDFS (RDF Schema):** Lehetővé teszi hierarchiák (osztályok, alosztályok) definiálását.
4.  **OWL (Web Ontology Language):** Bonyolult logikai szabályok, megszorítások, számosságok leírása.

---

## II. NLP Feladatok (Python / Colab)

### 1. Spamszűrő készítése (Gyakoriság alapú)
**Feladat:** Adott egy szöveg, döntsd el, hogy spam-e bizonyos kulcsszavak (pl. "ingyen", "nyeremény") alapján.

**Megoldás (Kódvázlat):**
```python
def simple_spam_filter(text):
    spam_keywords = ["ingyen", "nyeremény", "akció", "klikk", "kattints"]
    text = text.lower()
    
    # Megszámoljuk, hányszor szerepelnek a tiltott szavak
    spam_score = 0
    for word in spam_keywords:
        if word in text:
            spam_score += 1
            
    # Döntés: Ha több mint 1 gyanús szó van, akkor SPAM
    if spam_score > 1:
        return "SPAM"
    else:
        return "NOSPAM"

print(simple_spam_filter("Szia, nyeremény vár rád, kattints ide ingyen!"))
```

### 2. NLP feldolgozólánc (Lemmatizálás + POS)
**Feladat:** Szöveg tisztítása és elemzése. A legegyszerűbb a `spaCy` könyvtárral.

**Megoldás (Kódvázlat):**
```python
import spacy

# Modell betöltése (Colab-ban előtte: !python -m spacy download en_core_web_sm)
nlp = spacy.load("en_core_web_sm")

text = "The cats are running quickly."
doc = nlp(text)

print(f"{'Szó':<12} {'Lemma (Tő)':<12} {'POS (Szófaj)':<10}")
for token in doc:
    print(f"{token.text:<12} {token.lemma_:<12} {token.pos_:<10}")

# Eredmény példa: cats -> cat (NOUN), running -> run (VERB)
```

### 3. OpenAI GPT válasz dokumentum alapján
**Feladat:** Van egy `document.txt`, a GPT válaszoljon a kérdésre EBBŐL a fájlból.

**Megoldás (Kódvázlat):**
```python
from openai import OpenAI

# 1. Dokumentum beolvasása
context_text = "A vizsga időpontja 2025. január 20. Helyszíne a Q épület."

# 2. A Prompt összeállítása (RAG alapjai)
question = "Mikor lesz a vizsga?"
prompt = f"""
Használd az alábbi szöveget a válaszhoz:
SZÖVEG: {context_text}

KÉRDÉS: {question}
"""

# 3. API hívás
client = OpenAI(api_key="SK-...")
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": prompt}]
)

print(response.choices[0].message.content)
```

---

## III. Szemantikus Technológiák (RDF4J / SPARQL)

Ezeket az `Explore` -> `Query` ablakba kell írni.

### 1. Állapítsa meg egy megadott egyed tulajdonságait!
Például: Mit tudunk `Rembrandt`-ról?
```sparql
SELECT ?tulajdonsag ?ertek
WHERE {
    <http://dbpedia.org/resource/Rembrandt> ?tulajdonsag ?ertek .
}
```

### 2. Számolja meg egy tulajdonsággal rendelkező egyedek számát!
Például: Hányan születtek 1900-ban? (`dbo:birthYear` a tulajdonság).
```sparql
SELECT (COUNT(?ember) as ?darab)
WHERE {
    ?ember dbo:birthYear "1900"^^xsd:gYear .
}
```

### 3. Keresse meg a leírásnak megfelelő egyedeket!
Például: Keressünk olyan festőket, akik "Barokk" stílusban alkottak.
```sparql
PREFIX dbo: <http://dbpedia.org/ontology/>
SELECT ?festo
WHERE {
    ?festo rdf:type dbo:Artist .        # Legyen művész
    ?festo dbo:movement :Baroque .      # Stílusa legyen Barokk
}
```

---

## IV. Protege Ontológiaszerkesztő

### 1. Inkonzisztencia (Ellentmondás) javítása
**Feladat:** A Reasoner (következtető) piros hibát jelez. Javítsd ki!
*   **Hogyan találd meg?** Indítsd el a Reasonert (`Start Reasoner`). Ha piros felkiáltójel jelenik meg, kattints rá, majd a kérdőjelre (`Explain`).
*   **Gyakori hiba:**
    *   Egy egyed (pl. `Bodri`) típusa `Kutya`.
    *   De van egy olyan állítás is, hogy `Bodri` `nyávog`.
    *   A szabály szerint: Aki `nyávog`, az `Macska`.
    *   A `Kutya` és `Macska` osztályok `Disjoint`-ok (kizárják egymást).
*   **Megoldás:** Töröld a hibás állítást (pl. vedd ki, hogy Bodri nyávog, vagy vedd ki a Disjoint megszorítást, ha ez egy hibrid lény).

### 2. Osztály bővítése (Equivalent To)
**Feladat:** Bővítse a `Nagykoru` osztályt úgy, hogy akiknek a kora >= 18, azok automatikusan odatartozzanak.
*   **Lépések:**
    1.  Kattints a `Classes` fülön a `Nagykoru` osztályra.
    2.  Az **Equivalent To** (és NEM a SubClass Of!) dobozba írd be a definíciót.
    3.  **Szintaxis:** `Ember and (eletkor some integer[>= 18])`.
*   **Ellenőrzés:**
    1.  `Reasoner` -> `Synchronize`.
    2.  Menj az `Individuals` fülre.
    3.  Keress egy egyedet, akinek a kora 20.
    4.  Nézd meg, hogy sárgával megjelent-e nála a `Type: Nagykoru`.