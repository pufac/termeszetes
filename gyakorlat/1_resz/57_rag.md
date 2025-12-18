Ez a feladat egy **RAG (Retrieval-Augmented Generation)** rendszer gyakorlati felépítését és használatát mutatja be, konténerizált környezetben (**Docker**).

A vizsgán ez a feladat azt méri, hogy érted-e a modern NLP rendszerek architektúráját (LLM + Tudásbázis), és rendelkezel-e alapvető üzemeltetési ismeretekkel (Docker).

Íme az általános összefoglaló a lépésekről és az elméleti háttérről.

---

### 1. Elméleti háttér: Mi az a RAG és miért kell Docker?

#### A) RAG (Retrieval-Augmented Generation)
A "sima" ChatGPT-nek két baja van:
1.  **Hallucinál:** Ha nem tud valamit, kitalálja.
2.  **Elavult/Hiányos:** Nem ismeri a te privát dokumentumaidat (pl. a vizsgatételeket vagy a céges PDF-eket).

A **RAG** ezt oldja meg:
1.  Van egy **Dokumentumtár (Vector Database):** Ide töltöd fel a saját PDF-jeidet. A rendszer ezeket feldarabolja és vektorokká alakítja (Embedding).
2.  **Keresés (Retrieval):** Amikor kérdezel, a rendszer először megkeresi a dokumentumtárban a kérdésedhez releváns szövegrészleteket.
3.  **Generálás:** A megtalált szövegeket hozzácsapja a kérdésedhez ("Itt van ez az infó, ez alapján válaszolj: ..."), és így küldi el az LLM-nek (pl. GPT-4).

#### B) Docker
Ezek a rendszerek bonyolultak (kell nekik adatbázis, backend, frontend, Python könyvtárak). Hogy ne kelljen órákig telepítgetni a gépedre, egy **Docker Containerbe** csomagolják őket. Ez egy "doboz", amiben minden benne van, ami a futáshoz kell. Neked csak el kell indítanod a dobozt.

---

### 2. A megvalósítás lépései (Általános recept)

A feladat konkrétan a `gpt4all` eszközt használja, de a folyamat minden RAG rendszernél (PrivateGPT, Ollama, LangChain) ugyanez.

#### 1. lépés: Környezet indítása (Deployment)
*   **Letöltés:** Megkapod a konfigurációs fájlokat (általában egy `docker-compose.yaml`).
*   **Indítás:** A parancssorban (Terminal/PowerShell) abba a mappába lépsz, ahol a fájl van.
*   **Parancs:** `docker compose up --build`
    *   Ez letölti a szükséges "dobozokat" (image-eket) az internetről, összekapcsolja őket, és elindítja a szervert.
    *   A `--build` kapcsoló biztosítja, hogy ha változott valami, újraépüljön a rendszer.

#### 2. lépés: Konfiguráció (Model Setup)
A webes felületen (általában `localhost:valami`) be kell állítani az "agyat".
*   **Local Model:** Ingyenes, a gépeden fut (pl. Llama 2, Mistral). Lassabb lehet, de privát.
*   **Cloud Model (OpenAI):** Ezt kéri a feladat. Itt a rendszer csak egy "közvetítő".
    *   Meg kell adnod az **API Kulcsot** (amit az 5.6. gyakorlatban is használtál).
    *   Kiválasztod a típust: `GPT-4 Turbo`. Ez azért jó, mert nagy a "kontextusablaka" (sok szöveget tud egyszerre feldolgozni).

#### 3. lépés: Adatbetöltés (Ingestion) – A legfontosabb rész!
A RAG lényege a saját adat.
1.  **Kollekció létrehozása:** A felületen létrehozol egy "mappát" (pl. `LocalDocs`), ami logikailag összefogja a dokumentumaidat.
2.  **Fájl másolása a konténerbe:** Mivel a Docker egy zárt doboz, nem látja a te Asztalodat. Be kell másolni oda a fájlt.
    *   **Parancs:** `docker cp [helyi_fájl_útvonal] [konténer_neve]:[belső_útvonal]`
    *   *Példa:* `docker cp jegyzet.pdf gpt4all:/root/Documents/`
    *   *Magyarázat:* A `gpt4all` a konténer neve, a kettőspont után pedig az a mappa a konténeren BELÜL, ahová a rendszer várja a fájlokat (ezt a leírás szokta megadni).
3.  **Beágyazás (Embedding):** A felületen rá kell nyomni a frissítésre/indexelésre. Ekkor a rendszer végigolvassa a PDF-et, és elmenti a vektorokat az adatbázisába. Ez eltarthat egy ideig.

#### 4. lépés: Használat (Querying)
*   **Dokumentum nélkül:** Ha csak simán kérdezel, a GPT-4 a saját (2023-ig tanult) tudását használja.
*   **Dokumentummal:** Ha bepipálod a `LocalDocs`-ot (vagy az adott fájlt), akkor a RAG aktiválódik.
    *   *Tesztelés:* Kérdezz olyat, ami biztosan CSAK a PDF-ben van benne (pl. egy speciális szerződés sorszáma, vagy egy fiktív történet szereplője). Ha helyesen válaszol, működik a RAG.

#### 5. lépés: Leállítás és Takarítás (Cleanup)
Vizsgán fontos, hogy ne hagyj szemetet magad után.
*   **Leállítás:** `CTRL+C` a terminálban, vagy `docker compose down`. Ez lelövi a futó folyamatokat.
*   **Törlés (Prune):** `docker system prune`.
    *   Ez törli a leállított konténereket, a nem használt hálózatokat és a "szemetet".
    *   *Vigyázz:* Ez mindent töröl, ami nem fut éppen! A beágyazott adatbázisod is elveszhet, ha nem csatoltál (mountoltál) külső kötetet (volume). A vizsgafeladat kéri az adatfájlok törlését, tehát ez itt a cél.

---

### 3. Vizsga tippek és buktatók

1.  **Hibakezelés:** "Ha hibával leállna a konténer..."
    *   A Docker néha kifogy a memóriából, főleg ha nagy modellt vagy nagy PDF-et dolgozol fel.
    *   Megoldás: Indítsd újra a `docker compose up` paranccsal. A már bemásolt fájlok (általában) megmaradnak, ha perzisztens volume-ot használ a rendszer, de ha nem, akkor újra be kell másolni (`docker cp`).

2.  **Útvonalak:**
    *   A `docker cp`-nél figyelj, hogy hol vagy a terminálban. Ha a `file.pdf` nem ott van, ahol állsz, írd be a teljes elérési utat.

3.  **Hasonlítás:**
    *   A feladat kéri, hogy kérdezz dokumentum nélkül is.
    *   *Miért?* Hogy lásd a különbséget. Dokumentum nélkül a GPT lehet, hogy általánosságokat mond vagy hallucinál. Dokumentummal konkrétan idéznie kell a forrásból.

4.  **OpenAI Key:**
    *   Ne felejtsd el, hogy a kulcsnak érvényesnek kell lennie és legyen rajta kredit ($). Ha a kulcs nem jó, a rendszer hibaüzenetet fog dobni a chateléskor.

**Összefoglalva:**
A feladat lényege, hogy felépíts egy saját "Okos Keresőt". Ehhez a Dockert használod futtatókörnyezetnek, az OpenAI-t agynak, és a `docker cp` parancsot adatkábelnek, amin keresztül beadod a tudást a gépnek.