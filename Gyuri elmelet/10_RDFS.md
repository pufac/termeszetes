Rendben, vettem! Ez a diasor az **RDF Sémákról (RDFS)** és a **szótárakról (SKOS, Dublin Core)** szól. Itt már nemcsak az adatokat írjuk le, hanem a **struktúrát** és a **szabályokat** (milyen osztályok és tulajdonságok léteznek).

Ez a Szemantikus Web "Schema" rétege (mint az SQL-ben a `CREATE TABLE`).

Íme a részletes, technikai magyarázatokkal bővített összefoglaló:

---

### 1. RDFS (RDF Schema) – A szabályok nyelve
*(4. oldal)*

Az RDF önmagában csak adatokat közöl ("Gipsz Jakab az írója ennek a könyvnek"). De ki mondja meg, mi az az "író"? És mi az a "könyv"?
*   **RDFS célja:** Szótárak és sémák definiálása. Leírja az osztályokat (Classes) és a tulajdonságokat (Properties), valamint ezek viszonyát.

---

### 2. Adatintegrációs problémák (Miért kell séma?)
*(5-6. oldal)*

A példában két különböző adatforrásból jön információ:
1.  Az egyik azt mondja: `Austin Mitchell` született `1934`-ben.
2.  A másik azt mondja: A `281`-es ID-jú dolog született `1934`-ben.
3.  A `281`-es ID-ről kiderül, hogy az egy **választókerület** (Constituency), nem ember.
*   **Probléma:** Választókerületnek nincs születési dátuma (DOB - Date of Birth).
*   **Megoldás:** Definiálni kell a szabályokat (sémát), hogy a `DOB` tulajdonság csak `Ember` típusú dologhoz tartozhat.

---

### 3. RDFS Alapelemek és Kényszerek (A technikai lényeg!)
*(11-18. oldal)* – **Kiemelten fontos vizsgatéma!**

Hogyan írjuk le ezeket a szabályokat RDFS-ben?

**A) Osztályok és Tulajdonságok definiálása:**
*   `rdf:type`: Megmondja, hogy egy erőforrás minek a példánya. (Pl. `Gipsz Jakab rdf:type Ember`).
*   `rdf:Property`: Ez definiál egy új tulajdonságot (pl. `írója`, `született`).

**B) Domain és Range (Értelmezési tartomány és Értékkészlet):**
Ez a legfontosabb logikai szabályrendszer az RDFS-ben!
*   **`rdfs:domain` (Alany típusa):** Megmondja, hogy egy tulajdonságnak (pl. `írója`) **kiből** kell kiindulnia.
    *   *Példa:* Ha `írója` domain-je `Könyv`, és leírom, hogy `X írója Y`, akkor a gép kikövetkezteti, hogy **X egy Könyv** (még ha nem is mondtam külön).
*   **`rdfs:range` (Tárgy típusa):** Megmondja, hogy a tulajdonság **milyen értékre** mutathat.
    *   *Példa:* Ha `írója` range-e `Ember`, és `X írója Y`, akkor a gép tudja, hogy **Y egy Ember**.

> **Vizsgacsapda:** Az RDFS-ben a kényszerek nem "tiltanak", hanem **következtetnek**!
> Ha azt mondom: `A széknek van születési dátuma` (és a születési dátum domain-je Ember), a rendszer nem dob hibát, hanem **kikövetkezteti, hogy a szék egy Ember**. (Ez furcsa, de így működik a szemantikus logika).

**C) Hierarchiák (Öröklődés):**
*   **`rdfs:subClassOf`:** Alosztály. (Pl. `Kutya subClassOf Állat`). Minden, ami Kutya, az automatikusan Állat is.
*   **`rdfs:subPropertyOf`:** Altulajdonság. (Pl. `anyja subPropertyOf szülője`). Ha X anyja Y-nak, akkor X szülője is Y-nak.

---

### 4. Dublin Core (DC) – A szabványos metaadatok
*(38-56. oldal)*

Ez egy kész, szabványos szótár dokumentumok leírására. Nem kell fejből tudni mind a 15 elemet, de a legfontosabbakat fel kell ismerni:
*   **`dc:title`**: Cím.
*   **`dc:creator`**: Szerző/Alkotó (személy vagy szervezet).
*   **`dc:subject`**: Téma (kulcsszavak).
*   **`dc:description`**: Leírás/Absztrakt.
*   **`dc:date`**: Dátum (általában YYYY-MM-DD formátum).
*   **`dc:format`**: Fájlformátum (MIME type, pl. `text/html`).
*   **`dc:identifier`**: Egyedi azonosító (pl. URL, ISBN).

**Refinements (Finomítók):** A Dublin Core elemei tovább szűkíthetők (pl. `date` -> `created`, `modified`).

---

### 5. SKOS (Simple Knowledge Organization System)
*(55-69. oldal)*

Ez a szabvány arra való, hogy **tezauruszokat, fogalomtárakat, osztályozási rendszereket** írjunk le RDF-ben. (Könnyebb, mint az OWL, de "okosabb", mint a sima címkézés).

**Főbb elemek (Mit kell tudni?):**
*   **`skos:Concept`**: A fogalom maga.
*   **Címkék (Labels):**
    *   `skos:prefLabel`: Az "igazi", preferált név (pl. "Macska").
    *   `skos:altLabel`: Szinonima, alternatív név (pl. "Cica", "Macsek").
    *   `skos:hiddenLabel`: Rejtett címke (pl. elgépelések kezelésére: "Mcska"), hogy a kereső megtalálja, de ne jelenjen meg.
*   **Kapcsolatok:**
    *   `skos:broader`: Tágabb fogalom (pl. Macska -> Állat).
    *   `skos:narrower`: Szűkebb fogalom (pl. Állat -> Macska).
    *   `skos:related`: Kapcsolódó fogalom (nem hierarchikus, pl. Macska -> Egérfogó).

> **Különbség az OWL-hez képest:** Az SKOS **nyelv-orientált** és lazább. Az OWL **logika-orientált** és szigorú (halmazelmélet).

### Összefoglalva: A "Hard" részek a vizsgára:

1.  **RDFS Domain/Range:** Tudd, hogy a domain az alany típusát, a range a tárgy típusát határozza meg (és ez következtetést von maga után).
2.  **Öröklődés:** `subClassOf` és `subPropertyOf`. (A tulajdonságok is öröklődhetnek!).
3.  **SKOS Címkék:** Tudd a különbséget a `prefLabel` (hivatalos név) és `altLabel` (szinonima) között.
4.  **Dublin Core:** Ismerd fel a prefixet (`dc:`) és tudd, hogy ez metaadat-szabvány.