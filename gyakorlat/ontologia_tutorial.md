Itt van egy **Ontológia (OWL) Gyorstalpaló**, kifejezetten a Protégé szoftverhez és az egyetemi feladatokhoz igazítva.

Az ontológia lényege: **Nem csak adatot tárolunk, hanem tanítjuk a gépet a világ szabályaira.**

---

### 1. A Három Alapépítőkő (A Protégé fülei)

Minden ontológia ebből a háromból áll:

1.  **Classes (Osztályok):** A fogalmak, halmazok.
    *   Pl.: `Ember`, `Kutya`, `Auto`.
    *   Hierarchiában vannak: `Kutya` -> `SubClass Of` -> `Emlős` -> `SubClass Of` -> `Allat`.
2.  **Properties (Tulajdonságok):** A kapcsolatok (az "igék").
    *   **Object Property:** Két egyedet köt össze. (Pl. `Jani` *szereti* `Marit`).
    *   **Data Property:** Egyedet köt össze egy adattal/számmal. (Pl. `Jani` *életkora* `25`).
3.  **Individuals (Egyedek):** A konkrét példányok.
    *   Pl.: `Bodri` (aki egy Kutya), `Rembrandt` (aki egy Festő).

---

### 2. A Logikai Kifejezések (Manchester Syntax)

A Protégé-ben angol szavakkal írjuk le a matekot. Ezeket kell fejből tudni:

| Kulcsszó | Jelentés | Példa | Magyarázat |
| :--- | :--- | :--- | :--- |
| **some** | Van legalább egy... (Egzisztenciális) | `hasChild some Person` | Van legalább egy gyereke, aki Ember. (A leggyakoribb kapcsoló). |
| **only** | Csak ilyen lehet... (Univerzális) | `eats only Plant` | Ha eszik valamit, az BIZTOSAN növény. (De az is lehet, hogy nem eszik semmit). |
| **and** | És (Metszet) | `Man and Teacher` | Férfi is ÉS tanár is. |
| **or** | Vagy (Unió) | `Cat or Dog` | Vagy macska, vagy kutya. |
| **not** | Nem (Komplementer) | `not Human` | Minden, ami NEM ember. |
| **value** | Konkrét egyed | `hasCountry value Hungary` | Pontosan Magyarországgal van kapcsolatban. |
| **min / max** | Darabszám | `hasLegs min 2` | Legalább 2 lába van. |

---

### 3. A Két Legfontosabb Doboz (Hol írjam a szabályt?)

Ez a leggyakoribb hiba a vizsgán! Hol kell megadni a szabályokat a "Class Description" részben?

#### A) `SubClass Of` (Részhalmaza) => "Leírás"
*   **Jelentése:** "Minden A-ra igaz, hogy..."
*   **Mire jó?** Általános szabályok megadására.
*   **Példa:** A `Zsiráf` osztályhoz írod: `SubClass Of: eszik only Növény`.
    *   *Ez azt jelenti:* Ha valami zsiráf, akkor biztosan növényevő.
    *   *De fordítva nem:* Ha valami növényevő, attól még nem biztos, hogy zsiráf (lehet nyúl is).

#### B) `Equivalent To` (Azzal egyenértékű) => "Definíció"
*   **Jelentése:** "Akkor és csak akkor A, ha..."
*   **Mire jó?** Új fogalmak definiálására, amiket a gépnek fel kell ismernie.
*   **Példa:** Létrehozod a `Szülő` osztályt. `Equivalent To: Ember and (van_gyereke some Ember)`.
    *   *Ez azt jelenti:* Ha valakiről kiderül, hogy van gyereke, a gép **automatikusan** átteszi a `Szülő` osztályba (sárgával jelzi).

---

### 4. Tulajdonságok Extrái (A pipálós rész)

Az `Object Properties` fülön a tulajdonságoknak speciális matek szabályai lehetnek:

*   **Inverse (Inverz):** Ha `A` *szülője* `B`-nek, akkor `B` *gyereke* `A`-nak.
    *   Beállítás: `hasParent` Inverse Of `hasChild`.
