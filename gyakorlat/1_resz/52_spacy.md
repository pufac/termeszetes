Ez a gyakorlat (5.2.) az **ipari szintű NLP** eszközre, a **spaCy**-ra fókuszál. A vizsgán ez egy szintlépés az NLTK-hoz képest: míg az NLTK inkább oktatási célokat szolgál és "listákkal" dolgozik, a spaCy objektumorientált, gyors, és mélyebb nyelvi összefüggéseket (szintaxis, entitások) tár fel.

Itt az összefoglaló a szükséges elméletről és a gyakorlati receptekről.

---

### 1. Elméleti háttér: A spaCy filozófiája

A legfontosabb különbség, amit értened kell:
*   **Pipeline (Feldolgozási lánc):** Amikor a spaCy-ba bedobsz egy szöveget, az automatikusan lefuttat rajta mindent: tokenizál, szófajt elemez, lemmatizál, mondattani elemzést végez, és felismeri a tulajdonneveket.
*   **Doc Object:** Az eredmény nem egy lista (mint az NLTK-nál), hanem egy `Doc` objektum. Ez "okos": minden szónak (Token) rengeteg tulajdonsága van (`.text`, `.lemma_`, `.pos_`, `.dep_`).

**Kulcsfogalmak a vizsgához:**
1.  **POS (Part-of-Speech):** Szófaj (pl. `NOUN` - főnév, `VERB` - ige).
2.  **Lemma:** Szótári alak (pl. "ate" -> "eat").
3.  **NER (Named Entity Recognition):** Tulajdonnév-felismerés (Személyek, Helyek, Cégek, Pénzösszegek).
4.  **Dependency Parsing (Függőségi elemzés):** Melyik szó melyikhez tartozik? (pl. ki a cselekvő? mi a tárgy?).

---

### 2. Gyakorlati megvalósítás (Receptek)

A vizsgán az alábbi mintákat kell tudnod alkalmazni.

#### A) Alapok: Betöltés és Elemzés
Mindig ezzel kezdesz. Be kell tölteni egy nyelvi modellt (angolnál `en_core_web_sm`).

```python
import spacy

# 1. Modell betöltése
nlp = spacy.load("en_core_web_sm")

# 2. Feldolgozás (Pipeline futtatása)
text = "Apple is looking at buying U.K. startup for $1 billion."
doc = nlp(text)

# 3. Hozzáférés az adatokhoz (Tokenenként)
for token in doc:
    print(token.text,        # Eredeti szó
          token.lemma_,      # Szótő (fontos az alulvonás!)
          token.pos_,        # Szófaj (pl. PROPN, VERB)
          token.is_stop)     # Stopszó-e?
```
*Tipp:* Ha magyar szöveggel kell dolgoznod a vizsgán, a kód ugyanez, csak a modellt kell cserélni (pl. `hu_core_news_lg`-re, vagy amit a feladat kér, pl. `huSpacy`).

#### B) Információkinyerés (NER - Named Entity Recognition)
Ha a feladat az: *"Gyűjtse ki a szövegben szereplő cégeket vagy személyeket!"*

```python
# A doc.ents tartalmazza a felismert entitásokat
for ent in doc.ents:
    print(ent.text, ent.label_) 
    # ent.label_ lehet: PERSON (személy), ORG (szervezet), GPE (ország/város), MONEY (pénz)
```
*Trükk:* Ha nem tudod, mit jelent egy rövidítés (pl. 'GPE'), használd a `spacy.explain("GPE")` parancsot!

#### C) Mintaillesztés (Matcher) – A "RegEx szteroidokon"
Ez a notebook egyik legfontosabb része. Ha nem konkrét szavakra keresel, hanem **szerkezetekre** (pl. "minden ige után álló főnév" vagy "szám + főnév").

*   **Feladat:** Keresd meg, ki mit evett (vagy mennyit).
*   **Megoldás:**
```python
from spacy.matcher import Matcher
matcher = Matcher(nlp.vocab)

# Minta definiálása: [Számnév] + [Főnév] (pl. "two pizzas")
pattern = [
    {"POS": "NUM"},   # Első szó legyen számnév
    {"POS": "NOUN"}   # Második szó legyen főnév
]

matcher.add("MENNYISEG_MINTA", [pattern])

matches = matcher(doc)
for match_id, start, end in matches:
    span = doc[start:end] # Kivágjuk a találatot
    print(span.text)
```

#### D) Vizualizáció (displaCy)
Ha a feladat: *"Ábrázolja a mondat szerkezetét!"*
```python
from spacy import displacy
# style="dep" -> mondattani fa (nyilak)
# style="ent" -> színes entitások
displacy.render(doc, style="dep", jupyter=True) 
```

---

### 3. Hogyan oldd meg a "Gyakorlófeladatok" részt?

A notebook végén lévő feladatok adják meg a vizsga lehetséges kérdéseit:

1.  **Kereső fejlesztése (Matcherrel):**
    *   Helyettesítsd a sima string keresést (`if "alma" in text`) a fenti `Matcher` logikával. Így megtalálod az "almát", "almákat", "almákból" alakokat is (ha a LEMMA-ra keresel: `{"LEMMA": "alma"}`).

2.  **Hangulatelemzés javítása (Adjective szűrés):**
    *   Az NLTK-s feladatnál minden szót néztél. Itt megteheted, hogy **csak a mellékneveket** (`ADJ`) vizsgálod, mert azok hordoznak érzelmet.
    *   *Kód:* `relevant_words = [token.text for token in doc if token.pos_ == "ADJ"]`

3.  **Tagadás kezelése (Dependency Parsing):**
    *   Ez a legnehezebb, de legszebb rész. Hogyan kezeld a "not bad"-et?
    *   Meg kell nézni a függőségi fát. Ha a "bad" szóhoz tartozik egy "neg" (negation) címkéjű gyerek (a "not"), akkor fordítsd meg a jelentését.
    *   *Logika:* `if token.text == "bad" and "neg" in [child.dep_ for child in token.children]: ...`

---

### Összefoglaló a vizsgához

Ha spaCy feladatot kapsz:
1.  **Import:** `import spacy`, `nlp = spacy.load(...)`.
2.  **Doc:** Mindig csinálj `doc`-ot (`doc = nlp(text)`).
3.  **Ne stringként kezeld:** Ne `.split()`-elj! Iterálj a `doc`-on (`for token in doc`).
4.  **Használd az attribútumokat:** `.lemma_` (szótő), `.pos_` (szófaj), `.ent_type_` (entitás típus).
5.  **Matcher:** Ha bonyolultabb keresés kell (pl. "főnév + ige"), használd a Matchert a RegEx helyett.