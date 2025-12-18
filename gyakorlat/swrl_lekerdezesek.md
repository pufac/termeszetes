Ez a feladatsor tipikus **OWL Ontológia modellezési** feladat (Description Logic), amit Protégé-ben szokás megoldani. Bár a kérdésedben SWRL-t említettél, ezek a feladatok 90%-ban **szabványos OWL osztálykifejezésekkel (Class Expressions)** és **megszorításokkal (Restrictions)** oldhatók meg a legelegánsabban. SWRL szabályokra csak nagyon speciális esetekben van szükség.

Itt egy **gyorstalpaló tutorial**, hogyan oldd meg ezt a három feladatot lépésről lépésre Protégé-ben.

---

### Előkészületek (Minden feladat előtt)
1.  **Protégé indítása.**
2.  **Reasoner indítása:** `Reasoner` menü -> `HermiT` (vagy Pellet) kiválasztása, majd `Start reasoner`. Ha sárga változásokat látsz, akkor működik a következtetés.

---

### 1. Feladat: Zsiráfok és Vegetáriánusok
**Cél:** Levezetni, hogy a Zsiráf vegetáriánus, pusztán a logikai szabályokból.

#### Lépések:
1.  **Osztályok létrehozása (Classes tab):**
    *   Hozd létre ezeket: `Allat`, `Noveny`, `Resz`, `Level`, `Fa`, `Zsiraf`, `Vegetarianus`.
    *   **Hierarchia:**
        *   `Level` legyen a `Resz` alosztálya (`SubClass Of`).
        *   `Fa` legyen a `Noveny` alosztálya.

2.  **Diszjunkt (Kizáró) halmazok beállítása (NAGYON FONTOS!):**
    *   A gép nem tudja magától, hogy ami Növény, az nem lehet Állat.
    *   Kattints az `Allat` osztályra. A jobb oldalon a `Disjoint With` dobozba add hozzá: `Noveny`.
    *   Ugyanígy: `Allat` Disjoint With `Resz`. (Hogy egy állat része ne lehessen maga az állat a feladat szerint).

3.  **Objektum tulajdonságok (Object Properties tab):**
    *   `eszik` (Domain: Allat).
    *   `resze` (Transitive property-t bepipálhatod, de itt elég simán is).

4.  **A szabályok definiálása (Classes tab):**

    *   **A levelek a fák részei:**
        *   Válaszd ki a `Level`-t.
        *   `SubClass Of` hozzáadása: `resze some Fa`.

    *   **A zsiráfok csak leveleket esznek:**
        *   Válaszd ki a `Zsiraf`-ot.
        *   `SubClass Of` hozzáadása: `eszik only Level` (fontos: **only**, azaz *csak*).

    *   **Definiáljuk a Vegetáriánust (A trükk):**
        *   A feladat szerint: "nem esznek állatot vagy ezeknek részeit".
        *   Válaszd ki a `Vegetarianus`-t.
        *   Mivel ez egy *definíció*, az **Equivalent To** dobozt használd!
        *   Írd be: `not (eszik some (Allat or (resze some Allat)))`.
        *   *Magyarázat:* Aki NEM (eszik VALAMI (Állatot VAGY Állat részét)).

5.  **Következtetés (A varázslat):**
    *   Szinkronizáld a Reasonert (`Reasoner` -> `Synchronize`).
    *   Ha mindent jól csináltál, a `Zsiraf` osztály sárgával meg fog jelenni a `Vegetarianus` osztály alatt (vagy az adatlapján látod, hogy `SubClass Of Vegetarianus`).
    *   *Miért?* Mert a Zsiráf csak Levelet eszik -> A Levél Növény része -> A Növény nem Állat -> Tehát a Zsiráf nem eszik Állatot -> Tehát Vegetáriánus.

#### b.) rész: Példányok (Individuals)
*   **Individuals by class** fül.
*   Hozz létre: `Zsiraf_Peldany` (típus: Zsiraf), `Sonka` (típus: Allat vagy Allat_Resze), `Palma` (típus: Fa).
*   A Reasoner azonnal jelezni fogja, ha megpróbálod beállítani, hogy a `Zsiraf_Peldany` `eszik` `Sonka`-t. (Piros hibaüzenet = Inkonzisztencia, tehát a zsiráf nem eheti meg a sonkát).

---

### 2. Feladat: János és a kisállatok
**Cél:** Megszámolni a háziállatokat. Erre az OWL "Cardinality Restriction" való.

