Ez az utolsó (5.6-os) gyakorlat a modern NLP csúcsáról, a **Nagy Nyelvi Modellek (LLM-ek)** használatáról szól az OpenAI API-n keresztül. Ez a vizsgán valószínűleg a legkönnyebb rész kódolás szempontjából (mert a modell megcsinál helyetted mindent), de érteni kell a mögötte lévő koncepciókat.

Itt az összefoglaló a szükséges elméletről és a `TODO` részek kitöltéséről.

---

### 1. Elméleti háttér (Mit kell tudni az LLM-ekről?)

A vizsgához három fő fogalmat kell tisztáznod:

1.  **Chat Completion (Szöveggenerálás):**
    *   A modell nem csak kiegészít egy mondatot, hanem "beszélget".
    *   **Szerepkörök (Roles):**
        *   `system`: A modell viselkedését állítja be (pl. "Te egy segítőkész asszisztens vagy").
        *   `user`: A te kérdésed.
        *   `assistant`: A modell válasza.
2.  **Tokenizálás (Tiktoken):**
    *   Az LLM-ek nem betűket vagy szavakat látnak, hanem **tokeneket** (szótöredékeket).
    *   Egy token kb. 0.75 szó (angolul).
    *   Ez azért fontos, mert a modelleknek van egy limitje (Context Window), és a használatért tokenenként fizetsz.
3.  **Prompt Engineering:**
    *   Ahelyett, hogy Python kódot írnál a logikára (mint a 3.1-es feladatban), egyszerű angol/magyar mondattal utasítod a modellt a feladat elvégzésére (pl. "Oldd meg ezt az analógiát...").

---

### 2. Gyakorlati megvalósítás (A TODO-k kitöltése)

Íme a lépések és a kódok, amiket a vizsgán be kell írnod a hiányzó helyekre.

#### 1. lépés: Modell kiválasztása
Az OpenAI folyamatosan frissíti a modelljeit. A vizsgán valószínűleg a `gpt-3.5-turbo` vagy a `gpt-4` a helyes válasz.

```python
# A chat modell neve
openai_modell = 'gpt-3.5-turbo' 
# Vagy 'gpt-4o', ha 2025-ben járunk és van rá keret
```

#### 2. lépés: Chat funkció használata
A megadott `openai_complete` függvény már kész van, csak érteni kell.
*   Létrehoz egy listát az üzenetekkel (`messages`).
*   Elküldi az API-nak.
*   Kinyeri a választ: `completion.choices[0].message.content`.

#### 3. lépés: Tokenizálás (`tiktoken`)
Ez a technikai rész. Meg kell nézni, hogyan látja a gép a szöveget.

*   **TODO: Miért lehet ilyen a tokenizálás?**
    *   *Válasz:* A **BPE (Byte Pair Encoding)** algoritmust használják. A gyakori szavak egy tokennek számítanak (hatékonyság), a ritkák vagy magyar ragos szavak több darabra esnek szét.
*   **Kód:**
```python
import tiktoken
# Betöltjük az adott modellhez tartozó kódolót
enc = tiktoken.encoding_for_model(openai_modell)

# Szöveg átalakítása számokká (token ID-k)
text_to_encode = "Ez egy teszt szöveg."
tokens = enc.encode(text_to_encode)
print(tokens) 
# Kimenet pl.: [345, 789, 12, ...] - számok listája
```
*   **Visszafejtés (Decode):** A ciklusban a `enc.decode([tok])` visszaalakítja a számot szöveggé, így láthatod, hol vágta el a szót a gép.

#### 4. lépés: Szóbeágyazás (Embeddings) API-val
Itt nem a GloVe-ot töltjük be fájlból, hanem lekérjük az OpenAI-tól a vektorokat. Ezek sokkal jobbak ("okosabbak"), mint a statikus GloVe vektorok.

*   **Modell neve:** Itt egy speciális embedding modellt kell megadni.
    ```python
    embedding_model = "text-embedding-3-small" 
    # Vagy a régebbi: "text-embedding-ada-002"
    ```
*   **Függvény működése:** A `client.embeddings.create` hívás visszaad egy objektumot, amiből a `.data[0].embedding` mező kell. Ez egy kb. 1536 dimenziós vektor (számlista).

#### 5. lépés: Prompttervezés (Analógia generatív módon)
Ez a feladat legérdekesebb része. Ahelyett, hogy vektorokat vonnál ki egymásból (`king - man + woman`), megkéred a Chat modellt, hogy fejezze be a mondatot.

*   **Feladat:** "small is to smaller as big to..."
*   **Kód:**
```python
prompt = "Complete this analogy: small is to smaller as big is to"
print(openai_complete(prompt))
# A modell válasza: "bigger"
```
*   **Tanulság:** A modern LLM-ek "értik" az analógiákat vektoraritmetika nélkül is, pusztán a nyelvi minták alapján.

---

### 3. Összefoglaló a vizsgára (Cheat Sheet)

Ha OpenAI / LLM feladatot kapsz:

1.  **Setup:** Kell a `client = OpenAI(...)` inicializálás.
2.  **Modellek:** Tudd fejből a neveket:
    *   Chat: `gpt-3.5-turbo`
    *   Embedding: `text-embedding-3-small`
3.  **Struktúra:** Az API mindig JSON-szerű választ ad. A szöveg mindig mélyen van elrejtve: `.choices[0].message.content`.
4.  **Embeddings:** Ha vektor kell, használd a `get_embedding` függvényt, utána pedig a `numpy`-t (`np.linalg.norm`) a távolsághoz.
5.  **Tiktoken:** Ha azt kérdezik, "Hány tokenbe kerül ez a mondat?", akkor: `len(enc.encode("mondat"))`.

**Pro tipp:** A vizsgán figyelj a **Prompt** megfogalmazására. Ha a feladat azt kéri, hogy a modell *csak* a kódot írja ki, és ne magyarázzon, akkor a `system` promptba írd bele: *"You are a code generator. Output only Python code, no explanation."*