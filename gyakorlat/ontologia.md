Ez az útmutató az **OWL Ontológiák** és a **Protégé** szoftver használatát foglalja össze, a megadott feladatsor alapján. Ez a gyakorlat az "okos" adatbázisok világába vezet, ahol a gép nem csak tárolja az adatot, hanem **következtet** is belőle (pl. ha valaki anya, akkor biztosan nő).

---

# Általános Útmutató: OWL Ontológiák és Protégé

## 1. Alapok: Mi az az OWL Ontológia?
Az OWL (Web Ontology Language) egy nyelv, amivel leírhatjuk a világ dolgait és a köztük lévő logikai szabályokat.

A Protégé-ben három fő füllel dolgozunk:
1.  **Classes (Osztályok):** A fogalmak (pl. Ember, Művész, Kép).
2.  **Object Properties (Objektum Tulajdonságok):** Kapcsolat két dolog között (pl. *megfestette*, *anyja*).
3.  **Data Properties (Adat Tulajdonságok):** Értékek (pl. *születési év*, *magasság*).

---

## 2. A "Reasoner" (Következtető) Szerepe (2. feladat)
Ez a "varázslat" az ontológiákban. Ha bekapcsolod (Reasoner -> Start reasoner), a gép végigfut a szabályokon, és új dolgokat talál ki.

*   **Sárga háttér:** A Protégé sárgával jelöli azt, amit a gép "magától jött rá" (inferred), nem te írtad be.
    *   *Példa:* Ha definiálod, hogy "Mindenki, akinek van gyereke, az Szülő", és felviszed, hogy "Jóska apja Petinek", a gép sárgával kiírja Jóska mellé: **Type: Szülő**.

---

## 3. Séma Ellenőrzése és Inkonzisztencia (3. feladat)
Az ontológiák szigorúak. Ha ellentmondást írsz, a Reasoner "kiabál" (piros lesz minden).

**Hogyan hozz létre szabályokat?**
*   **Domain (Értelmezési tartomány):** "Kire igaz ez a tulajdonság?"
    *   *Példa:* A `hasBirthYear` (születési év) Domain-je az `Ember`. Ha beírod egy Kőnek a születési évét, a gép tudni fogja, hogy az a Kő valójában egy Ember (vagy hiba van).
*   **Range (Értékkészlet):** "Milyen típusú az érték?"
    *   *Példa:* `xsd:integer` (egész szám) vagy `xsd:decimal`.
*   **Disjoint With (Kizáró halmazok):** "Aki A, az nem lehet B."
    *   *Példa:* `Ember` Disjoint With `Kő`.

**A feladatban lévő hiba (Inkonzisztencia):**
Ha azt mondjuk, hogy az `Actor` (Ember) és a `Dimension` (Méret) kizárják egymást, de az adatok között véletlenül van egy "Rembrandt", akinek van értéke (pedig csak méretnek lehetne), a Reasoner hibát jelez.
*   **Megoldás:** Meg kell keresni a hibás adatot és törölni, vagy javítani a szabályt. Az "Explain" gomb megmondja, mi a baj.

---

## 4. DL Query: A Logikai Kereső (4. feladat)
A DL (Description Logic) Query fülön nem SQL-t írunk, hanem logikai feltételeket.

**Szintaxis (Puskázó):**
*   **És:** `and` (pl. `Ember and Nok`)
*   **Vagy:** `or`
*   **Nem:** `not`
*   **Van valamilyen kapcsolata:** `property some Class`
    *   *Példa:* `hasChild some Man` (Van fiúgyermeke)
*   **Konkrét érték:** `property value ...`
    *   *Példa:* `hasBirthYear value 1990`
*   **Intervallum (Számoknál):** `some típus[>= tól, <= ig]`

**Példa a feladatból (Képek mérete):**
"Legfeljebb 100-as méretű dolgok":
```
ecrm:P90_has_value some xsd:decimal[<= 100]
```
De ez még megtalálja a 100 cm magas embereket is. Szűkítsük le Képekre:
```
ecrm:E22_Man-Made_Object and (ecrm:P90_has_value some xsd:decimal[<= 100])
```

---

## 5. Nevesített Osztályok Definiálása (5-7. feladat)
Ha egy DL Query-t gyakran használsz, elmentheted új Osztályként (`Defined Class`).

**Lépések:**
1.  Classes fül -> Új osztály létrehozása (pl. `BarokkMuvesz`).
2.  Az osztály leírásánál (Description) az **"Equivalent To"** (Azzal egyenértékű) mezőbe írd be a szabályt.

**Trükk: Az "Inverse" használata**
Néha visszafelé kell gondolkodni.
*   Van: `Alkotási_Folyamat` -> `résztvevője` -> `Művész`.
*   Keresünk: Művészt.
*   Logika: Olyan Művész, aki "résztvevője volt" (inverze a résztvevőjének) egy 17. századi folyamatnak.

**Példa (17. századi művész):**
```
inverse (ecrm:P11_had_participant) some (
    ecrm:E65_Creation and (dbo:creationYear some integer[>=1601, <=1700])
)
```

**Automatikus besorolás:**
Ha létrehozod a `BarokkMuvesz` osztályt a fenti szabállyal, és elindítod a Reasonert, a gép **automatikusan** áthelyezi Rembrandt-ot ebbe az osztályba a listában (sárgával), anélkül, hogy te hozzáadtad volna.

---

## 6. Új Ontológia Építése (8. feladat)
Ez a "csináld magad" rész.

**Hogyan építsd fel?**
1.  **Osztályok (Classes):** Ember, Férfi, Nő.
    *   *Tipp:* Férfi és Nő legyenek az Ember alosztályai (`SubClass Of`).
2.  **Tulajdonságok (Object Properties):** van_gyereke, van_szuleje, van_testvere.
    *   *Inverzek:* `van_gyereke` inverze `van_szuleje`.
3.  **Adatok (Data Properties):** eletkor (integer).

**A "Nagykorú" definíciója (Equivalent To):**
```
Ember and (eletkor some integer[>=18])
```

**A "Nagymama" definíciója:**
Itt láncolni kell a tulajdonságokat (`property chain`). De DL Query-ben egyszerűbb így:
```
No and (van_gyereke some (van_gyereke some Ember))
```
*(Magyarul: Nő, és van olyan gyereke, akinek van gyereke.)*

**Tesztelés:**
Hozz létre egy "Juliska" egyedet (Individual), add meg neki, hogy Nő, és az életkora 80. Indítsd el a Reasonert. Ha jól dolgoztál, a gép magától rájön, hogy Juliska `Nagykorú`.