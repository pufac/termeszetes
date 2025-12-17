Szia! Ez a diasor a **Formális Nyelvi Modellek és Szintaktikai Elemzés** témakörét öleli fel. Ez egy nagyon fontos lépcsőfok: az előző anyagokban csak statisztikai alapon néztük a szavakat (pl. "ez a szó hányszor szerepel"), most viszont **a mondatok szerkezetét (grammatikáját)** vizsgáljuk meg, hogy a gép megértse, ki csinál mit, kivel.

Itt a részletes, strukturált összefoglaló:

---

### 1. Hol helyezkedünk el? (A nyelvi elemzés szintjei)
*(4-6. oldal)*

Ismétlésként látjuk az NLP folyamatot (pipeline). A tisztítás és tokenizálás után jön a lényeg:
*   **Szótövesítés / Lemmatizálás:** A szavak alapalakjának megtalálása (pl. *almákat* -> *alma*).
*   **Szófaji címkézés (POS Tagging):** Mi a szó? Főnév, ige, melléknév?
*   **Szintaktikai elemzés (Parsing):** Ez a mai anyag fő témája. A mondat fan-szerkezetének felépítése (alany, állítmány, tárgy viszonya).

---

### 2. A Formális Nyelvtan (Grammatika)
*(7-8. oldal)*

Ahhoz, hogy a gép megértse a mondatot, szabályokra van szüksége. Ezt hívjuk formális nyelvtannak ($G$).
A definíció: **$G = (N, \Sigma, P, S)$**
*   **$N$ (Nemterminálisok):** Nyelvtani kategóriák (pl. *Mondat, IgeiCsoport, Főnév*). Ezek nem jelennek meg a végső szövegben, csak a szerkezetben.
*   **$\Sigma$ (Terminálisok):** A konkrét szavak vagy karakterek, amik a mondatban vannak (pl. *"piros", "lámpa"*).
*   **$P$ (Produkciós szabályok):** Átírási szabályok.
    *   Példa: `UTASÍTÁS -> PARANCS + MODOSÍTÓ` (pl. "Kapcsold" + "fel").
*   **$S$ (Start szimbólum):** A kiindulópont (pl. *Mondat*).

**Példa a diáról:**
`„Kapcsold fel a piros lámpát”`
A szabályok segítségével a gép lebontja:
1.  *Kapcsold* (Parancs)
2.  *fel* (Módosító)
3.  *a* (Névelő) *piros* (Jelző) *lámpát* (Tárgy) -> Ez egy Névszói Csoport (NP).

---

### 3. Szintaktikai Elemzés (Parsing)
*(10-12. oldal)*

Ez az algoritmus, ami a szövegből és a nyelvtanból felépíti a fát (**Parse Tree**).
*   **Cél:** A bemeneti mondathoz megtalálni azt a szabálysorozatot, ami levezeti azt.
*   **Fa szerkezet:** A gyökérben a Mondat ($S$) áll, a leveleken a szavak.
*   **Problémák:**
    *   **Rekurzió:** Ha egy szabály önmagára hivatkozik, a gép végtelen ciklusba kerülhet.
    *   **Komplexitás:** Túl sok lehetőség van, lassú lehet a keresés.
*   **Megoldás:** Dinamikus programozás (pl. **CYK algoritmus**). Ez részproblémákra bontja a mondatot, és eltárolja a részeredményeket, hogy ne kelljen mindent újra számolni.

---

### 4. Eszközök: ANTLR
*(13-16. oldal)*

Nem írunk kézzel elemzőprogramot, mert bonyolult. Használunk **Parser Generátorokat**, mint az **ANTLR**.
*   **Hogyan működik?**
    1.  Írsz egy nyelvtani fájlt (`.g4` kiterjesztés).
    2.  Az ANTLR ebből legenerálja a Java/Python/C# kódot.
    3.  Ez a kód képes szöveget beolvasni és fává alakítani.
*   **Mit kezdünk a fával? (Két módszer):**
    1.  **Listener (Passzív):** Az elemző végigszalad a fán, és szól (event), ha belép egy csomópontba vagy kilép onnan. (Egyszerűbb).
    2.  **Visitor (Aktív):** Te írod meg a bejáró logikát, te döntöd el, mikor mész a gyerekekhez. (Nagyobb kontroll).

---

### 5. Szemantika és Többértelműség (Disambiguation)
*(17. oldal)*

A nyelvtan helyessége nem jelenti azt, hogy értjük is a mondatot.
**A híres példa:**
> *"Egy reggel pizsamában lőttem egy elefántot."*

*   *Kérdés:* Ki volt pizsamában? Én, vagy az elefánt?
*   Nyelvtanilag mindkettő helyes fa lehet!
*   **Többértelműség feloldása:** Itt kell a **szemantika** (jelentéstan) és a **világtudás**. (Tudjuk, hogy elefántok ritkán hordanak pizsamát $\to$ valószínűbb, hogy én voltam abban).

---

### 6. Kontrollált Természetes Nyelvek (CNL)
*(18-22. oldal)*

Mivel a természetes nyelv (pl. magyar) tele van kétértelműséggel, néha korlátozzuk a szabályokat.
*   **CNL lényege:** Rögzített szókincs + szigorú nyelvtan = **Egyértelmű értelmezés.**
*   **Alkalmazás:**
    *   Légiirányítás (hogy ne értsék félre a pilóták).
    *   Robotika (egyértelmű parancsok).
    *   Adatbázis lekérdezés ("reads english" -> gép automatikusan SQL lekérdezést csinál belőle).

---

### 7. Modern Hangalapú Rendszerek (Alexa, Siri)
*(23-33. oldal)*

Hogyan működik ez a gyakorlatban, pl. az Amazon Alexánál? **Ez a rész nagyon fontos gyakorlati szempontból!**

Egy parancs felépítése:
> *"Alexa, ask MyWeather for the temperature in Budapest"*

1.  **Wake Word (Ébresztő szó):** *"Alexa"* (ez aktiválja a hardvert).
2.  **Invocation Name (Hívószó):** *"MyWeather"* (ez mondja meg, melyik appot/skillt indítsa el).
3.  **Utterance (Kifejezés):** Amit a felhasználó mond.
4.  **Intent (Szándék):** Mit akar csinálni? -> *"GetTemperature"* (hőmérséklet lekérdezése).
5.  **Slot (Helyőrző/Változó):** A paraméterek -> *"Budapest"* (ez a város változója).

**Tanítás (Machine Learning):**
A modern rendszerek nem csak fix szabályokkal (mint az ANTLR), hanem **tanítóadatokon** (Treebank) is tanulnak.
*   *Tanulság:* A Microsoft "Tay" csetbotja rossz példa volt, mert a Twitterről tanult (szűretlen, szemetes adat), és 24 óra alatt náci lett. Fontos a minőségi tanítóadat!

### Összefoglalva a lényeg:
1.  **Grammatika:** Szabályok halmaza ($N, \Sigma, P, S$).
2.  **Parsing:** A szöveg fává alakítása a szabályok alapján.
3.  **ANTLR:** Eszköz, ami kódot generál a nyelvtanból. (Tudni kell a különbséget: Listener vs Visitor).
4.  **Ambiguity:** A "pizsamás elefánt" probléma (többértelműség), amit szemantikával oldunk fel.
5.  **Alexa modell:** Tudni kell, mi az **Intent** (szándék) és a **Slot** (paraméter).