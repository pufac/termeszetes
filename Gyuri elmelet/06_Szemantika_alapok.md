Szia! Értem a kérést. Mivel ez a tárgy elméletibb ("szárazabb") része, nagyon fontos pontosan érteni a fogalmakat, mert a vizsgán gyakran kérdeznek rá a különbségekre (pl. extenzionális vs. intenzionális).

Itt van a **Szemantika és Ontológiák** bevezető előadásának részletes, kidolgozott összefoglalója.

---

### 1. Bevezetés: Mi a Szemantika?
A szemantika a **jelentéstan**, vagyis a nyelvi elemek (szavak, mondatok) jelentésének vizsgálata.

**Miért van rá szükség?**
A számítógépeknek nem elég a szintaxis (nyelvtan/struktúra), érteniük kell a tartalmat is.
*   **Többértelműség (Ambiguity) feloldása:**
    *   *Szintaktikai többértelműség:* "Lőttem egy elefántot a pizsamámban." (Én voltam pizsamában, vagy az elefánt?) – A szintaxisfák (parse trees) mindkettőt lehetővé teszik, a szemantika és a világtudás segít dönteni.
    *   *Lexikai többértelműség:* "Betette a pénzt a *fiókba*." (Bankfiók vagy asztalfiók?) – A szövegkörnyezet (kontextus) alapján dől el.
*   **Következtetés (Entailment):** Ha "A rabló megölte az asszonyt", abból következik, hogy "Az asszony meghalt". Ezt a logikai kapcsolatot a gépnek értenie kell.

### 2. A Nyelv és a Világ kapcsolata (Fontos elméleti alapok)

Ahhoz, hogy formalizáljuk a jelentést, modellekre van szükségünk.

#### A) Saussure jelelmélete
Ferdinand de Saussure szerint a nyelvi jel két elválaszthatatlan részből áll:
1.  **Jelölő (Signifier):** A hangsor vagy írott szó (pl. a "fa" szó hangalakja).
2.  **Jelölt (Signified):** A fogalom, ami a fejünkben megjelenik (a fa képe/fogalma).

#### B) Ogden és Richards: A hivatkozás háromszöge (Triangle of Reference) **(Kiemelten fontos!)**
Ez a modell azt mutatja be, hogy a szavak nem közvetlenül kapcsolódnak a valós tárgyakhoz.
*   **Szimbólum (Symbol):** A szó maga (pl. "kutya").
*   **Referencia/Gondolat (Reference/Thought):** A fejünkben lévő fogalom a kutyáról.
*   **Referens (Referent):** A valós, fizikai tárgy a világban (az a konkrét kutya, amire mutatok).
*   **Lényeg:** A Szimbólum és a Referens között **nincs közvetlen kapcsolat** (csak szaggatott vonal), a kapcsolatot az emberi gondolat (Referencia) közvetíti.

#### C) Frege: A kompozicionalitás elve (Compositionality Principle) **(Vizsgakérdés gyanús!)**
Hogyan értünk meg végtelen számú új mondatot?
*   **Az elv:** Egy összetett kifejezés (pl. mondat) jelentése a benne lévő **részek (szavak) jelentéséből** és azok **kombinálásának módjából** (szintaxis) áll elő.
*   Vagyis: `Mondat jelentése = f(szavak jelentése, nyelvtani szerkezet)`.

---

### 3. A jelentés megközelítési módjai

Hogyan írjuk le, mit jelent egy szó? Négy fő módszert tárgyal az anyag:

#### 1. Lexikális szemantika (Lexical Semantics)
A szavak jelentését **más szavakhoz való viszonyukkal** definiáljuk.
*   Példa: A "kar" része a "testnek". A "kutya" egy fajtája az "állatnak". (Lásd később a WordNetnél).

#### 2. Extenzionális szemantika (Extensional Semantics)
A jelentést a világban található **összes olyan dolog halmazával** (kiterjesztésével) adjuk meg, amire a szó vonatkozik.
*   **Példa:** Az "EU tagállamok" jelentése a konkrét lista: {Ausztria, Belgium, Bulgária...}.
*   **Ekvivalencia:** Ha két kifejezésnek ugyanaz a kiterjesztése, akkor extenzionálisan egyenlőek.
    *   Pl. 2020-ban: "Angela Merkel" == "Németország kancellárja". A két halmaz ugyanazt az egy embert tartalmazza.

