Ez a diasor a **Statisztikai Nyelvi Modellek** (Statistical Language Models) témakörét dolgozza fel. Ez az NLP (Természetesnyelv-feldolgozás) egyik legfontosabb alapköve, mivel itt lépünk át az egyszerű kereséstől (amit az előző anyagban láttunk) a szöveg "megértése", osztályozása és generálása felé.

Itt van a részletes, logikusan felépített összefoglaló:

---

### 1. Az alapok: Hogyan látja a gép a nyelvet?
*(1-5. oldal)*

A gép számára a nyelv nem jelentések halmaza, hanem matematikai szimbólumok sora.
*   **Alfabéta ($\Sigma$):** A rendelkezésre álló karakterek (pl. ABC betűi).
*   **Füzér (String):** Karakterek sorozata.
*   **Nyelv:** Az összes lehetséges értelmes füzér halmaza.

**Mi az a Nyelvi Modell?**
Egy olyan matematikai modell, ami két dologra képes:
1.  **Eldönti**, hogy egy karaktersorozat része-e a nyelvnek (helyes-e).
2.  **Generál** új szöveget a nyelv szabályai szerint.

Mivel a nyelv folyamatosan változik és nem lehet minden szabályt mereven leprogramozni (formális modellek), ezért **statisztikai alapokon** (megfigyelések alapján) építünk modelleket.

---

### 2. Klasszifikáció (Osztályozás) – A fő alkalmazási terület
*(3-4. oldal)*

Az előző anyagban tanult "indexelés" (melyik szó hol van) itt új értelmet nyer. Nemcsak keresésre használjuk, hanem arra, hogy kitaláljuk a dokumentum tulajdonságait.

**Példa: Hangulatelemzés (Sentiment Analysis)**
*   *Feladat:* Eldönteni egy applikáció értékeléséről, hogy a felhasználó szereti-e vagy utálja.
*   *Módszer:* Megszámoljuk a pozitív szavakat (*good, great, love*) és a negatívokat (*bad, useless*). Aki többet használ a pozitívból, az valószínűleg elégedett.

**Hogyan mérjük a pontosságot? (Nagyon fontos!)**
Mivel a gép tévedhet, szükség van egy **Tévesztési mátrixra (Confusion Matrix)**:
*   **TP (True Positive):** Helyes találat (Pozitív volt, és a gép is annak hitte).
*   **TN (True Negative):** Helyes elutasítás (Negatív volt, és a gép is annak hitte).
*   **FP (False Positive):** Téves riasztás (Negatív volt, de a gép pozitívnak hitte).
*   **FN (False Negative):** Kihagyott találat (Pozitív volt, de a gép negatívnak hitte).

Ebből számoljuk az **F1 score**-t, ami a pontosság (precision) és a találati arány (recall) egyensúlya.

---

### 3. A nyelv statisztikai tulajdonságai: Zipf-törvény
*(6-8. oldal)*

Amikor a gép elkezd olvasni egy hatalmas szöveghalmazt (**Korpusz**), érdekes dolgokat figyelhetünk meg.

**Zipf törvénye:**
Ez a nyelvészet egyik legfontosabb statisztikai szabálya. Azt mondja ki, hogy egy szó gyakorisága ($f$) és a rangsorban elfoglalt helye ($r$) fordítottan arányos.
*   $f \times r = konstans$
*   **Mit jelent ez a gyakorlatban?**
    *   Van **kevés nagyon gyakori** szó (pl. *a, az, és, hogy* – angolul *the, and*). Ezek alkotják a szöveg nagy részét, de kevés információt hordoznak.
    *   Van **rengeteg nagyon ritka** szó (pl. *szinuszritmus, defenesztráció*). Ezek a "hosszú farok" (long tail).
*   **Probléma:** Emiatt sosem lehet tökéletes szótárat építeni, mert mindig lesznek ritka szavak, amikkel a gép még sosem találkozott.
*   **Magyar nyelv nehézsége:** Mivel toldalékolunk (ragozunk), a *ház, házban, házat, házunk* mind külön szónak számítana a gépnek. Ezért a magyarban sokkal nagyobb a "szótár", hacsak nem vágjuk le a toldalékokat (**szótövesítés**).

