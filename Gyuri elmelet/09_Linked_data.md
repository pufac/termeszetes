Rendben, vettem az adást! Ebben a diasorban már **kód szintű** dolgok és **konkrét szabályrendszerek** vannak. Itt nem elég a "nagy képet" érteni, a szintaxist és a logikai buktatókat fogom kiemelni, mert ezek tipikus vizsgafeladatok (pl. "Miért nem ad eredményt ez a SPARQL lekérdezés?").

Itt a részletes, technikai összefoglaló:

---

### 1. RDF (Resource Description Framework) – Technikai részletek
*(4-9. oldal)*

Már tudjuk, hogy Alany-Állítmány-Tárgy hármasokból áll. De hogyan néz ki ez a gyakorlatban?

**A) Az adatmodell elemei (Szigorú szabályok!):**
*   **Alany (Subject):** Csak **URI** vagy **Üres csomópont** (Blank Node) lehet. (Literális NEM lehet alany!)
*   **Állítmány (Predicate):** Csak **URI** lehet. (Tulajdonságot jelöl).
*   **Tárgy (Object):** Lehet **URI**, **Literális** vagy **Üres csomópont**.

**B) RDF/XML Szintaxis (A kód értelmezése – 9. oldal):**
A vizsgán gyakran adnak egy ilyen XML kódot, és le kell rajzolni a gráfot, vagy megmondani, mi micsoda.
```xml
<rdf:RDF ...>
    <!-- Ez itt az ALANY (Subject) definíciója -->
    <description about="http://umbc.edu/~finin/talks/idm02/">
        
        <!-- Ez itt az ÁLLÍTMÁNY (Predicate) -->
        <dc:title>
            <!-- Ez itt a TÁRGY (Object) mint Literális -->
            Intelligent Information Systems...
        </dc:title>

        <!-- Ha a Tárgy egy másik URI, akkor NEM a tag közé írjuk, hanem attribútumba! -->
        <bib:Aff resource="http://umbc.edu/"/> 
    </description>
</rdf:RDF>
```
*   **`rdf:about`**: Mindig az **Alanyt** jelöli.
*   **`rdf:resource`**: Mindig a **Tárgyat** jelöli, ha az egy hivatkozás (URI).
*   **Szöveg a tag-ek között**: A **Tárgy**, ha az egy Literális (string).

---

### 2. SPARQL – A lekérdező nyelv (A legfontosabb rész!)
*(10-15. oldal)*

Ez az RDF-hez tartozó "SQL". Mintaillesztésen alapul.

**A) A lekérdezés logikája (Mintaillesztés):**
A `WHERE { ... }` részben megadunk egy "lyukas" állítást, ahol a lyukak a változók (`?` jellel kezdődnek).
*   Példa: `?x foaf:name ?name .`
*   A gép megkeresi az összes olyan hármast, ahol a predikátum `foaf:name`, és a talált Alanyt berakja az `?x`-be, a Tárgyat a `?name`-be.

**B) A "Join" (Összekapcsolás) működése (12. oldal):**
Nincs `JOIN` kulcsszó. A kapcsolást a **változónevek egyezése** végzi.
```sparql
WHERE { 
    ?x foaf:name ?name . 
    ?x foaf:mbox ?mbox 
}
```
*   Mivel mindkét sorban `?x` van, a rendszer csak olyan találatokat ad vissza, ahol **ugyanannak** az erőforrásnak van neve IS és email címe IS.

**C) A "Literális Csapda" (Kiemelten VIZSGAGYANÚS! – 13. oldal):**
Az RDF-ben a típusosság nagyon szigorú.
*   Az adatbázisban: `"cat"@en` (angol nyelvű string).
*   Lekérdezés 1: `?v ?p "cat"` $\rightarrow$ **EREDMÉNYTELEN!** (Mert a sima string nem egyenlő az angol stringgel).
*   Lekérdezés 2: `?v ?p "cat"@en` $\rightarrow$ **SIKERES TALÁLAT.**
*   Ugyanez igaz számokra: `"42"` (string) nem egyenlő `"42"^^xsd:integer` (szám).
*   *Tanulság:* Ha a vizsgán ilyen feladat van, mindig nézd meg a nyelvi címkét (`@hu`, `@en`) vagy az adattípust!

**D) Üres csomópontok (Blank Nodes) a lekérdezésben (14-15. oldal):**
*   Jelölése: `_:a`, `_:b`.
*   Ezek olyan entitások, amiknek nincs globális nevük (URI), csak lokálisan léteznek a gráfban. A SPARQL-ben ezeket is le lehet kérdezni, de az eredményben belső azonosítókat kapunk vissza.

---

### 3. Linked Data (Kapcsolt Adatok) – Az 5 csillagos modell
*(7-13. és 25-27. oldal)*

Ez a gyakorlati megvalósítása a Szemantikus Webnek. Tim Berners-Lee definiálta a szabályait.

**A 4 Szabály (27. oldal):**
1.  Használj **URI**-kat a dolgok megnevezésére (ne csak ID-kat).
2.  Használj **HTTP URI**-kat (hogy a böngészőben is meg lehessen nézni).
3.  Ha valaki rákeres az URI-ra, adj vissza hasznos adatot szabványos formátumban (**RDF**, SPARQL).
4.  Tartalmazzon **linkeket** más adatokra (hogy hálózat legyen, ne sziget).

**Az 5 Csillagos Értékelés (Nagyon fontos elméleti modell! - 7-13. oldal):**
Ez azt méri, mennyire "jó" minőségű a nyílt adat (Open Data).
*   ★☆☆☆☆: Adat elérhető a weben, bármilyen formátumban (pl. egy szkennelt **PDF** kép). *Gép számára feldolgozhatatlan.*
*   ★★☆☆☆: Strukturált adat (pl. **Excel**), de zárt/szabadalmaztatott formátum. *Gép olvassa, de szoftverfüggő.*
*   ★★★☆☆: Nyílt, strukturált formátum (pl. **CSV**). *Bárki feldolgozhatja.*
*   ★★★★☆: URI-kat használ az azonosításra (**RDF**). *Minden elem egyedileg címezhető.*
*   ★★★★★: Össze van kötve más adatokkal (**Linked Data**). *Kontextusba helyezett adat.*

---

### 4. Fontos Adattárak (Ezeket felismerés szintjén kell tudni)
*(16. és 28. oldal)*

*   **DBpedia:** A Wikipédia strukturált változata (a legfontosabb központi csomópont a Linked Data felhőben).
*   **GeoNames:** Földrajzi adatok.
*   **FOAF (Friend of a Friend):** Emberek és kapcsolataik leírására szolgáló szótár.
*   **Dublin Core (dc):** Könyvtári metaadatok (cím, szerző, dátum).

### Összefoglaló: Mit kell "magolni"?

1.  **RDF/XML szintaxis:** Tudd, hogy az `about` az alany, a `resource` a tárgy (ha URI), és a tag neve a predikátum.
2.  **SPARQL Join:** Ha ugyanazt a változónevet használod két sorban, az ÉS kapcsolatot (metszetet) jelent.
3.  **Literálisok:** `"text"` $\neq$ `"text"@en` $\neq$ `"text"^^xsd:string`.
4.  **5 csillagos modell:** Tudd a sorrendet (PDF -> Excel -> CSV -> RDF -> Linked RDF).
5.  **A 4 Linked Data szabály:** URI, HTTP, Standard (RDF), Linkek.