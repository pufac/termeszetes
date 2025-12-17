Rendben, folytatjuk a technikai mélyfúrást. Ez a diasor a **SWRL** (Semantic Web Rule Language) és a **SQWRL** (Semantic Query-Enhanced Web Rule Language) technológiákról szól.

Ez a rész azért kritikus, mert az OWL (amit az előzőben vettünk) bizonyos dolgokra nem képes (pl. matematikai műveletek, bonyolultabb "ha-akkor" szabályok). A SWRL hidalja át ezt a szakadékot.

Itt a részletes, vizsgaorientált összefoglaló:

---

### 1. Mi az a SWRL és hova illeszkedik?
*(1-6. oldal)*

*   **Helye a tortában:** Az OWL réteg *felett* helyezkedik el. (Lásd: 3. oldal stack ábra).
*   **Definíció:** Az OWL fogalmak (osztályok, tulajdonságok) és a **Horn-klózok** (Horn-szerű szabályok) kombinációja.
*   **Célja:** Olyan szabályokat leírni, amiket sima OWL-lel nem lehetne (pl. "Ha valakinek a testvére férfi, akkor az a fivére").

---

### 2. A SWRL Szabályok Szerkezete (Szintaxis)
*(8-11. oldal)* – **Technikai alap, tudni kell írni ilyet!**

Egy SWRL szabály két részből áll:
**Előtag (Body/Antecedent) $\to$ Utótag (Head/Consequent)**

*   **Logika:** Ha az Előtag minden állítása igaz, akkor az Utótag is igaz legyen. (Implikáció).
*   **Atomok:** A szabály építőkockái.
    *   `Osztály(?x)`: x egy példány.
    *   `Tulajdonság(?x, ?y)`: x és y között kapcsolat van.
    *   `sameAs(?x, ?y)`, `differentFrom(?x, ?y)`.

**Példák (Ezeket érteni kell):**
1.  **Tulajdonságláncolás (Amit a sima OWL nehezen tud):**
    *   `hasParent(?x, ?y) ^ hasBrother(?y, ?z) -> hasUncle(?x, ?z)`
    *   *Magyarázat:* Ha x-nek y a szülője, ÉS y-nak z a fivére, AKKOR x-nek z a nagybátyja.
2.  **Konkrét példányokkal:**
    *   `Person(Fred) ^ hasSibling(Fred, ?s) ^ Man(?s) -> hasBrother(Fred, ?s)`
    *   *Magyarázat:* Ha Frednek van egy ?s testvére, és ?s férfi, akkor ?s Fred fivére.

---

### 3. Beépített függvények (Built-ins) – A SWRL ereje
*(12-16. oldal)*

Az OWL nem tud számolni. A SWRL viszont igen, a `swrlb` (SWRL Built-ins) könyvtár segítségével.

*   **Szintaxis:** `swrlb:függvénynév(?kimenet, ?bemenet1, ?bemenet2...)`
    *   *Fontos:* A SWRL-ben nem úgy írjuk, hogy `x = 5 + 2`, hanem logikai predikátumként: `swrlb:add(?x, 5, 2)`.

**Gyakori beépítettek (Vizsgára!):**
*   **Matek:** `swrlb:greaterThan(?age, 17)` (Kor > 17), `swrlb:multiply`, `swrlb:add`.
*   **String:** `swrlb:startsWith(?szoveg, "+36")`.
*   **Idő:** `temporal:duration`.

**Példa (14. oldal):**
`hasSalaryInPounds(?p, ?pounds) ^ swrlb:multiply(?dollars, ?pounds, 2.0) -> hasSalaryInDollars(?p, ?dollars)`
*   *Jelentés:* Ha van fizetés fontban, szorozd meg 2-vel, és az eredményt rendeld hozzá dollárban.

---

### 4. A SWRL Korlátai és a "Monotonitás" (A legnehezebb elméleti rész!)
*(20-26. oldal)* – **Ez a legvalószínűbb buktató kérdés!**

A SWRL (az OWL-hez hasonlóan) **Monoton**. Ez azt jelenti, hogy csak **új** információt adhatunk hozzá, meglévőt nem törölhetünk és nem másíthatunk meg. Ebből fakadnak a korlátok:

1.  **Nincs negáció (Nem támogatott a `NOT`):**
    *   *Hibás szabály:* `Person(?p) ^ NOT hasCar(?p, ?c) -> Gyalogos(?p)`
    *   *Miért?* A **Nyílt Világ Feltételezés (OWA)** miatt! Attól, hogy az adatbázisban nem szerepel autó, még lehet neki, csak nem tudunk róla. Ezért nem következtethetjük ki, hogy gyalogos.

2.  **Nincs visszavonás/módosítás (Non-retraction):**
    *   *Hibás szabály:* `hasAge(?p, ?age) ^ swrlb:add(?new, ?age, 1) -> hasAge(?p, ?new)`
    *   *Miért?* Ez végtelen ciklust okoz. Ha 20 éves, a szabály hozzáadja, hogy 21. De akkor már 21 is, újra lefut a szabály -> 22 -> 23... A régi érték nem tűnik el, csak gyűlnek az életkorok.

3.  **Nincs számlálás (Counting):**
    *   Nem mondhatod azt SWRL-ben, hogy "Ha pontosan 3 gyereke van...". (Erre az SQWRL való, lásd lejjebb).

---

### 5. Technikai megvalósítás: SWRLTab és a Híd (Bridge)
*(27-37. oldal)*

Hogyan fut a SWRL?
*   Nem önállóan. Kell hozzá egy **Ontológia szerkesztő** (Protégé) és egy **Szabálymotor** (Rule Engine, pl. **Jess**).
*   **SWRL Bridge:** Ez a szoftverkomponens hidalja át a szakadékot.
    1.  Kiveszi az adatokat az OWL-ből.
    2.  Átkonvertálja a Jess formátumára.
    3.  A Jess lefuttatja a szabályokat.
    4.  A következtetett új tényeket a Bridge visszarakja az OWL ontológiába.

---

### 6. SQWRL (Semantic Query-Enhanced Web Rule Language)
*(45-54. oldal)*

Mivel a SWRL-nek sok a korlátja (nem tud számolni, nem tudja listázni az adatokat), létrehozták az SQWRL-t (ejtsd: "szkverrel" vagy "squirrel" - mókus).

*   **Lényege:** Ez egy **Lekérdező Nyelv** (mint az SQL vagy SPARQL), de SWRL szintaxist használ.
*   **Kulcsszó:** `sqwrl:select(...)`. Ha ezt látod a nyíl (`->`) jobb oldalán, akkor az nem szabály, hanem lekérdezés!

**Miben tud többet a SWRL-nél? (Vizsgakérdés!)**
Az SQWRL (mivel csak lekérdez, nem ír vissza az ontológiába) **megszegi a monotonitást**, ezért képes olyanra, amire a SWRL nem:
*   **Számlálás:** `sqwrl:count(?c)` (Megszámolja az autókat).
*   **Aggregálás:** `sqwrl:avg(?age)` (Átlagéletkor), `min`, `max`, `sum`.
*   **Rendezés:** `sqwrl:orderBy(?age)`.
*   **Zárt világot feltételezhet:** Képes megszámolni a *jelenleg ismert* adatokat.

### Összefoglalva: Mit kell "bemagolni"?

1.  **SWRL Szintaxis:** `Atom ^ Atom -> Atom`. (A `^` jelöli az ÉS kapcsolatot).
2.  **A "Monotonitás" átka:** Értsd meg, miért **nem lehet** negálni (OWA miatt) és miért **nem lehet** értéket növelni (`x = x + 1` tilos).
3.  **SWRL vs. SQWRL:**
    *   **SWRL:** Következtet (új tényt ír be), monoton, szigorú.
    *   **SQWRL:** Lekérdez (táblázatot ad vissza), tud számolni/átlagolni/rendezni (`select`, `count`, `avg`).
4.  **Beépített függvények:** `swrlb:multiply`, `swrlb:greaterThan` – tudd, hogy ezek léteznek és a matekhoz kellenek.
5.  **Technológia:** Protégé + SWRLTab + Jess motor.