#### 3. Intenzionális szemantika (Intensional Semantics)
A jelentést a dolgok **tulajdonságaival/belső jellemzőivel** írjuk le.
*   **Példa:** "Kacsa" = {van szárnya, tud úszni, tud repülni...}.
*   **Különbség az extenzionálissal szemben (Klasszikus példa):**
    *   *Esthajnalcsillag* (Evening Star) vs. *Hajnalcsillag* (Morning Star).
    *   **Extenzionálisan:** Ugyanaz (mindkettő a Vénusz bolygó).
    *   **Intenzionálisan:** Különböző (az egyik reggel látható, a másik este; más a jelentéstartalom/fogalom).

#### 4. Prototípus szemantika
Nem definíciót adunk, hanem egy "legjellemzőbb példányt".
*   Példa: Ha azt mondom "madár", mindenki verébre vagy galambra gondol (prototípus), nem pingvinre (pedig az is madár, csak a periférián van).

---

### 4. WordNet: Egy lexikális ontológia

A WordNet a legfontosabb gyakorlati példa a szemantikus hálókra. Ez egy **pszicholingvisztikai alapú tezaurusz**.

#### A) Alapfogalmak
*   **Synset (Szinonima halmaz):** A WordNet alapegysége. A fogalmakat nem definícióval, hanem szinonimák halmazával írja le.
    *   Pl. {autó, kocsi, gépjármű} -> Ez EGY darab synset, egy fogalmat jelöl.
*   **Lexikális Mátrix:** Ez a modell szemlélteti a szavak és jelentések viszonyát.
    *   Ha egy sorban több szó van: **Szinonímia** (több szó ugyanazt jelenti).
    *   Ha egy oszlopban több jelentés van: **Poliszemía** (többértelműség - egy szóalak több fogalmat jelöl).

#### B) Relációk (Kapcsolatok) a WordNetben
A fogalmak (synsetek) között szemantikai relációk vannak:

1.  **Szinonímia:** Két szó szinonima, ha felcserélhetők a mondatban anélkül, hogy az igazságtartalom változna (Leibniz-elv).
2.  **Antonímia:** Ellentétes jelentés (pl. hideg <-> meleg).
3.  **Hiponímia / Hiperonímia (IS-A kapcsolat):**
    *   Alárendeltség/Fölérendeltség.
    *   *Hiponima:* "kutya" (szűkebb fogalom).
    *   *Hiperonima:* "állat" (tágabb fogalom, gyűjtőfogalom).
    *   Ez tranzitív (ha a kutya állat, és az állat élőlény, akkor a kutya élőlény).
4.  **Meronímia / Holonímia (PART-OF kapcsolat):**
    *   Rész-egész viszony.
    *   *Meronima:* "ujj" (része).
    *   *Holonima:* "kéz" (egész).
    *   Több fajtája van a WordNetben: rész-tárgy (faág-fa), tag-gyűjtemény (fa-erdő), anyag-tárgy (alumínium-repülő).

---

### 5. EuroWordNet és Magyar WordNet (HuWN)

Hogyan készítsünk WordNetet más nyelvekre?

*   **EuroWordNet:** Több európai nyelv összekapcsolása.
*   **ILI (Inter-Lingual Index):** Nem közvetlenül fordítják a szavakat (pl. magyar->holland), hanem van egy központi, nyelvfüggetlen index (fogalomtár), és minden nyelv ehhez kapcsolódik.
*   **Magyar WordNet (HuWN) építése:** Két módszert kombináltak:
    1.  **Kiterjesztés (Expand):** Az angol WordNetet lefordították (synseteket), és átvették a relációkat.
    2.  **Összefésülés (Merge):** Meglévő magyar erőforrásokat (pl. Értelmező Kéziszótár, szinonimaszótárak) használtak fel.
*   **Nehézség:** Az igék kezelése. Az angol és magyar igerendszer logikája nagyon más, ezért itt speciális "vegyes" módszertan kellett.

### Összefoglaló a vizsgához (Mit kell tudni?):
1.  **Hivatkozás háromszöge:** Szó -> Gondolat -> Dolog (nincs közvetlen szó-dolog kapcsolat).
2.  **Kompozicionalitás:** A teljes jelentés a részek jelentéséből áll össze.
3.  **Extenzionális vs. Intenzionális:** Kiterjesztés (lista) vs. Belső tulajdonság. (Esthajnalcsillag példa).
4.  **WordNet felépítése:** Synsetek (szinonima halmazok) alkotják a csomópontokat, nem önálló szavak.
5.  **Relációk:** Tudd a példákat (Hiponima = "fajtája", Meronima = "része").