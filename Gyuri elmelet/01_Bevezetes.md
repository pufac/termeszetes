Szia! Örömmel segítek. Ez az előadásanyag egy bevezető (Introduction), amely lefekteti az alapokat a **Természetesnyelv-feldolgozás (NLP - Natural Language Processing)** témakörében.

A célja az volt, hogy bemutassa, miért nehéz ez a terület, miért van rá szükség, és milyen valós projektekkel foglalkoznak a BME MIT tanszékén.

Itt van a fóliák részletes, strukturált összefoglalója és magyarázata:

---

### 1. Mi az a Természetesnyelv-feldolgozás (NLP)?
*(1-6. oldal)*

**Alapkoncepció:**
Az NLP a számítógépes nyelvészet, a mesterséges intelligencia (MI) és a nyelvészet metszete. A célja, hogy a gépek képesek legyenek megérteni, értelmezni és generálni az emberi nyelvet (pl. magyar, angol).

**Miért van rá szükség? (Felhasználói igények):**
*   **Kényelem:** Nem akarunk gombokat nyomogatni, inkább beszélünk a géphez (pl. Siri, Alexa, otthonirányítás).
*   **Információkeresés:** "Szeretnék megtudni valamit" – keresőmotorok, ChatGPT.
*   **Automatizálás:** "Írj egy programot", "Fordítsd le", "Foglald össze".

**A folyamat lépései (Hogyan működik?):**
1.  **Felismerés/Dekódolás:** A hangot vagy írott jelet digitális szöveggé alakítjuk (pl. beszédfelismerés, OCR).
2.  **Értelmezés (A legnehezebb rész!):** Meg kell érteni, miről szól a szöveg.
3.  **Végrehajtás/Válasz:** A gép cselekszik vagy választ generál.

**Miért nehéz ez a gépnek? (A kihívások):**
Az emberi nyelv tele van **bizonytalansággal** és **kétértelműséggel**. A fólia egy remek példát hoz:
> *"Ausztriában lopott autóval karambolozott három magyar fiatal."*

*   *Kérdés:* Hol lopták az autót? (Ausztriában lopták és ott is karamboloztak? Vagy Magyarországon lopták és Ausztriában karamboloztak?)
*   *Kérdés:* Kinek az autója volt lopott? (A fiataloké, vagy akivel ütköztek?)

A gépnek nincs "háttértudása" a világról (world knowledge), míg az ember tudja, hogy általában a bűnöző menekül a lopott autóval. Ezt a háttértudást nehéz formalizálni (ontológiák, szemantikus web).

**További nehézségek:**
*   Nyelvjárások, szleng, hibás helyesírás.
*   Kontextus függőség (pl. a "Nem." szó jelentése attól függ, mi volt a kérdés).
*   Hivatkozások feloldása (pl. "Ki írta a könyvet? **Ő**." -> Ki az az "ő"?).

---

### 2. Tudományterületek találkozása
*(7-8. oldal)*

Az NLP nem áll magában. A fólián lévő Venn-diagram és a könyvborítók azt mutatják, hogy ez egy interdiszciplináris terület:
*   **Számítógépes nyelvészet:** A nyelv matematikai/informatikai leírása.
*   **Mesterséges Intelligencia (MI):** Tanuló algoritmusok.
*   **Nyelvészet:** A nyelv szabályai.
*   **Pszichológia:** Hogyan érti meg az ember a nyelvet.

---

### 3. Valós alkalmazások és projektek (Ízelítő)
*(12-24. oldal)*

Az előadó bemutat néhány konkrét projektet, amelyeken dolgoztak. Ezek segítenek megérteni, mire jó az NLP a gyakorlatban.

#### A) Digitális Bölcsészet (Digital Humanities)
*(13-15. oldal)*
Régi szövegek feldolgozása.
*   **Kézírás-felismerés:** Mikes Kelemen leveleinek digitalizálása.
*   **Kritikai kiadások:** Az MTA-val közösen klasszikus írók (Ady, Arany) szövegeinek kereshetővé tétele.
*   **Szótárépítés:** A gép "átolvas" rengeteg régi szöveget (korpuszt), és kigyűjti, hogyan írták például Konstantinápoly nevét különböző időkben (*Constancinapoly, Constantinápolyban* stb.), majd ezt összeköti egy modern adatbázissal (DBpedia).

