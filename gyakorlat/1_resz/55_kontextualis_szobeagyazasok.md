Ez az 5.5-ös gyakorlat a **Kontextuális Szóbeágyazásokról (Contextual Embeddings)** szól. Ez a legfontosabb ugrás a modern NLP-ben (kb. 2018 utáni technológia).

Míg az előző (GloVe/FastText) modelleknél a "bank" szónak mindig ugyanaz volt a vektora (akár folyópart, akár pénzintézet), itt a **környezettől függően változik a vektor**.

Itt a részletes összefoglaló a vizsgára, a **TensorFlow (ELMo)** és a **PyTorch/HuggingFace (BERT)** használatáról.

---

### 1. Elméleti háttér (Mit kell tudni?)

A vizsgán a különbséget kell értened a **Statikus** és **Kontextuális** modellek között.

1.  **Statikus (GloVe, Word2Vec, FastText):**
    *   Egy szó = Egy fix vektor.
    *   Nem kezeli a többjelentésű szavakat (poliszémia).
2.  **Kontextuális (ELMo, BERT):**
    *   Egy szó vektora attól függ, milyen szavak állnak mellette.
    *   A modell nem egy szótárat olvas fel, hanem egy neurális hálót futtat le a teljes mondaton minden egyes alkalommal.
    *   **ELMo (Embeddings from Language Models):** LSTM alapú (rekurrens háló), "balról-jobbra" és "jobbról-balra" olvassa a szöveget.
    *   **BERT (Bidirectional Encoder Representations from Transformers):** Transformer alapú (Attention mechanism), egyszerre látja az egész mondatot. Ez a mai "state-of-the-art".

---

### 2. ELMo gyakorlati megvalósítása (TensorFlow Hub)

A notebook első fele (1. és 2. oldal teteje) az ELMo-t használja.

#### A) A modell betöltése és használata
Itt `tensorflow_hub`-ot használunk. A kód egy függvényt (`elmo_embeddings`) definiál, ami szövegeket vár és tenzorokat (többdimenziós tömböket) ad vissza.

*   **Bemenet:** Lista stringekkel. Pl. `["Ez egy mondat.", "Ez egy másik."]`
*   **Kimenet (Tensor):** Egy 3 dimenziós tömb.
    *   Alakja (Shape): `(Batch_size, Max_length, Embedding_dim)`
    *   Pl. `(1, 5, 1024)` jelentése: 1 mondat van, 5 szó hosszú, és minden szó 1024 számjeggyel van leírva.

#### B) A TODO kérdések megválaszolása (Vizsga-segédlet)

**Kérdés 1:** *"Írja le a többdimenziós adattárólók (a tenzorok) felépítését!"*
*   **Válasz:** A tenzor egy 3D mátrix: `[mondatok_száma, szavak_száma, vektor_dimenzió]`.
    *   `emb[0]`: Az első mondat mátrixa.
    *   `emb[0][2]`: Az első mondat harmadik szavának vektora (ez az az 1024 hosszú számsor, amivel számolni kell).

**Kérdés 2:** *"Az addresses szó reprezentációja minden példában azonos volt? Miért?"*
*   **Válasz:** NEM (vagyis attól függ).
    *   Ha önmagában áll a szó (`["address"]`), akkor mindig ugyanaz.
    *   Ha **mondatban** van, akkor az ELMo figyelembe veszi a környezetet. Tehát a *"The king addresses the audience"* (ige) és a *"He has several addresses"* (főnév) mondatokban az "addresses" szó vektora **teljesen más** lesz. (Míg GloVe-nál ugyanaz lenne).

#### C) Távolságszámítás (Összehasonlítás)
A kód a `tf.norm(emb1 - emb2)` függvényt használja. Ez az Euklideszi távolság.
*   Vizsgán a feladat: Bizonyítsd be, hogy a környezet számít!
*   Megoldás: Kiszámolod a távolságot az "addresses" vektorai között két különböző mondatban. Ha a távolság > 0, akkor a kontextus módosította a vektort.

---

### 3. BERT gyakorlati megvalósítása (Transformers)

A 2. oldal alja a modern módszert, a **Hugging Face `transformers`** könyvtárat mutatja be. Ez a leggyakoribb eszköz ma az iparban.

#### A lépések sorrendje (Recept):

1.  **Importálás:** `BertTokenizer`, `BertModel`.
2.  **Inicializálás:**
    ```python
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    model = BertModel.from_pretrained('bert-base-uncased')
    ```
3.  **Tokenizálás (Fontos!):**
    A BERT nem szavakkal, hanem "wordpiece"-ekkel (szótöredékekkel) dolgozik.
    ```python
    inputs = tokenizer("Hello world", return_tensors="pt")
    # A 'pt' azt jelenti, hogy PyTorch tenzort kérünk vissza
    ```
4.  **Futtatás (Inference):**
    ```python
    with torch.no_grad(): # Nem tanítunk, csak futtatunk -> memória spórolás
        outputs = model(**inputs)
    ```
5.  **Eredmény kinyerése:**
    Az eredmény a `last_hidden_state`-ben van.
    ```python
    embeddings = outputs.last_hidden_state
    print(embeddings.shape) 
    # Alakja: (1, tokens_szama, 768) -> A BERT base vektora 768 dimenziós.
    ```

---

### 4. Összefoglaló a vizsgához (Cheat Sheet)

Ha **kontextuális** feladatot kapsz:

1.  **Melyik modellt kérik?**
    *   Ha *TensorFlow / ELMo*: Használd a `hub.load` mintát. Tudd, hogy a kimenet `[batch, time, dim]`.
    *   Ha *Hugging Face / BERT*: Használd a `tokenizer` -> `model` láncot.

2.  **Hogyan férek hozzá egy szóhoz?**
    *   Mindig indexeléssel: `vector = embeddings[0][szó_indexe]`.
    *   *Vigyázz BERT-nél:* A BERT beszúr egy speciális `[CLS]` tokent az elejére és egy `[SEP]`-et a végére. Tehát az első igazi szavad indexe az **1**, nem a 0!

3.  **Mi a bizonyíték?**
    *   Ha azt kérik, igazold, hogy a modell "okos": Vedd ugyanazt a szót két különböző jelentésű mondatban (pl. "nyúl" mint állat, és "hozzá nyúl" mint ige), vedd ki a vektoraikat, és számold ki a távolságot. Ha nagy a távolság, a modell jól működik.

**Kódminta a vizsgára (BERT összehasonlítás):**
```python
# 1. Tokenizálás
inputs = tokenizer("Apple is a fruit", return_tensors="pt")
# 2. Modell futtatása
out = model(**inputs)
# 3. 'Apple' vektor kinyerése (az 1. indexen van a [CLS] után)
apple_fruit_vec = out.last_hidden_state[0][1]

# Ugyanez egy másik mondatra ("Apple is a company")
# ...
# apple_company_vec = ...

# Távolság (PyTorch-ban)
dist = torch.norm(apple_fruit_vec - apple_company_vec)
print(dist) # Jelentős eltérésnek kell lennie
```