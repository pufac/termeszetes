Szia! Ez a diasor az NLP „modernebb” korszakába vezet át: elhagyjuk a szigorú szabályokat (mint az ANTLR nyelvtanok) és a statisztikai szószámolgatást, és belépünk a **Gépi Tanulás (Machine Learning - ML)** és a **Mélytanulás (Deep Learning)** világába.

Ez az anyag lefedi az utat az egyszerű szóvektoroktól (Word Embeddings) egészen a ChatGPT-ig és a modern LLM (Large Language Model) kihívásokig.

Íme a részletes, strukturált összefoglaló:

---

### 1. Mi az a Gépi Tanulás az NLP-ben?
*(1-4. oldal)*

A klasszikus programozással ellentétben itt nem mi írjuk meg a szabályokat (pl. `ha "nem" van a szó előtt, akkor tagadás`), hanem:
*   **Adatokat** mutatunk a gépnek (tanítóhalmaz).
*   A gép **modellt** épít (megtanulja a mintázatokat).
*   Ezt a modellt új, ismeretlen adatokon is tudja használni.

**Alkalmazási példák:**
*   Hangalapú asszisztensek (Siri, Alexa, Google Assistant).
*   Ezek felhőalapúak (Cloud AI), tehát az interneten keresztül, szervereken futnak.

---

### 2. Az Amazon Alexa működése (Ismétlés + ML szemszög)
*(5-9. oldal)*

Bár ezt érintettük korábban, itt a gépi tanulás szemszögéből látjuk. A rendszernek meg kell tanulnia felismerni a felhasználó szándékát.
*   **Skill:** A képesség (pl. időjárás-jelentés).
*   **Intent (Szándék):** Mit akar a felhasználó? (pl. `GetWeather`).
*   **Slot (Helyőrző):** Milyen paraméterekkel? (pl. `Budapest`).
*   **Sample Utterances:** Példamondatok, amikkel **tanítjuk** a rendszert (pl. "Milyen idő van?", "Esernyő kell-e?"). Minél több példát adunk, a modell annál ügyesebb lesz.

---

### 3. Adatminőség és XAI (Explainable AI)
*(10-14. oldal)*

*   **A "Garbage in, Garbage out" elv:** Ha rossz adatokon tanítjuk a modellt (pl. a Microsoft Tay nevű botja, ami a Twitterről tanult és nácivá vált 24 óra alatt), akkor az eredmény is rossz lesz.
*   **Black Box (Fekete doboz) probléma:** A modern mélytanuló modellek (Deep Learning) nagyon bonyolultak. Sokszor a mérnökök sem tudják, *miért* döntött úgy a gép, ahogy.
*   **XAI (Magyarázható MI):** Ez egy kutatási terület, célja, hogy a gép meg tudja indokolni a döntéseit.

---

### 4. A nagy ugrás: Szóbeágyazások (Word Embeddings)
*(18-23. oldal)* **Ez a rész technológiailag a legfontosabb!**

Korábban a szavakat csak sorszámoztuk vagy megszámoltuk (One-hot encoding).
*   **A probléma:** Ha van 100.000 szavad, akkor egy szó egy 100.000 elemű vektor, amiben egy darab 1-es van, a többi 0. Ez ritka (sparse) és pazarló. Ráadásul a gép nem tudja, hogy a "kutya" és az "eb" rokonértelműek.

**A megoldás: Sűrű vektorok (Embeddings)**
Minden szót egy számsorozattal (vektorral) reprezentálunk, pl. 300 dimenzióban.
*   A modell megtanulja, hogy a hasonló jelentésű szavak vektorai **közel legyenek egymáshoz** a térben.
*   **Matematikai műveletek szavakkal:** A leghíresebb példa:
    $$Király - Férfi + Nő \approx Királynő$$
*   **Eszközök:** Word2Vec (Google), GloVe (Stanford), FastText (Facebook).

---

### 5. Kontextuális modellek és a Transzformerek
*(26. oldal)*

A sima szóbeágyazás (Word2Vec) hibája: a "körte" szónak ugyanaz a vektora, akár gyümölcsről, akár izzóról van szó.
*   **Kontextuális beágyazás (BERT, ELMo):** A szó vektora aszerint változik, hogy milyen szavak veszik körül.
*   **Attention (Figyelem) mechanizmus:** A modell megtanulja, hogy a mondat melyik szavára kell fókuszálnia, hogy megértse a jelentést. Ez a technológia (Transformer) az alapja a ChatGPT-nek is.