#### B) Információkinyerés (Ágens alapú rendszer)
*(16. oldal)*
Egy példa arra, hogyan bányász ki adatot a gép egy nyers szövegből.
*   *Bemenet:* "A ZX81 Kft. székhelye Üllő Spektrum utca 14."
*   *Feladat:* A gép darabokra szedi a mondatot, és felismeri, hogy:
    *   "ZX81 Kft." = Cégnév
    *   "Üllő" = Település
    *   "Spektrum utca" = Közterület
    *   "14" = Házszám

#### C) Hangulatelemzés (Sentiment Analysis)
*(17. oldal)*
Annak eldöntése, hogy egy szöveg (pl. termékértékelés, komment) pozitív vagy negatív.
*   **1. módszer (Szabályalapú):** Van egy listánk szavakkal. "Jó" = +1 pont, "Rossz" = -1 pont. Összeadjuk a pontokat.
*   **2. módszer (Gépi tanulás):** Megmutatunk a gépnek 10.000 pozitív és negatív példát, ő pedig megtanulja a mintázatokat (pl. a "nem" szó megfordítja a jelentést).

#### D) Stilometria (Stylometry)
*(18. oldal)*
A szöveg stílusának elemzése statisztikai módszerekkel.
*   **Mire jó?** Szerzőség megállapítása. A dendrogram (faábra) azt mutatja, hogy mely szövegek hasonlítanak egymásra. Ha találnak egy névtelen levelet, a gép megmondhatja, hogy stílusa alapján valószínűleg Mikes Kelemen írta.

#### E) Demencia Csetbot (RemiGPT) – *Fontos modern technológia!*
*(19-20. oldal)*
Ez egy ChatGPT alapú megoldás, de **RAG (Retrieval Augmented Generation)** technológiát használ.
*   **Mi a RAG?** A sima ChatGPT néha hallucinál (kitalál dolgokat). A RAG esetén a bot kap egy hiteles tudásbázist (pl. orvosi dokumentumokat az Alzheimer-kórról). Először kikeresi a választ a dokumentumból, és csak utána fogalmazza meg a választ a felhasználónak. Így pontos marad.

#### F) Robotika és Federated Learning
*(21-24. oldal)*
*   **R5COP:** Robotok irányítása természetes nyelven (pl. "Menj a polchoz és hozd ide a dobozt").
*   **Federated Learning (Szövetségi tanulás):** Ez egy adatvédelmi technológia (főleg egészségügyben fontos).
    *   *Lényege:* "Data stays, AI models travel". Az érzékeny betegadatok nem hagyják el a kórházat. A mesterséges intelligencia modell "utazik" a kórházba, ott tanul, és csak a "tanult tudást" (paramétereket) küldi vissza a központba, az adatokat nem.

---

### Összefoglalás: Mit kell ebből megjegyezned?

1.  **Az NLP definíciója:** Emberi nyelv és számítógép kommunikációja.
2.  **A fő probléma:** A természetes nyelv **kétértelmű** (ambiguitás) és kontextusfüggő, ezért nehéz programozni hagyományos szabályokkal (if-then).
3.  **Szintek:** Hang/Írás -> Szintaxis (nyelvtan) -> Szemantika (jelentés) -> Pragmatika (szövegkörnyezet/világtudás).
4.  **Technológiák:**
    *   **Korpusz:** Hatalmas szöveggyűjtemény, amiből a gép tanul.
    *   **LLM (Large Language Model):** Pl. ChatGPT.
    *   **RAG:** Amikor az LLM-et hiteles dokumentumokkal egészítjük ki a pontosság érdekében.
    *   **Federated Learning:** Adatvédelmet biztosító tanulási módszer (az adat helyben marad).

Ez a diasor egy "kedvcsináló" volt, bemutatva, hogy a kurzus során a nyelvészettől a modern mélytanuló modellekig (Deep Learning) sok mindennel fogtok találkozni. Ha bármelyik részlet (pl. a RAG vagy a stilometria) mélyebben érdekel, szólj, és kifejtem!