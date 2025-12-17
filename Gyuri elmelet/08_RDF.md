Vettem a kérést! Mivel az előző anyagoknál (NLP) hiányoltad a konkrétumokat, itt most nagyon figyelni fogok arra, hogy a technikai részleteket (szintaxis, működési elv, kódrészletek értelmezése) mélyebben kifejtsem.

Ez a diasor az **RDF** (az adatok leíró nyelve) és a **SPARQL** (a lekérdező nyelv) technikai részleteibe megy bele. Ez a "Szemantikus Web SQL-je", tehát itt konkrétan kódot és logikát kell érteni.

Íme a részletes, vizsgaorientált kidolgozás:

---

### 1. A Szemantikus Web Felépítése (Ismétlés + Kontextus)
*(2-3. oldal)*

A "Tortaszelet" (Layer Cake) ábra újra előkerül, de most a fókusz az **RDF** és a **Query (Lekérdezés)** rétegen van.
*   **Lényeg:** A Szemantikus Web nem csak dokumentumokat köt össze, hanem *adatokat*.
*   **Hogyan?** Ágensek (szoftverrobotok) járják a webet, és szabványos formátumban (RDF) publikált adatokat gyűjtenek, majd ezeken logikai következtetéseket végeznek.

---

### 2. RDF (Resource Description Framework) – A technikai alapok
*(4-6. oldal)*

Az RDF nem egy fájlformátum, hanem egy **absztrakt adatmodell**.

**A legfontosabb szabály (Hármasok / Triples):**
Minden állítást három részből álló szerkezetben írunk le:
1.  **Alany (Subject):** Amiről beszélünk. (Mindig URI-nak kell lennie!)
2.  **Állítmány (Predicate):** A tulajdonság vagy kapcsolat. (Mindig URI!)
3.  **Tárgy (Object):** Az érték. (Lehet URI vagy Literális – pl. szöveg, szám).

**Két alapvető adattípus (Vizsgakérdés gyanús!):**
1.  **URI (Uniform Resource Identifier):** Egyedi azonosító. Pl. `http://example.org/book/book1`. Fontos: Nem kell, hogy ez a cím ténylegesen bejöjjön a böngészőben, ez csak egy "név", ami globálisan egyedi.
2.  **Literális (Literal):** Konkrét adatérték. Lehet sima szöveg ("macska"), vagy típusos adat (szám, dátum). Pl. `"42"^^xsd:integer`.

**Nyílt Világ Feltételezés (Open World Assumption - OWA) – *MÉLYEBB TARTALOM*:**
Ez nagyon fontos elvi különbség az SQL adatbázisokhoz képest!
*   **Zárt világ (SQL):** Ha egy adat nincs benne az adatbázisban, akkor az hamis (nem létezik).
*   **Nyílt világ (RDF):** Ha egy állítás hiányzik, az **nem jelenti azt, hogy hamis**, csak azt, hogy "nem tudunk róla".
    *   *Következmény:* Bárki, bárhol bővítheti az adatokat. Ha én azt mondom "A könyv írója Géza", te máshol mondhatod "A könyv ára 500 Ft". A gép ezt a kettőt egyesíti.
    *   *Hátrány:* Nem garantált a teljesség és a konzisztencia (ellentmondásos adatok lehetnek).

---

### 3. RDF Reprezentációk (Hogyan néz ki?)
*(7-9. oldal)*

Ugyanazt az adatot háromféleképpen ábrázolhatjuk. A vizsgán fel kell ismerned mindhármat!

1.  **Gráf (Graph):**
    *   **Körök (ellipszisek):** Erőforrások (URI-k).
    *   **Nyilak:** Tulajdonságok (Predikátumok).
    *   **Téglalapok (néha):** Literális értékek (szöveg).
    *   *Előnye:* Ember számára átlátható.

2.  **Logikai predikátumok (N-Triples jellegű):**
    *   Formátum: `stmt(Alany, Állítmány, Tárgy)` vagy `<Alany> <Állítmány> <Tárgy> .`
    *   *Előnye:* A gép könnyen dolgozza fel.

3.  **XML Szintaxis (RDF/XML) – *Ezt érteni kell sorról sorra!***
    *(Lásd a 9. oldal kódját)*
    *   `<rdf:RDF ...>`: A gyökérelem, itt definiáljuk a névtereket (`xmlns`).
    *   `<rdf:Description rdf:about="URI">`: Ez jelöli ki az **Alanyt**. Minden, ami ezen belül van, erre az alanyra vonatkozik.
    *   `<dc:title>Cím</dc:title>`: Ez az **Állítmány** (a tag neve) és a **Tárgy** (a tag tartalma, itt Literális).
    *   `<bib:Aff rdf:resource="URI"/>`: Ha a Tárgy nem szöveg, hanem egy másik erőforrás (URI), akkor nem a tag közé írjuk, hanem az `rdf:resource` attribútumba!
        *   *Ez fontos:* `<szerzo>Gipsz Jakab</szerzo>` = Jakab egy string.
        *   `<szerzo rdf:resource=".../jakab_id"/>` = Jakab egy entitás (URI).

