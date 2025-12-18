Ez az útmutató az **RDF Alapok és SPARQL Lekérdezések** témakörét dolgozza fel, kifejezetten a Szépművészeti Múzeum adataira és az **ECRM (Erlangen CRM)** ontológiára építve.

Ez a gyakorlat az alapozás a következő (integrációs) feladathoz. Itt tanulod meg, hogyan épül fel a gráf, és hogyan kell "sétálni" benne.

---

# Általános Útmutató: Múzeumi Adatok (ECRM) és SPARQL

## 0. Előkészületek és a legfontosabb szabály
Mielőtt bármilyen lekérdezést írnál, tisztáznod kell a névtereket (Prefix). A Szépművészeti Múzeum adatai az **Erlangen CRM** (ECRM) ontológiát használják.

**Ezt a sort minden lekérdezés elejére másold be:**
```sparql
PREFIX ecrm: <http://erlangen-crm.org/current/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
```
*(Ha ezt lehagyod, a rendszer nem fogja érteni, mi az az `E21_Person` vagy `P108_has_produced`.)*

---

## 1. Entitások és Típusok (1. és 4. feladat)
Az RDF adatbázisban mindennek van egy típusa (`rdf:type` vagy röviden `a`).

*   **Feladat:** Keresd meg az alkotókat!
*   **Logika:** Meg kell találnod, mi az "Ember" vagy "Színész" az ontológiában.
    *   ECRM-ben: **`ecrm:E21_Person`** (Személy) vagy **`ecrm:E39_Actor`** (Cselekvő).

**SPARQL Minta:**
```sparql
SELECT ?alkoto
WHERE {
  ?alkoto rdf:type ecrm:E21_Person .
}
LIMIT 10
```

---

## 2. Az ECRM Ontológia Szíve: Esemény alapú modell (2. és 6. feladat)
Ez a legfontosabb elméleti rész, amit meg kell értened! A hagyományos adatbázisokkal ellentétben itt **nincs közvetlen kapcsolat** a Festő és a Kép között (pl. *NEM létezik `Picasso -> festette -> Kép` kapcsolat*).

Helyette **Események** vannak:
1.  Van a **Festő** (`E39_Actor`).
2.  Van egy **Alkotási Folyamat** (`E12_Production`), ami egy esemény.
3.  Van a **Kész Mű** (`E22_Man-Made_Object`).

**A láncolat:**
> **Festő** --(*részt vett benne*)--> **Alkotás Eseménye** --(*létrehozta*)--> **Műtárgy**

**A tulajdonságok kódjai:**
*   `ecrm:P14_performed` (vagy `P11_had_participant`): A személy részt vett az eseményben.
*   `ecrm:P108_has_produced`: Az esemény létrehozta a tárgyat.

**SPARQL Minta (Láncolás):**
```sparql
SELECT ?alkoto ?alkotasi_folyamat ?mu
WHERE {
  # 1. Alkotó -> Folyamat
  ?alkoto ecrm:P14_performed ?alkotasi_folyamat .
  
  # 2. Folyamat -> Mű
  ?alkotasi_folyamat ecrm:P108_has_produced ?mu .
}
```

---

## 3. Komplex Adatok: Méretek (3. feladat)
Miért nem csak egy szám a méret (pl. "50")? Mert nem tudnánk, hogy cm, mm, vagy inch, és azt sem, hogy ez a szélessége vagy a magassága.

**A struktúra:**
`Műtárgy` --(van mérete)--> `Dimenzió Entitás`
A `Dimenzió Entitás`-nak három adata van:
1.  **Érték** (`P90_has_value`): pl. 50.
2.  **Mértékegység** (`P91_has_unit`): pl. cm.
3.  **Típus** (`P2_has_type`): pl. szélesség.

**Tanulság:** Ha egy adatbázisban nem sima számot látsz, hanem egy újabb csomópontot, az általában a pontosság miatt van (mértékegység, időzóna, pénznem stb.).

---

## 4. Szűrés Szövegre (5. feladat)
Hogyan keressünk meg valakit név alapján (pl. "Giovanni")? A név az ECRM-ben nem közvetlenül az emberen lóg, hanem egy **Név Entitáson**.

**Láncolat:**
`Ember` --(`P131_is_identified_by`)--> `Név Entitás` --(`P3_has_note`)--> "Giovanni..." (String)

**SPARQL Minta (Regex):**
```sparql
SELECT ?alkoto ?nev
WHERE {
  ?alkoto a ecrm:E21_Person .
  
  # Név kikeresése
  ?alkoto ecrm:P131_is_identified_by ?nev_entitas .
  ?nev_entitas ecrm:P3_has_note ?nev .
  
  # Szűrés (i = kis/nagybetű nem számít)
  FILTER regex(?nev, "Giovanni", "i")
}
```

---

## 5. Komplex Keresés: Rembrandt Rézkarcai (7. feladat)
Itt mindent össze kell rakni, amit eddig tanultunk.