#### Lépések:
1.  **Osztályok:** `Allat`, `Kutya`, `Macska`, `Horcsog`, `Haziallat`, `Ember`, `HaziallatKedvelo`.
    *   Állítsd be: `Kutya`, `Macska`, `Horcsog` mind `SubClass Of Haziallat`.

2.  **Tulajdonság:** `van_haziakllata` (Object Property).

3.  **Egyedek (Individuals):**
    *   Hozz létre: `Janos` (Ember), `Bodri` (Kutya), `Samu` (Horcsog), `Cirmos` (Macska).
    *   **Fontos:** A Protégé alapból nem tudja, hogy Bodri és Samu két *külön* állat (lehetne Bodri a hörcsög beceneve is).
    *   Ki kell jelölni mindet, és a `Different Individuals` dobozban hozzáadni a többieket (vagy ctrl+klikk kijelölés után jobb gomb -> *Different individuals*).

4.  **Kapcsolatok:**
    *   `Janos` adatlapján (Object property assertions):
        *   `van_haziakllata` `Bodri`
        *   `van_haziakllata` `Samu`
        *   `van_haziakllata` `Cirmos`

5.  **A "Háziállat kedvelő" definíciója:**
    *   Ez a feladat lényege: "akinek van legalább három".
    *   Válaszd ki a `HaziallatKedvelo` osztályt.
    *   **Equivalent To**: `van_haziakllata min 3 Haziallat`.
    *   *(Magyarul: Akinek a `van_haziakllata` kapcsolata minimum 3 különböző, `Haziallat` típusú dologra mutat).*

6.  **Eredmény:**
    *   Indítsd el a Reasonert.
    *   Nézd meg `Janos` adatlapját. Sárgával meg fog jelenni: **Type: HaziallatKedvelo**.

---

### 3. Feladat: Nagyi és a macskák
**Cél:** A "csak" (only) megszorítás használata és az inkonzisztencia (ellentmondás) demonstrálása.

#### Lépések:
1.  **Osztályok:** `Ember`, `No`, `IdosHolgy`, `Allat`, `Macska`, `Kutya`.
    *   `Macska` és `Kutya` legyenek **Disjoint** (kizáróak)! (Egy állat nem lehet egyszerre kutya és macska).
    *   `No` `SubClass Of Ember`.

2.  **Tulajdonság:** `tart` (Object Property).

3.  **Definíciók:**
    *   **Idős nőnemű emberek:** A feladat szerint ők az `IdosHolgy` osztály.
    *   Szabály: "csak macskákat tartanak".
    *   `IdosHolgy` -> `SubClass Of`: `tart only Macska`.
    *   *(Ez az `Universal Restriction`. Azt jelenti: Ha tart valamit, az BIZTOSAN macska).*

4.  **Egyedek és b.) rész:**
    *   Létrehoz: `Nagyi` (típus: IdosHolgy).
    *   Létrehoz: `Cirmos` (ne adj neki típust!).
    *   Kapcsolat: `Nagyi` `tart` `Cirmos`.
    *   **Következtetés:** Futtasd a Reasonert. Nézd meg `Cirmos`-t. A gép sárgával beírja: **Type: Macska**.
    *   *Miért?* Mert Nagyi csak macskát tarthat. Mivel tartja Cirmost, Cirmosnak macskának kell lennie.

5.  **c.) rész: Inkonzisztencia (Bodri):**
    *   Létrehoz: `Bodri` (típus: **Kutya**).
    *   Próbáld meg felvenni: `Nagyi` `tart` `Bodri`.
    *   Indítsd a Reasonert.
    *   **Hiba!** (Piros felkiáltójel). Az ontológia inkonzisztens.
    *   *Ok:* Nagyi csak macskát tarthat. Bodri egy kutya. A Kutya és a Macska osztályok "Disjoint"-ok (kizárják egymást). Ezért Bodri nem lehet Nagyi állata.

### Összefoglaló Puskázó (Syntax)

A Protégé "Manchester Syntax"-ot használ az egyenletekhez:

*   **ÉS:** `and` (pl. `Ember and No`)
*   **VAGY:** `or`
*   **NEM:** `not`
*   **Van legalább egy ilyen kapcsolata:** `some` (pl. `eszik some Level` - egzisztenciális)
*   **CSAK ilyen kapcsolata lehet:** `only` (pl. `eszik only Level` - univerzális)
*   **Darabszám (Számosság):** `min`, `max`, `exactly` (pl. `min 3 Haziallat`).
*   **Definíció (Akkor és csak akkor):** `Equivalent To` (Ezzel definiálunk új fogalmakat).
*   **Szabály (Minden esetre igaz):** `SubClass Of` (Ezzel írjuk le a tulajdonságokat).