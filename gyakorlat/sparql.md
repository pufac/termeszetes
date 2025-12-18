Itt egy **SPARQL gyorstalpaló**, kifejezetten azokra a feladattípusokra kihegyezve, amikkel a vizsgán/gyakorlaton találkozol (RDF4J, múzeumi adatok, családfa).

A SPARQL olyan, mint az SQL, csak nem táblázatokon, hanem **gráfon** (csomópontokon és nyilakon) dolgozik.

---

### 1. Az Alapelv: A Hármas (Triple)
Minden sor a `WHERE` kapcsos zárójelei között egy állítás, ami három részből áll:
**`?Alany` + `?Állítmány (Tulajdonság)` + `?Tárgy (Érték)`**
*(Subject + Predicate + Object)*

Ahol **kérdőjel (`?`)** van, azt keresi a gép (változó). Ahol **konkrét szöveg/link** van, arra szűr.

**Példa:** "Keress meg mindent (`?s`), ami (`?p`) az (`?o`)-ra mutat."
```sparql
SELECT * 
WHERE { 
  ?s ?p ?o 
} 
LIMIT 10
```

---

### 2. Típus szerinti keresés (`rdf:type`)
Ez a leggyakoribb első lépés. "Keresd meg az összes embert/alkotót/műtárgyat."

Az `rdf:type` azt mondja meg, hogy mi az entitás "fajtája".
*   **Trükk:** Az `rdf:type` helyett írhatsz egyszerűen egy **`a`** betűt is.

**Példa:** "Listázd ki az összes Személyt (`E21_Person`)"
```sparql
PREFIX ecrm: <http://erlangen-crm.org/current/>

SELECT ?ember
WHERE {
  ?ember rdf:type ecrm:E21_Person .
}
```
*(Ha FamilyDB-t használsz, ott `owl:NamedIndividual` vagy `Person` a típus).*

---

### 3. Szűrés szövegre (`FILTER regex`)
Ha nevet keresel (pl. "Giovanni" vagy "Jane"), a `FILTER`-t kell használnod.

**Példa:** "Keresd meg azt az entitást, akinek a nevében benne van, hogy 'Jane'."
```sparql
SELECT ?ember ?nev
WHERE {
  ?ember ?tulajdonsag ?nev .  # Itt a ?nev a változó
  
  # A FILTER mindig a WHERE blokkon belül van!
  # str(?nev) -> biztos ami biztos stringgé alakítjuk
  # "i" -> case insensitive (kis/nagybetű mindegy)
  FILTER regex(str(?nev), "Jane", "i")
}
```

---

### 4. Gráf bejárása (Összekapcsolás)
Ez a "JOIN" a SPARQL-ben. Hogyan kötjük össze az Alkotót a Művel? **Közös változókkal.**
Ha az egyik sor végén ugyanaz a változónév van (`?x`), mint a következő sor elején, akkor a gép összeköti őket.

**Logika:** Alkotó -> (csinálta) -> Folyamat -> (létrehozta) -> Mű

**Kód:**
```sparql
PREFIX ecrm: <http://erlangen-crm.org/current/>

SELECT ?alkoto ?mu_cime
WHERE {
  # 1. lépés: Alkotó -> Folyamat
  ?alkoto ecrm:P14_performed ?folyamat .

  # 2. lépés: Ugyanaz a ?folyamat -> Mű
  ?folyamat ecrm:P108_has_produced ?mu .

  # 3. lépés: A műnek a címe (hogy ne csak linket lássunk)
  ?mu ecrm:P102_has_title ?cim_entitas .
  ?cim_entitas ecrm:P3_has_note ?mu_cime .
}
```

---

### 5. Számlálás (Aggregáció)
Ha a kérdés: *"Hány tagja van a családnak?"* vagy *"Hány művet készített?"*.

**Példa:** Számoljuk meg az egyedeket.
```sparql
PREFIX owl: <http://www.w3.org/2002/07/owl#>

# SELECT (COUNT(?s) AS ?darab) -> Megszámolja a találatokat
SELECT (COUNT(?s) AS ?darab)
WHERE {
   ?s rdf:type owl:NamedIndividual .
}
```

---

### 6. Egy konkrét egyed adatainak lekérése
Ha tudod a konkrét azonosítót (pl. `jane_jacobs_1836`), és mindent tudni akarsz róla. Mivel az URI-k hosszúak és tele vannak speciális karakterekkel, gyakran kacsacsőrök `< >` közé kell tenni őket.

**Példa:** Mit tudunk Jane Jacobsról?
```sparql
# Ha tudjuk a teljes URI-t:
SELECT ?tulajdonsag ?ertek
WHERE {
  <http://www.co-ode.org/roberts/family-tree.owl#jane_jacobs_1836> ?tulajdonsag ?ertek .
}
```

---

### Vizsga Puskázó (Cheat Sheet)

| Feladat | SPARQL Minta |
| :--- | :--- |
| **Minden listázása** | `SELECT * WHERE { ?s ?p ?o } LIMIT 10` |
| **Típus keresése** | `?s a <TipusNeve>` vagy `?s rdf:type <TipusNeve>` |
| **Keresés névben** | `FILTER regex(?nev, "keresett_szo", "i")` |
| **Összekötés** | `?A kapcsolat ?B . ?B kapcsolat ?C .` (A láncolás) |
| **Darabszám** | `SELECT (COUNT(?x) as ?count) WHERE { ... }` |
| **Prefix megadása** | `PREFIX nev: <http://hosszu.url/bla#>` (így használhatod: `nev:Ember`) |

**Gyakori hibák, amikre figyelj:**
1.  **Pont a sor végén:** Minden állítás végét ponttal (`.`) kell lezárni a `WHERE`-ben (kivéve az utolsót, ott nem kötelező, de nem árt).
2.  **Prefix hiánya:** Ha azt írod `E21_Person`, a gép nem érti. Vagy írd ki a teljes `<http://.../E21_Person>` címet, vagy definiáld a PREFIX-et az elején.
3.  **Változónevek:** A `?` kötelező. A `?s`, `?p`, `?o` csak szokás, írhatsz `?kiscica`-t is, ha konzisztensen használod.