---

### 4. Az NLP Pipeline (Feldolgozási lánc)
*(9. oldal)*

Hogyan lesz a nyers szövegből modell?
1.  **Tisztítás:** HTML kódok, felesleges formázás eltávolítása.
2.  **Szegmentálás:** Mondatokra bontás.
3.  **Tokenizálás:** Szavakra bontás (pl. szóközök mentén).
4.  **Szűrés:** A túl gyakori, töltelékszavak (stopwords) kidobása (pl. névelők), mert csak zajt keltenek.
5.  **Modellépítés:** Jöhet a matek.

---

### 5. A konkrét modellek típusai
*(10. oldal)*

Ez a diasor "technikai szíve". Két fő megközelítést mutat be:

#### A) Szózsák modell (Bag-of-Words)
*   **Lényege:** Nem érdekli a szórend, csak az, hogy melyik szó hányszor szerepel.
*   **Hasonlat:** Bedobáljuk a mondat szavait egy zsákba, és összerázzuk.
*   *Példa:* "A telefonom király" $\to$ {telefonom: 1, király: 1}. Ha a "király" szó gyakran szerepel pozitív kategóriában, a gép megtanulja, hogy ez egy dicséret.
*   **Hátrány:** Nem érti a kontextust ("Nem jó" vs "Jó").

#### B) N-gram modellek
*   **Lényege:** Figyelembe vesszük a szórendet is, kis ablakokban.
*   **Unigram (1-gram):** Csak szavak külön (mint a szózsák).
*   **Bigram (2-gram):** Szópárokat nézünk (pl. "nem jó", "nagyon szép"). Így már érzékeli a tagadást.
*   **Trigram (3-gram):** Három szót néz egyben.
*   **Valószínűség:** Ezzel a módszerrel megjósolható a következő szó. Ha azt látom: "Megyek a...", a modell tudja, hogy a "boltba" valószínűbb (80%), mint a "zsiráfba" (0.01%).

---

### 6. Eszközök és Gyakorlati alkalmazások
*(11-14. oldal)*

*   **NLTK (Natural Language Toolkit):** Ez a legnépszerűbb Python könyvtár, amivel ezeket a modelleket (tokenizálás, szótövesítés) meg lehet valósítani.
*   **Stilometria:** A szerző stílusának elemzése statisztikával (pl. ki írt egy névtelen levelet?).
*   **Tone Analyzer (IBM):** Nemcsak pozitív/negatív, hanem érzelmek (düh, öröm, félelem) felismerése.

---

### 7. A "Vak" modellek okosítása (Szemantika)
*(15-17. oldal)*

A fenti statisztikai modellek "vakok", azaz nem tudják, mit jelent a "Konstantinápoly", csak azt látják, hogy egy karaktersorozat.

*   **Hogyan javíthatunk ezen?** Háttértudást adunk hozzá.
*   **Szótárak:** Megtanítjuk neki, hogy *Konstantinápoly = Isztambul*.
*   **Ontológiák:** Hierarchikus tudásbázisok.
    *   *Példa:* Megmondjuk a gépnek, hogy "Konstantinápoly" -> "Város" -> "Település". Így ha valaki "települést" keres, megtalálja ezt is.
*   **Kapcsolt adatok (Linked Data):** Összekötjük a szöveget adatbázisokkal (pl. DBpedia, WordNet). Így a gép tudni fogja, hogy Törökországról van szó, anélkül, hogy ez le lenne írva a szövegben.

### Összefoglaló lényeg a vizsgára/megértéshez:
1.  **Nyelv = Statisztika:** A szabályok helyett gyakoriságokat számolunk.
2.  **Zipf-törvény:** A kevés gyakori szó uralja a szöveget, de a ritka szavak adják az egyediséget.
3.  **Pipeline:** Szövegből -> Tokenek -> Szűrt adatok -> Vektorok/Számok.
4.  **Bag-of-Words vs. N-gram:** Az előbbi nem lát szórendet, az utóbbi lát (pl. bigram), de sokkal több adat kell hozzá (ritka adatok problémája).
5.  **F1 Score:** Így mérjük, hogy mennyire jó a modellünk.