Vettem! Ez a diasor **technikai szempontból az egyik legnehezebb**, de egyben a legfontosabb is, ha a szemantikus következtetésről van szó. Itt hagyjuk el az egyszerű gráfokat, és belépünk a **Leíró Logika (Description Logic - DL)** és a konkrét **Következtető Algoritmusok (Reasoning)** világába.

A vizsgán szinte biztosan lesz olyan feladat, ahol:
1.  Át kell írni egy állítást matematikai jelölésre.
2.  Végig kell követni a Tabló algoritmus lépéseit egy példán.

Íme a részletes, gyakorlatias összefoglaló:

---

### 1. Miért nem elég az RDFS? (A motiváció)
*(3. oldal)*

Az RDFS-nél láttuk, hogy "előrefele láncolással" (forward chaining) dolgozik: ha A=B és B=C, akkor létrehoz egy új tényt, hogy A=C.
*   **Probléma:** Az OWL sokkal bonyolultabb (vannak benne "vagy", "nem", "minden", "létezik" kapcsolatok). Itt az előrefele láncolás túl lassú lenne, vagy végtelen ciklusba futna.
*   **Megoldás:** **Tableau (Tabló) Algoritmus**.
    *   **Alapelv:** Nem azt bizonyítjuk be közvetlenül, hogy valami igaz, hanem **megpróbálunk ellentmondást (Clash) találni**. (Reductio ad absurdum – indirekt bizonyítás).

---

### 2. Leíró Logika (Description Logic) Jelölések
*(5-6. oldal)* – **Ezt a "szótárat" meg kell tanulni, mert ezzel írjuk le a szabályokat!**

A diákon matematikai szimbólumokat látsz. Íme a fordítókulcs:

*   **$C(x)$**: $x$ egyed a $C$ osztályba tartozik. (Pl. `Ember(Péter)`).
*   **$R(x, y)$**: $x$ és $y$ között van egy $R$ kapcsolat. (Pl. `vanGyereke(Péter, Julcsi)`).
*   **$\sqsubseteq$ (Subclass):** Részhalmaz/Alosztály. ($C \sqsubseteq D$: Minden C egyben D is).
*   **$\equiv$ (Equivalent):** Ekvivalencia. ($C \equiv D$: C és D ugyanaz).
*   **$\neg$ (Not):** Tagadás/Komplemens. ($\neg C$: Ami nem C).
*   **$\sqcap$ (And/Intersection):** Metszet. ($C \sqcap D$: Ami C is ÉS D is).
*   **$\sqcup$ (Or/Union):** Unió. ($C \sqcup D$: Ami vagy C VAGY D).

**A legtrükkösebbek (Restrikciók - 6. oldal):**
*   **$\exists R.C$ (Exists / Létezik):** Van **legalább egy** olyan kapcsolata R-en keresztül, ami C típusú.
    *   *Példa:* $\exists \text{Gyereke}.\text{Lány}$. (Van legalább egy lány gyereke).
*   **$\forall R.C$ (For all / Minden):** Ha van kapcsolata R-en keresztül, akkor az **csak és kizárólag** C típusú lehet.
    *   *Példa:* $\forall \text{Gyereke}.\text{Lány}$. (Minden gyereke lány. *Figyelem: ez akkor is igaz, ha nincs gyereke!*)
*   **$\ge n R$ (Cardinality):** Legalább $n$ darab ilyen kapcsolata van.

---

### 3. Az Algoritmus 1. lépése: Negációs Normál Forma (NNF)
*(7-10. oldal)*

Mielőtt a gép nekiállna számolni, át kell alakítani a szabályokat egy egyszerűbb, egységes formára.
**A cél:** A tagadás jele ($\neg$) csak közvetlenül az osztálynevek előtt állhat (pl. $\neg \text{Ember}$), zárójel előtt nem (pl. $\neg(A \sqcap B)$ tilos).

**Átalakítási szabályok (Ezeket kérik számon!):**
1.  **Elimináljuk a $\sqsubseteq$-t:** $A \sqsubseteq B \Rightarrow \neg A \sqcup B$. (Vagy nem A, vagy B. Ez a logikai implikáció átírása).
2.  **De Morgan azonosságok (A tagadás bevitele):**
    *   $\neg (A \sqcap B) \Rightarrow \neg A \sqcup \neg B$ (Nem igaz, hogy A és B $\rightarrow$ Vagy A nem igaz, vagy B nem igaz).
    *   $\neg (A \sqcup B) \Rightarrow \neg A \sqcap \neg B$.
3.  **Kvantorok tagadása (Ez a legfontosabb!):**
    *   $\neg \forall R.C \Rightarrow \exists R.(\neg C)$ (Nem igaz, hogy minden gyereke lány $\rightarrow$ Van legalább egy gyereke, ami NEM lány).
    *   $\neg \exists R.C \Rightarrow \forall R.(\neg C)$.