*   **Transitive (Tranzitív):** Ha A->B és B->C, akkor A->C.
    *   Pl.: `os_e` (őse). Ha apám őse, és apámnak őse a nagyapám, akkor a nagyapám nekem is ősöm.
*   **Functional (Funkcionális):** Maximum 1 lehet belőle.
    *   Pl.: `hasBiologicalMother`. Senkinek nem lehet 2 biológiai anyja.
*   **Domain (Értelmezési tartomány):** Kiből indul a nyíl?
    *   Pl.: `ugat` Domain: `Kutya`. (Ha valami ugat, a gép tudja, hogy az csak kutya lehet).
*   **Range (Értékkészlet):** Hova érkezik a nyíl?
    *   Pl.: `ugat` Range: `Hang`.

---

### 5. A "Disjoint" (Kizáró) fontossága

Az ontológiák alapból azt hiszik, minden lehetséges. A gép szerint simán lehetsz egyszerre `Kutya` és `Macska` is, amíg nem mondod neki, hogy **TILOS**.

*   **Hogyan?** Az osztály adatlapján a `Disjoint With` dobozban.
*   **Mire jó?** Hibaellenőrzésre. Ha véletlenül azt mondod Bodrira, hogy Kutya, de nyávog (ami macska tulajdonság), a `Reasoner` pirossal hibát dob (Inkonzisztencia).

---

### 6. A Reasoner (A Következtető)

Ez a "varázsgomb". A Protégé csak egy szerkesztő, a Reasoner (pl. HermiT, Pellet) az az agy, ami gondolkodik.

**Két dolgot csinál:**
1.  **Consistency Check (Piros):** Megnézi, van-e ellentmondás (pl. egy Disjoint szabály megsértése).
2.  **Inference (Sárga):** Új információkat vezet le.
    *   Pl. Te csak annyit vittél fel: `Józsi` (Ember), `Pistike` (Ember), `Józsi` -> `van_gyereke` -> `Pistike`.
    *   A Reasoner kitalálja (ha definiáltad a Szülőt): `Józsi` -> **Type: Szülő**.

---

### 7. Open World Assumption (Nyílt Világ Feltételezés)

Ez a legnehezebb elméleti buktató.
**Az SQL adatbázisban:** Ha valami nincs benne az adatbázisban, az nem létezik (Hamis).
**Az Ontológiában:** Ha valami nincs benne, az **MÉG lehet igaz**, csak nem tudunk róla.

**Példa:**
*   Szabály: A Vegetáriánus *csak* növényt eszik (`eats only Plant`).
*   Adat: `Boci` eszik `Fű`-vet.
*   Kérdés: `Boci` vegetáriánus?
*   **Gép válasza:** NEM TUDOM.
*   **Miért?** Mert attól, hogy tudjuk, hogy eszik füvet, még ehet titokban húst is, amiről nem szóltunk a gépnek.
*   **Megoldás (Closure Axiom):** A `Boci` egyednél az `eats` tulajdonságra jobb klikk -> "Add closure axiom". Ezzel lezárod: "Boci füvet eszik és SEMMI MÁST". Így már vegetáriánus lesz.

---

### Példa feladat megoldása (Gyorstalpaló stílusban)

**Feladat:** Definiáld a "Gazdag" embert (akinek több mint 1 milliója van).

1.  **Class:** Hozz létre: `Ember`, `Gazdag`.
2.  **Data Property:** Hozz létre: `vagyon` (Range: `integer`).
3.  **Definíció:**
    *   Kattints a `Gazdag` osztályra.
    *   `Equivalent To` mezőbe:
    *   `Ember and (vagyon some integer[> 1000000])`
4.  **Teszt:**
    *   Individuals fül -> Új egyed: `Bill_Gates`.
    *   Type: `Ember`.
    *   Data property assertions: `vagyon` -> `2000000000` (integer).
5.  **Futtatás:**
    *   Reasoner -> Start.
    *   `Bill_Gates` neve mellett megjelenik sárgával: `Type: Gazdag`.