**A keresés feltételei:**
1.  Az Alkotó neve legyen "Rembrandt".
2.  Az alkotási folyamat technikája legyen "rézkarc".
3.  Kellenek a művek.

**Logikai lépések:**
1.  Keressük meg az Embert, akinek a neve "Rembrandt".
2.  Keressük meg az Alkotási Folyamatot, amiben részt vett.
3.  Nézzük meg a Folyamat technikáját (`P32_used_general_technique`).
4.  Szűrjünk a technika nevére ("rézkarc").
5.  Kérjük el a Folyamat által létrehozott Műtárgyat.

**SPARQL Megoldás:**
```sparql
SELECT ?mu ?cim
WHERE {
  # --- 1. Rembrandt megtalálása ---
  ?alkoto a ecrm:E21_Person .
  ?alkoto ecrm:P131_is_identified_by ?a_nev .
  ?a_nev ecrm:P3_has_note ?alkoto_neve .
  FILTER regex(?alkoto_neve, "Rembrandt", "i")

  # --- 2. Kapcsolás: Alkotó -> Folyamat -> Mű ---
  ?alkoto ecrm:P14_performed ?folyamat .
  ?folyamat ecrm:P108_has_produced ?mu .

  # --- 3. Technika szűrése ---
  ?folyamat ecrm:P32_used_general_technique ?technika .
  ?technika ecrm:P3_has_note ?technika_neve .
  FILTER regex(?technika_neve, "rézkarc", "i")

  # --- 4. Cím lekérése (opcionális, de szebb) ---
  ?mu ecrm:P102_has_title ?cim_entitas .
  ?cim_entitas ecrm:P3_has_note ?cim .
}
```

---

## 6. Sémák és Gráfok (8. feladat)
Ha le kell rajzolnod az adatbázis sémáját, a csomópontok az `E`-betűs osztályok, a nyilak a `P`-betűs tulajdonságok lesznek.

**Egyszerűsített gráf rajz:**

```text
[E21 Person] 
      |
      | P14 performed
      v
[E12 Production] -- P32 used technique --> [E55 Type (pl. rézkarc)]
      |
      | P108 has produced
      v
[E22 Man-Made Object]
      |
      | P43 has dimension
      v
[E54 Dimension] -- P90 has value --> "50"
```

**Összefoglalva:**
Az ilyen feladatoknál a kulcs mindig a **"közvetítő esemény"** (Production) megtalálása. Ha megpróbálod közvetlenül összekötni az embert a tárggyal, nem fogsz találatot kapni. Mindig gondolkodj így: *Ki csinálta? -> Milyen eseményen? -> Mit hozott létre?*


Ez az útmutató összefoglalja az RDF adatintegrációs feladatok megoldásának logikáját, a megadott PDF anyaga alapján. A cél az, hogy megértsd, hogyan kell összekapcsolni egy **lokális adatbázist** (pl. Szépművészeti Múzeum) egy **külső adatbázissal** (pl. DBpedia/Wikipédia) SPARQL segítségével.

---

# Általános Útmutató RDF Adatintegrációs Feladatokhoz

## 1. Az Alapkoncepció: Linked Data (Kapcsolt Adatok)
A feladatok lényege, hogy van két külön gráfunk:
1.  **Belső gráf (Lokális):** A mi szerverünkön van (pl. `http://data.szepmuveszeti.hu/...`).
2.  **Külső gráf (Publikus):** Máshol van (pl. `http://dbpedia.org/...`), de ismerjük az URI-kat.

A két világot a **híd** köti össze, ami leggyakrabban az `owl:sameAs` reláció.

---

## 2. A "Híd" felderítése (owl:sameAs)
Mielőtt bonyolult lekérdezést írnál, mindig meg kell nézni, hogyan hivatkozunk a külvilágra.

**Alapfeladat:** "Hány hivatkozás van a DBpedia-ra?"

**Megoldási minta:**
1.  Keressük meg az összes `owl:sameAs` kapcsolatot.
2.  A célpont (object) szöveges formájában (`str(?id)`) keressünk rá a "dbpedia" szóra reguláris kifejezéssel.

```sparql
PREFIX owl: <http://www.w3.org/2002/07/owl#>

SELECT (COUNT(?s) as ?count)
WHERE {
    ?s owl:sameAs ?kulso_id .
    FILTER regex(str(?kulso_id), "dbpedia", "i")
}
```

---

## 3. Adattisztítás és Csoportosítás (Namespace elemzés)
Gyakori feladat, hogy statisztikát kell készíteni arról, milyen külső forrásokhoz kapcsolódunk. Ehhez le kell vágni az URI végét (az egyedi azonosítót), hogy csak a domaint (namespace-t) kapjuk meg.

**Technika:** `BIND` és `replace` használata.
*   A `replace` reguláris kifejezéssel cserél szöveget.
*   A `/[^/]*$` minta jelentése: "az utolsó perjel utáni összes karakter a szöveg végéig". Ezt cseréljük üresre (""), így megkapjuk a base URL-t.