---

### 6. LLM: Large Language Models (Nagy Nyelvi Modellek)
*(27-32. oldal)*

Itt érkezünk el a **ChatGPT, GPT-4, Llama** szintjére.
*   **Jellemzők:** Hatalmas adatmennyiség (TB-nyi szöveg), milliárdos költségek, "generatív" képesség (szöveget ír).
*   **Prompt Engineering (Prompttervezés):** Mivel ezeket a modelleket nem C++ kóddal programozzuk, hanem angol (vagy magyar) utasításokkal, ki kellett találni ennek a módját.
    *   **Zero-shot:** Csak a kérdést írod be.
    *   **Few-shot:** Mutatsz neki pár példát (kérdés-válasz párt), és utána kérdezel.
    *   **Chain-of-thought (Gondolatmenet):** Megkéred a modellt, hogy "lépésről lépésre" gondolja végig (pl. matek példánál). Ez drasztikusan javítja a pontosságot.

---

### 7. RAG: Retrieval-Augmented Generation (A "csodaszer")
*(34-37. oldal)*

A sima ChatGPT hajlamos hazudni (hallucinálni), és nem ismeri a te céges adataidat vagy a tegnapi híreket (mert a tanítása lezárult a múltban).

**Megoldás: RAG (Kereséssel kiterjesztett generálás)**
Ez a technika ötvözi a keresőt az LLM-mel:
1.  Felteszed a kérdést.
2.  A rendszer **kikeresi** a releváns dokumentumokat (pl. a saját PDF-jeidből vagy a Google-ről).
3.  Ezeket a dokumentumokat odaadja a ChatGPT-nek, mint "puskát".
4.  A ChatGPT a dokumentumok alapján válaszol, nem fejből.
*   **Előny:** Pontosabb, kevesebb hallucináció, ismeri a saját adataidat.
*   **Eszköz:** `gpt4all`, `LangChain`.

---

### 8. Az LLM-ek korlátai és veszélyei
*(41-46. oldal)*

Nem minden arany, ami fénylik. A modelleknek komoly bajai vannak:
*   **Hallucináció:** Tényként közöl hamis dolgokat (pl. nem létező jogszabályokat talál ki).
*   **Bias (Torzítás):** Mivel az internetről tanult, átvette az előítéleteket.
    *   *Példa:* Ha azt írod, "az ápoló", ő nőként hivatkozik rá. Ha "az orvos", akkor férfiként.
*   **Biztonság:** "Prompt injection" támadásokkal rávehető rossz dolgokra (pl. "Írj receptet bombához").
*   **Költség:** Iszonyatosan drága futtatni őket (sok videókártya/GPU kell).

---

### 9. RLHF: Hogyan szelídítsük meg a modellt?
*(47. oldal)*

A nyers modell (amit csak szövegen tanítottak) még összevissza beszél.
**Reinforcement Learning from Human Feedback (RLHF):**
1.  A modell válaszokat ad.
2.  Emberi szakértők értékelik: "Ez jó válasz", "Ez rasszista válasz", "Ez veszélyes".
3.  A modell ebből a visszajelzésből (jutalmazás) tanulja meg, hogyan viselkedjen "jól nevelt" csetbotként.

---

### 10. Összefoglaló táblázat (Evolúció)
*(48. oldal)*

Ez a dia tökéletesen összefoglalja a kurzus ívét:

| Típus | Jellemző | Eszköz | Előny/Hátrány |
| :--- | :--- | :--- | :--- |
| **Szabályalapú (Tudásalapú)** | Nyelvészek írják a szabályokat. | ANTLR, Prolog | Pontos, de nehéz karbantartani, merev. |
| **Statisztikai (ML)** | Gyakoriságokat számol, vektorokat használ. | NLTK, Spacy | Rugalmasabb, de "sekélyes" a megértés. |
| **Transzformer (Deep Learning)** | Figyelem-mechanizmus, hatalmas modellek (GPT). | OpenAI, Llama | "Érti" a szöveget, generál, de drága és hallucinál. |

**A legfontosabb üzenet:** Ma már nem választanunk kell ezek közül, hanem kombináljuk őket (pl. RAG = Keresés + LLM), hogy kiküszöböljük a hibákat.