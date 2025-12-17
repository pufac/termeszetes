Szia! Értem, folytatjuk a szemantikus technológiák második, technikaibb felével. Ez a diasor a **Szemantikus Web** (Semantic Web) alapkoncepcióját és az **RDF** (Resource Description Framework) alapjait fekteti le.

Mivel ez egy technológiai bevezető, a vizsgán általában a **"Mi a különbség a hagyományos és a szemantikus web között?"** kérdéskörre, az **architektúrára (rétegek)**, és az **adatmodellre (RDF hármasok)** szoktak fókuszálni.

Íme a részletes, vizsga-fókuszú összefoglaló:

---

### 1. A probléma: A Klasszikus Web vs. Adatok Webje
*(2-5. oldal)*

A hagyományos (klasszikus) web dokumentumokból áll, amiket emberek olvasnak.
*   **A probléma:** A gép csak a linket látja (`<a href="...">`), de nem érti a jelentését. Nem tudja, hogy a link egy "szerzőre", egy "munkahelyre" vagy egy "könyvre" mutat.
*   **A cél:** Az **Adatok Webje (Web of Data)**.
    *   A dokumentumok helyett/mellett adatokat publikálunk.
    *   A linkeknek **típusa/jelentése** van (pl. `hasAuthor`, `isLocatedIn`).
    *   Standardizált elérési módok kellenek (hogy a gépek maguktól tudjanak adatot gyűjteni és integrálni).

> **Vizsgagyanús gondolat:** A hagyományos web *tárolja* a dolgokat (nekünk kell kikeresni), a szemantikus web képes *működtetni* a dolgokat (gépi feldolgozás, ágensek).

---

### 2. A megoldás menete: Adatintegráció (A Könyvesbolt példa)
*(6-17. oldal)*

Ez a példa végigvezet azon, hogyan lesz két különálló Excel-táblázatból (vagy adatbázisból) egy nagy tudásháló.

1.  **Relációs adatbázis:** Van két könyvesboltunk, mindkettőnek van egy táblázata (ID, Cím, Szerző). Ezek szigetek, nem tudnak egymásról.
2.  **Gráfosítás (Export):** Az adatokat csomópontokká (node) és élekké (edge) alakítjuk.
    *   A könyv egy csomópont.
    *   A szerző egy csomópont.
    *   Közöttük a kapcsolat (`author`) egy él.
3.  **Összekapcsolás (Linking):**
    *   Ha az egyik adatbázisban a szerző "Ghosh, Amitav", a másikban "Amitav Ghosh", a gépnek meg kell mondani, hogy ez **ugyanaz a személy**.
    *   Ha ez megtörténik, a gráfok összeolvadnak.
4.  **Külső források (Linked Open Data):**
    *   Nem kell mindent nekünk tárolni. Hivatkozhatunk a **DBpedia**-ra (a Wikipédia strukturált változata).
    *   Ha bekötjük a DBpedia-t, a rendszerünk rögtön tudni fogja a szerző születési dátumát, anélkül, hogy mi tárolnánk, mert a gráfban "átmászik" a DBpedia csomópontjára.

---

### 3. A Szemantikus Web Architektúrája (A "Tortaszelet")
*(22. oldal)* – **EZ A LEGFONTOSABB ÁBRA! (The Semantic Web Stack)**

Tim Berners-Lee (a Web atyja) álmodta meg ezt a réteges felépítést. A vizsgán gyakran kérdezik a rétegek sorrendjét és funkcióját. Lentről felfelé:

1.  **Unicode, URI:** Az alapok. Mindennek van egyedi azonosítója (URI) és karakterkészlete.
2.  **XML + NS (Namespaces):** A szintaxis. Hogyan írjuk le az adatot strukturáltan (címkékkel).
3.  **RDF + RDF Schema:** Az adatmodell. (Lásd lejjebb). Ez mondja meg, hogy "Alany - Állítmány - Tárgy".
4.  **Ontology vocabulary (OWL):** A fogalmak és kapcsolatok bonyolultabb leírása (pl. "minden ember halandó").
5.  **Logic (Logika):** Szabályok és következtetések.
6.  **Proof (Bizonyítás):** A gép le tudja vezetni, miért igaz egy állítás.
7.  **Trust (Bizalom):** Digitális aláírás, hitelesség.

---

### 4. RDF (Resource Description Framework)
*(24-27. oldal)*

Ez a Szemantikus Web "nyelve". Nem egy konkrét fájlformátum, hanem egy **adatmodell**.

**A Hármasok (Triples) koncepciója (Vizsgakérdés!):**
Minden információt 3 részből álló állításként írunk le:
1.  **Alany (Subject):** Amiről beszélünk (pl. egy könyv URI-ja).
2.  **Állítmány (Predicate):** A tulajdonság (pl. `hasTitle` - címe).
3.  **Tárgy (Object):** Az érték (pl. "Harry Potter" vagy egy másik URI).

**Reprezentációs formák (Hogyan néz ez ki?):**
*   **Gráf:** Körök (erőforrások) és nyilak (tulajdonságok). Embernek jó.
*   **XML:** A gépnek jó, de embernek nehezen olvasható (lásd 29. oldal).
*   **Logikai predikátumok:** Következtetéshez jó (pl. `stmt(person, holding, doc)`).

**Tervezési alapelvek (25. oldal) - Kiemelten fontos!**
1.  **Két adattípus van:**
    *   **URI:** Minden dolognak (ember, könyv, fogalom) van egy webes címe (pl. `http://umbc.edu/~finin`).
    *   **Literális:** Konkrét értékek (számok, szövegek, pl. "1989", "Blue").
2.  **Nyílt Világ Feltételezés (Open World Assumption - OWA):**
    *   *Hagyományos adatbázis:* Ha valami nincs benne az adatbázisban, az nem létezik (Hamis).
    *   *Szemantikus Web:* Ha valami nincs benne az adatbázisban, az **nem biztos, hogy hamis**, csak még nem tudunk róla. Bárki, bárhol, bármit állíthat és hozzáadhat a tudáshoz.

### Összefoglaló a vizsgára (Mit kell bemagolni?):

1.  **A definíció:** A Szemantikus Web a jelenlegi web kiterjesztése, ahol az adatoknak jól definiált jelentése van, így gépek és emberek jobban együttműködhetnek.
2.  **Architektúra (Stack):** URI -> XML -> RDF -> Ontológia (OWL) -> Logika -> Bizalom.
3.  **RDF Hármas:** Alany, Állítmány, Tárgy (Subject, Predicate, Object).
4.  **Nyílt Világ (OWA):** Az információ hiánya nem jelenti azt, hogy az állítás hamis.
5.  **Cél:** Adatintegráció, gépi következtetés, intelligens ágensek támogatása.