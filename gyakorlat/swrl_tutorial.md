Itt a **SWRL (Semantic Web Rule Language) Gyorstalpaló**.

Ha az OWL a "leíró nyelv" (mint egy definíció), akkor a SWRL a **programozás** (ha ez van, csináld azt). Akkor használjuk, amikor az OWL `SubClass Of` és `Equivalent To` kifejezései már nem elég erősek (pl. matekhoz vagy bonyolult rokoni láncokhoz).

---

### 1. A Szintaxis: "Ha ... Akkor ..."

A SWRL szabály szerkezete pofonegyszerű:
**`Előtag (Body) -> Utótag (Head)`**

*   **Jelentése:** Ha az *Előtagban* minden igaz, akkor az *Utótag* is legyen igaz.
*   **Jelölés:** Az állításokat (atomokat) `^` jellel (és) fűzzük össze.
*   **Változók:** Kérdőjellel kezdődnek (pl. `?x`, `?y`, `?kor`). A gép behelyettesíti az összes lehetséges egyedet ezekbe a változókba.

---

### 2. Az Építőkockák (Atomok)

Csak ezeket használhatod a szabályokban:

| Típus | Szintaxis | Jelentés | Példa |
| :--- | :--- | :--- | :--- |
| **Osztály** | `Osztály(?x)` | `?x` ilyen típusú | `Ember(?x)` (Ha x egy Ember...) |
| **Objektum Tul.** | `tulajdonság(?x, ?y)` | `?x` kapcsolata `?y`-nal | `van_testvere(?x, ?y)` (Ha x testvére y...) |
| **Adat Tul.** | `tulajdonság(?x, ?ertek)` | `?x` adata `?ertek` | `eletkor(?x, ?kor)` (Ha x életkora egy ?kor szám...) |
| **Azonosság** | `sameAs(?x, ?y)` | `?x` ugyanaz, mint `?y` | `differentFrom(?x, ?y)` |
| **Built-in (Matek)** | `swrlb:függvény(...)` | Matek/Szöveg műveletek | `swrlb:greaterThan(?kor, 18)` |

---

### 3. A Három Leggyakoribb Vizsgafeladat Típus

Ezeket a mintákat tanuld meg, ezekkel megoldod a feladatok 99%-át.

#### A) A "Nagybácsi" probléma (Láncolás)
**Feladat:** OWL-ben nehéz leírni, hogy "Az apám testvére a nagybátyám". SWRL-ben gyerekjáték.
**Logika:** Ha `x`-nek apja `y`, ÉS `y`-nak testvére `z`, ÉS `z` Férfi => Akkor `x`-nek nagybátyja `z`.

**SWRL Szabály:**
```
van_apja(?x, ?y) ^ van_testvere(?y, ?z) ^ Ferfi(?z) -> van_nagybatyja(?x, ?z)
```

#### B) A "Nagykorú" probléma (Matek / Számhasonlítás)
**Feladat:** Ha valaki idősebb mint 18, legyen `Nagykoru`.
**Logika:**
1. Vedd az Embert (`?x`).
2. Vedd ki az életkorát egy változóba (`?kor`).
3. Ellenőrizd, hogy `?kor` > 18.
4. Ha igaz, akkor `?x` legyen `Nagykoru`.

**SWRL Szabály:**
```
Ember(?x) ^ eletkor(?x, ?kor) ^ swrlb:greaterThan(?kor, 18) -> Nagykoru(?x)
```
*Fontos: A `Nagykoru` itt egy OWL osztály. A szabály hatására a Reasoner "bedobja" az egyedet ebbe az osztályba.*

#### C) Érték átadás (Transzfer)
**Feladat:** Ha a cég székhelye Budapest, akkor a dolgozó lakhelye is legyen Budapest (feltételezés).
**Logika:** `x` dolgozik `y` cégnél + `y` székhelye `z` város -> `x` lakik `z`-ben.

**SWRL Szabály:**
```
dolgozik_nal(?x, ?y) ^ szekhely(?y, ?z) -> lakik(?x, ?z)
```

---

### 4. Hol írjam a Protégé-ben?

A SWRL nincs ott alapból a fülek között!
1.  **Bekapcsolás:** Menüsor -> `Window` -> `Tabs` -> `SWRLTab`.
2.  **Új szabály:** Kattints a `New` gombra (bal oldalon, a papír ikon + zöld plusz jel).
3.  **Írás:**
    *   A felső mezőbe adsz nevet (pl. `NagybacsiSzabaly`).
    *   Az alsó mezőbe írod a szabályt. **Tipp:** Használd a `TAB` billentyűt! Ha elkezded írni, hogy `Ember`, és nyomsz TAB-ot, kiegészíti `Ember(?x)`-re. Ez segít elkerülni a gépelési hibákat.

---

### 5. Hogyan futtassam? (A trükk)

Hiába írtad meg a szabályt, a sima `Start Reasoner` (HermiT) néha figyelmen kívül hagyja, vagy nem látod az eredményt azonnal.

**A biztos módszer:**
1.  A **SWRLTab**-on belül keresd meg a **"Drools"** vagy **"Pellet"** gombot (jobb oldalon vagy alul, verziótól függ).
2.  Nyomd meg a **"Run Drools"** (vagy Run) gombot.
3.  Ez lefuttatja a szabályokat, és átadja az eredményt az OWL ontológiának (sárgával jelennek meg az új tények).

---

### 6. Gyakori hibák (Mire figyelj?)

1.  **Csak létező dolgokra működik (DL-Safe Rules):**
    *   A SWRL szabályok csak azokra az egyedekre (Individual) működnek, amik már benne vannak az ontológiában. **Nem tud új egyedet létrehozni!**
    *   *Példa:* Nem mondhatod, hogy "Ha van egy ember, akkor *teremts* neki egy házat". Csak azt mondhatod: "Ha van egy ember ÉS van egy ház, akkor rendeld hozzá".

2.  **Nincs "NOT" (Negáció):**
    *   Nem mondhatod, hogy "Ha `?x` NEM felnőtt...". A SWRL (alapból) nem tudja kezelni a negatív feltételeket a nyílt világ feltételezés miatt.

3.  **Változók kötése:**
    *   Minden változónak, ami a nyíl jobb oldalán (`->`) szerepel, szerepelnie kell a bal oldalon is. Nem találhatsz ki új változót a semmiből a kimeneten.

### SWRL Puskázó (Built-ins)

Ezeket a `swrlb:` előtaggal éred el:

*   `swrlb:equal(?x, ?y)` - Egyenlő
*   `swrlb:notEqual(?x, ?y)` - Nem egyenlő
*   `swrlb:lessThan(?x, ?y)` - Kisebb `<`
*   `swrlb:greaterThan(?x, ?y)` - Nagyobb `>`
*   `swrlb:add(?eredmeny, ?x, ?y)` - Összeadás (`?eredmeny = ?x + ?y`) -> **Figyelj, az eredmény az első paraméter!**
*   `swrlb:stringConcat(?eredmeny, ?s1, ?s2)` - Szöveg összefűzése.

**Példa bonyolultabb matekra (Terület számítás):**
"Ha van egy téglalap, aminek van hossza és szélessége, számold ki a területét."
```
Teglalap(?t) ^ hossz(?t, ?h) ^ szelesseg(?t, ?sz) ^ swrlb:multiply(?ter, ?h, ?sz) -> terulet(?t, ?ter)
```