---

### 4. Az Algoritmus 2. lépése: A Tabló (Tableau) bővítése
*(11-14. oldal)*

Ez maga a következtetés menete. Van egy "vödrünk" (tabló), amibe bedobáljuk az állításokat, és szabályok szerint újakat gyártunk belőlük, amíg ellentmondást nem találunk.

**A szabályok (14. oldal táblázata – NAGYON FONTOS):**

1.  **$C(a)$**: Ha tudjuk, hogy $a$ egy $C$, beírjuk a tablóba.
2.  **$\neg C(a)$**: Ha tudjuk, hogy nem $C$, beírjuk.
3.  **$(C \sqcap D)(a)$** (ÉS szabály): Ha $a$ a metszetben van, akkor beírjuk külön: $C(a)$ **és** $D(a)$.
4.  **$(C \sqcup D)(a)$** (VAGY szabály – **Elágazás!**): Ha $a$ vagy $C$, vagy $D$, akkor a tablót **ketté kell vágni** két alternatív világra.
    *   1. világ: Feltesszük, hogy $C(a)$ igaz.
    *   2. világ: Feltesszük, hogy $D(a)$ igaz.
    *   *Ha mindkét világban ellentmondást találunk, akkor az eredeti állítás lehetetlen.*
5.  **$(\exists R.C)(a)$** (Létezik szabály): Generálunk egy **új, névtelen egyedet** (pl. $b$), és felírjuk: $R(a,b)$ és $C(b)$. (Mivel léteznie kell valakinek, adunk neki egy nevet).
6.  **$(\forall R.C)(a)$** (Minden szabály): Megnézzük, kivel van $a$-nak $R$ kapcsolata a tablóban (pl. $b$-vel), és hozzáírjuk $b$-hez, hogy $C(b)$.

---

### 5. Példa levezetése (Hogyan működik a gyakorlatban?)
*(15-22. oldal példája)*

**Feladat:**
*   Szabály: Az `Állat` uniója az (`Emlős`, `Madár`, `Hal`...).
*   Szabály: Az `Állat` és az `Ember` **diszjunkt** (nem lehet közös elemük).
*   Tény: `Seth` egy `Ember`.
*   Tény: `Seth` egy `Rovar` (Insect).
*   *Kérdés:* Konzisztens ez? (Van-e ellentmondás?)

**Levezetés (Tableau):**
1.  A "diszjunkt" szabályt átírjuk: Ha valaki `Állat`, akkor `NEM Ember`. ($\text{Animal} \sqsubseteq \neg \text{Human}$).
2.  Tudjuk, hogy `Seth` egy `Rovar`.
3.  Mivel a `Rovar` része az `Állat` uniónak (első szabály), ezért `Seth` egy `Állat`.
4.  Mivel `Seth` egy `Állat`, az 1. pont miatt $\neg \text{Human}(\text{Seth})$ (Nem Ember).
5.  **ELLENTMONDÁS (CLASH):** A bemenetben az volt, hogy `Human(Seth)`, most levezettük, hogy `NOT Human(Seth)`.
6.  **Eredmény:** Az ontológia inkonzisztens (hibás).

### 6. A "VAGY" (Diszjunkció) kezelése és a bonyolultabb példák
*(23-29. oldal)*

A második példa (Péter és Júlia) azt mutatja be, mi történik, ha elágazás van.
*   Ha van egy szabály, hogy "Mindenkinek a gyereke vagy Férfi vagy Nő", és tudjuk, hogy Péternek van gyereke, de nem tudjuk a nemét.
*   Az algoritmus szétválik:
    *   A ág: Tegyük fel, hogy a gyerek Férfi. -> Ellentmondás van?
    *   B ág: Tegyük fel, hogy a gyerek Nő. -> Ellentmondás van?
*   Ha az egyik ág "túléli" (nincs benne hiba), akkor az ontológia konzisztens.

### Összefoglalva a vizsgára (Mit gyakorolj?):

1.  **Jelölések átírása:** Tudd átírni a szöveget (`Minden gyerek lány`) képletre (`forall hasChild.Girl`).
2.  **NNF képzés:** Tudd bevinni a negálást a zárójelbe ($\neg \forall \rightarrow \exists \neg$).
3.  **Tableau lépések:** Tudd, hogy az **ÉS** ($\sqcap$) simán bővít, a **VAGY** ($\sqcup$) elágaztat, a **Létezik** ($\exists$) új egyedet hoz létre.
4.  **Clash:** Tudd, hogy a cél az ellentmondás ($A$ és $\neg A$) megtalálása.