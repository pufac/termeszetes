Ez a diasor az **OWL (Web Ontology Language)** szabványról szól, amely a Szemantikus Web "tortaszeletének" (layer cake) következő, logikai rétege az RDFS felett. Itt már nemcsak sémákat, hanem bonyolult logikai szabályokat és kényszereket is leírhatunk.

**Fő különbség az RDFS-hez képest:** Az RDFS egyszerű hierarchiákra (alosztály, altulajdonság) jó, az OWL viszont logikai következtetésekre és bonyolult definíciókra (pl. diszjunkt halmazok, számosság).

Íme a részletes, technikai magyarázatokkal bővített összefoglaló:

---

### 1. Miért kell az OWL az RDFS helyett?
*(6-8. és 11. oldal)*

Az RDFS korlátai (amit NEM tud leírni):
1.  **Számosság (Cardinality):** "Minden országnak *pontosan egy* fővárosa van." (RDFS-ben csak annyit tudsz mondani, hogy van fővárosa, de azt nem, hogy mennyi).
2.  **Diszjunkt osztályok:** "Egy város nem lehet egyszerre ország is." (Az RDFS-ben nincs "tiltás").
3.  **Logikai műveletek:** Unió, metszet, komplemens.
4.  **Tulajdonságok jellemzése:** Tranzitivitás, inverz, szimmetria.

Az OWL (Web Ontology Language) ezeket a hiányosságokat pótolja.

---

### 2. OWL Alapelemek
*(10. és 13. oldal)*

Az OWL kiterjeszti az RDF szótárát.
*   **`owl:Class`**: Osztály definíció. (Fontos: Az OWL-ben van egy fő osztály, az `owl:Thing`, minden egyed ennek a példánya).
*   **`owl:ObjectProperty`**: Két egyed (erőforrás) közötti kapcsolat (pl. `X isWifeOf Y`).
*   **`owl:DatatypeProperty`**: Egyed és literális közötti kapcsolat (pl. `X hasAge 30`). *Ez fontos technikai különbség!*

---

### 3. Logikai Relációk és Kényszerek (A "Hard" rész)
*(14. és 16-17. oldal)* – **Ezeket definíció szinten kell tudni a vizsgára!**

**A) Ekvivalencia és Különbözőség:**
*   **`owl:sameAs`**: Két különböző URI ugyanazt a valós dolgot jelöli. (Pl. `http://dbpedia.org/Budapest` sameAs `http://freebase.com/Bp`). Ez az adatintegráció alapja.
*   **`owl:differentFrom`**: Két dolog biztosan nem ugyanaz. (Pl. `Budapest differentFrom Debrecen`).
*   **`owl:disjointWith`**: Osztályokra vonatkozik. (Pl. `Férfi disjointWith Nő`). Ha valaki Férfi, akkor a gép tudja, hogy nem lehet Nő (és fordítva).

**B) Tulajdonságok jellemzése (Vizsgán gyakran kérdezik!):**
*   **`owl:inverseOf`**: Inverz kapcsolat. Ha `A szülője B`-nek, akkor `B gyereke A`-nak.
*   **`owl:TransitiveProperty`**: Ha `A része B`-nek és `B része C`-nek, akkor `A része C`-nek.
*   **`owl:SymmetricProperty`**: Ha `A testvére B`-nek, akkor `B testvére A`-nak.
*   **`owl:FunctionalProperty`**: Egy alanyhoz csak **egy** érték tartozhat. (Pl. `hasBirthMother`). Ha az adatbázisban két anya van megadva, a gép kikövetkezteti, hogy a kettő ugyanaz a személy (`sameAs`).
*   **`owl:InverseFunctionalProperty`**: Egy érték csak **egy** alanyhoz tartozhat. (Pl. `hasSocialSecurityNumber` - TAJ szám). Ha két embernek ugyanaz a TAJ száma, akkor ők ugyanaz a személy. (Lásd 16. oldal példája az email címmel!).

---

### 4. Osztályok Konstruálása (Halmazelmélet)
*(13. és 29-32. oldal)*

Az OWL-ben nemcsak megnevezünk osztályokat, hanem "építünk" is őket logikai szabályokkal.

**Halmazműveletek:**
*   **`owl:intersectionOf` (Metszet):** `A ÉS B`. (Pl. "Anya" = "Nő" metszet "Szülő").
*   **`owl:unionOf` (Unió):** `A VAGY B`.
*   **`owl:complementOf` (Komplemens):** `NEM A`.

**Megszorítások (Restrictions) – *Mélyebb tartalom*:**
Ez a legbonyolultabb rész. Egy osztályt úgy definiálunk, hogy megmondjuk, milyen tulajdonságokkal *kell* rendelkeznie.
*(Lásd 31-32. oldal kódját!)*
*   **`owl:onProperty`**: Melyik tulajdonságot vizsgáljuk? (Pl. `shavedBy` - ki borotválja).
*   **`owl:hasSelf`**: Önmagára vonatkozó reflexív kapcsolat. (Pl. "Aki magát borotválja").
*   **`owl:allValuesFrom`**: Csak adott típusú értékei lehetnek.
*   **`owl:someValuesFrom`**: Legalább egy adott típusú értéke van.
*   **`owl:cardinality`**: Pontos számosság (pl. 1).

---

### 5. Példa: A Russell-paradoxon
*(28-34. oldal)*

Ez a példa azt demonstrálja, hogy az OWL (pontosabban az OWL Full) olyan kifejező, hogy ellentmondásokba (paradoxonokba) ütközhetünk, ami miatt a gép "lefagy" vagy hibát jelez.

*   **A sztori:** "A borbély mindenkit megborotvál, aki nem borotválja magát. Ki borotválja a borbélyt?"
    *   Ha magát borotválja -> Akkor nem borotválhatja magát (a szabály miatt).
    *   Ha nem borotválja magát -> Akkor meg kell borotválnia magát (a szabály miatt).
*   **OWL Megoldás:** Definiálunk egy osztályt (`PeopleWhoDoNotShaveThemselves` - 32. oldal), és megpróbáljuk besorolni a Borbélyt.
*   **Eredmény:** A következtetőgép (Reasoner) "Inconsistent ontology" (Inkonzisztens ontológia) hibát dob (33. oldal képernyőképe). Ez azt jelenti, hogy logikai ellentmondást talált.

> **Tanulság a vizsgára:** Az OWL képes felismerni a logikai hibákat az adatmodellben! Ez az egyik fő ereje az RDFS-sel szemben (ami mindent "lenyel").

### Összefoglalva: Mit kell "magolni"?

1.  **OWL Tulajdonságtípusok:** ObjectProperty (egyed-egyed) vs. DatatypeProperty (egyed-adat).
2.  **Logikai jellemzők:** Tudd, mit jelent a Functional, InverseFunctional, Transitive, Symmetric. (Példákkal!).
3.  **Ekvivalencia:** sameAs, differentFrom, disjointWith.
4.  **Russell-paradoxon:** Értsd, hogy ez egy inkonzisztencia példa, amit az OWL Reasoner detektál.
5.  **Nyílt Világ (OWA):** Itt is érvényes! Ha nem mondjuk, hogy két dolog különböző (`differentFrom`), a gép feltételezheti, hogy azonosak, ha a logika úgy kívánja.