**Megoldási minta:**
```sparql
SELECT ?forras (COUNT(?forras) as ?darab)
WHERE {
    ?lokal_id owl:sameAs ?kulso_id .
    # Levágjuk az ID végét, hogy megkapjuk az adatbázis nevét
    BIND(replace(str(?kulso_id), "/[^/]*$", "") AS ?forras)
}
GROUP BY ?forras
```

---

## 4. Szűrés Dátumokra (Időbeli lekérdezések)
Ha történelmi adatokat (születés, halálozás) kell szűrni, figyelni kell az adattípusra (`xsd:date`).

**Szabály:** A dátumokat "ÉÉÉÉ-HH-NN" formátumban kell megadni, és meg kell mondani a rendszernek, hogy ez dátum típus.

**Megoldási minta (pl. 17. század első fele: 1600-1650):**
```sparql
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?muvesz ?szuletesi_datum
WHERE {
    ?muvesz dbo:birthDate ?szuletesi_datum .
    FILTER (?szuletesi_datum >= "1600-01-01"^^xsd:date && 
            ?szuletesi_datum <= "1650-12-31"^^xsd:date)
}
```

---

## 5. A Legfontosabb Minta: Kereszttáblás Lekérdezés (Federation)
Ez a leggyakoribb és legkomplexebb feladattípus.
**A szituáció:** Tudod egy híres ember DBpedia linkjét (pl. Rembrandt), és kíváncsi vagy a *helyi* múzeumban lévő képeire.

**A láncolat logikája:**
1.  **Külső pont:** Indulj ki a fix DBpedia URI-ból (`<http://dbpedia.org/resource/Rembrandt>`).
2.  **Híd:** Keresd meg azt a *helyi* entitást, ami `owl:sameAs` kapcsolattal mutat erre.
3.  **Helyi gráf bejárása:** A helyi entitástól (Alkotó) lépkedj el a Műalkotás címéig.

**Az ECRM (Múzeumi szabvány) útvonala a feladat alapján:**
`Alkotó` -> (P11 had participant) -> `Alkotási Folyamat` -> (P12i was present at) -> `Műalkotás` -> (label) -> `Cím`

**Megoldási minta:**
```sparql
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX ecrm: <http://erlangen-crm.org/current/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?kep_cime
WHERE {
    # 1. HÍD: Megkeressük a helyi alkotót a DBpedia ID alapján
    ?helyi_alkoto owl:sameAs <http://dbpedia.org/resource/Rembrandt> .

    # 2. HELYI GRÁF: Alkotó -> Alkotási folyamat (Production)
    ?alkotasi_folyamat ecrm:P11_had_participant ?helyi_alkoto .

    # 3. HELYI GRÁF: Alkotási folyamat -> Műalkotás (Image/Thing)
    ?kep ecrm:P12i_was_present_at ?alkotasi_folyamat .

    # 4. Cím lekérése
    ?kep rdfs:label ?kep_cime .
}
```
*(Megjegyzés: Az ECRM szabványban néha más property-ket használnak, pl. P14_performed és P108_has_produced, de mindig a feladatban megadott property-ket használd! Itt a P11 és P12i volt megadva.)*

---

## 6. Logikai Szűrés Külső Adat Alapján (Hatások, Tanítványok)
Ez a feladat azt kéri, hogy olyanokat keressünk a helyi adatbázisban, akiknek van valamilyen viszonyuk (pl. tanítványa volt) egy híres emberhez a *külső* adatbázis szerint.

**Logika:**
1.  Keressük meg a DBpedia-ban azokat az embereket, akikre igaz a feltétel (pl. `dbo:influencedBy` Rembrandt).
2.  Nézzük meg, hogy ezek közül kinek van párja a helyi adatbázisban (`owl:sameAs`).
3.  Ha van helyi párja, kérdezzük le a nevét/képeit.

**Megoldási minta:**
```sparql
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?helyi_muvesz_neve
WHERE {
    # 1. KÜLSŐ LOGIKA: Keressük a Rembrand által befolyásoltakat DBpedia-ban
    ?kulfoldi_muvesz dbo:influencedBy <http://dbpedia.org/resource/Rembrandt> .

    # 2. HÍD: Van-e ennek a külföldi művésznek helyi párja?
    ?helyi_muvesz owl:sameAs ?kulfoldi_muvesz .

    # 3. HELYI ADAT: Ha van, mi a neve? (vagy mik a képei, lásd 5. pont)
    ?helyi_muvesz rdfs:label ?helyi_muvesz_neve .
}
```

## Összefoglaló Checklist a feladatokhoz:
1.  **Prefixek:** Mindig definiáld az `owl`, `rdfs`, `ecrm` (vagy helyi múzeumi), `dbo` (DBpedia) prefixeket.
2.  **Irányok:** Figyelj, hogy az `owl:sameAs` melyik irányba mutat (Helyi -> Külső).
3.  **Típusosság:** Dátumoknál használj `^^xsd:date`-et.
4.  **String műveletek:** Ha URI-t kell elemezni, használd a `str()`, `regex()` és `replace()` függvényeket.