---

### 4. SPARQL – A lekérdező nyelv
*(10-15. oldal)*

Ez az RDF adatbázisok "SQL"-je. Az alapelve a **Gráf mintaillesztés (Graph Pattern Matching)**.
Úgy képzeld el, mintha egy "lyukas" gráfot rajzolnál (ahol a lyukak a változók), és ezt rápróbálod az adathalmazra. Ahol illeszkedik, onnan kiveszi az adatokat.

**Szintaxis alapok:**
*   **Változók:** `?` jellel kezdődnek (pl. `?title`, `?x`).
*   **Szerkezet:**
    ```sparql
    PREFIX dc: <http://purl.org/dc/elements/1.1/>  # Rövidítések
    SELECT ?title                                  # Mit akarok visszakapni?
    WHERE {                                        # A minta
       <konyv_uri> dc:title ?title .               # <Alany> <Állítmány> ?Változó
    }
    ```

**Összetett lekérdezések (JOIN) – *MÉLYEBB TARTALOM*:**
Hogyan kapcsolunk össze táblákat? Itt nem kell `JOIN` parancs, a változók nevével trükközünk.
*(12. oldal példája)*
```sparql
WHERE {
   ?x foaf:name ?name .    # 1. sor: ?x-nek van neve (?name)
   ?x foaf:mbox ?mbox .    # 2. sor: UGYANANNAK az ?x-nek van emailje (?mbox)
}
```
Mivel mindkét sorban `?x` szerepel, a rendszer tudja, hogy olyan csomópontot kell keresni, aminek *neve is* és *emailje is* van. Ez az **implicit join**.

**Literálisok és típusosság (A csapda!) – *Kiemelten fontos a 13. oldal!***
Az RDF-ben a "macska" szó nem ugyanaz, mint az angol "macska".
*   `"cat"` (sima string) $\neq$ `"cat"@en` (angol string).
*   `"42"` (string) $\neq$ `"42"^^xsd:integer` (szám).
*   **A lekérdezésnél pontosan kell egyeznie!** Ha az adatbázisban `@en` van, de te a lekérdezésben nem írod oda, **nem fogja megtalálni**. (Ez tipikus hibaforrás).

**Blank Nodes (Üres csomópontok) – *Technikai részlet*:**
*(14-15. oldal)*
Néha van egy csomópont, aminek nincs URI-ja (pl. "valaki", akit nem nevezünk meg, de tudjuk, hogy létezik). Ezek az üres csomópontok (`_:a`, `_:b`).
*   A lekérdezésben a változók (`?x`) illeszkedhetnek ezekre az üres csomópontokra is.
*   Az eredményben ilyenkor belső azonosítókat kapunk vissza (pl. `_:c`).

---

### 5. Esettanulmányok (Szabványos szótárak)
*(16. oldal)*

Ezeket illik felismerni, mert ezek az "építőkockák", amiket mindenki használ a saját RDF-jében:
*   **Dublin Core (dc):** Alapvető metaadatok (Cím, Szerző, Dátum). Könyvtári szabványból jött.
*   **FOAF (Friend of a Friend):** Emberek és kapcsolataik leírására (név, email, ismeri).
*   **SKOS:** Tezauruszok leírására.
*   **WordNet:** Szemantikus szótár (amit az előző előadásban vettünk).

### Összefoglalva a vizsgára (A "Hard" részek):

1.  **RDF Hármas szerkezete:** Tudd, mi lehet Alany (csak URI/Blank), Állítmány (csak URI), Tárgy (URI/Blank/Literal).
2.  **RDF/XML olvasása:** Tudd értelmezni, hogy a `<tag>` mikor predikátum és mikor osztálytípus. Tudd, mit csinál az `rdf:about` és az `rdf:resource`.
3.  **SPARQL Join:** Értsd meg, hogy a közös változónév (`?x`) végzi az összekapcsolást.
4.  **Literálisok egyezése:** A típusos és nyelvi címkés literálisok csak pontos egyezés esetén találhatók meg.
5.  **OWA (Nyílt Világ):** Tudd a definíciót (a hiányzó adat nem hamis, csak ismeretlen).