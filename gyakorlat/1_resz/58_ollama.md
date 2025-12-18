Ez a feladatkör a **Lokális LLM (Large Language Model) futtatásról** szól. Ez a legmodernebb trend az NLP-ben: hogyan tudsz olyan AI-t futtatni a saját gépeden (vagy zárt szerveren), amihez nem kell internet, és nem küldi ki az adataidat az OpenAI-nak.

A feladat technikai nehézsége itt nem a Python kód (az kb. 3 sor), hanem az **infrastruktúra összelegózása** (Docker, Hálózatok, Portok).

Itt az általános összefoglaló a vizsgához:

---

### 1. Elméleti háttér (Mit kell érteni?)

#### A) Ollama
Az **Ollama** a jelenlegi "de facto" szabvány a nyílt forráskódú modellek (Llama 3, Mistral, Gemma) futtatására.
*   **Működése:** Letölti a modellt, betölti a memóriába (RAM/VRAM), és nyújt egy **API-t** (általában a `11434`-es porton), amin keresztül beszélgethetsz vele.
*   **API kompatibilitás:** Úgy viselkedik, mint egy webszerver. Küldesz neki JSON-t a kérdéssel, ő visszaküldi a választ.

#### B) Docker és Hálózatok (A buktató)
A feladat legnehezebb része a hálózat megértése. Három szereplő van:
1.  **Ollama Konténer:** Itt fut az AI agya. (Belső név: `ollama`, Port: `11434`).
2.  **Colab Runtime Konténer:** Itt fut a Python kódod (Jupyter Notebook szerver).
3.  **A Te Böngésződ:** Ezzel csatlakozol a Colab Runtime-hoz.

**A trükk:** Mivel a Python kódod is Dockerben fut, nem a `localhost`-ot kell hívnia (mert a konténernek a localhost a saját belseje), hanem a másik konténer nevét (`http://ollama:11434`). Ezt látod a képernyőképen a `host=` paraméternél.

---

### 2. A megvalósítás lépései (Step-by-Step)

Ha ilyen feladatot kapsz, ezt a folyamatot kell végigcsinálnod:

#### 1. lépés: Szerver indítása (Docker Compose)
Ez a rendszer "backendje".
*   **Parancs:** `docker compose up -d`
*   **Mit csinál?** Elindítja az Ollama szervert és esetleg egy webes felületet (WebUI).
*   **Ellenőrzés:** Böngészőben beírod: `http://localhost:11434`. Ha látsz szöveget ("Ollama is running"), akkor jó.

#### 2. lépés: Modell letöltése (`docker exec`)
Az Ollama üresen indul. Meg kell mondani neki, melyik "agyat" használja.
*   **Parancs:** `docker exec -it ollama ollama run llama3.2`
    *   `docker exec`: Parancs futtatása egy már futó konténerben.
    *   `ollama`: A konténer neve (a `docker-compose.yml`-ből).
    *   `ollama run <modell>`: Letölti és elindítja a modellt.
*   *Vizsga tipp:* Ha ezt kihagyod, a Python kódod "Model not found" hibát fog dobni.

#### 3. lépés: A Python környezet (Local Runtime)
Mivel a Google Colab (felhő) nem látja a te gépedet, egy trükköt kell alkalmazni: a Colab "agyát" (runtime) is a te gépeden futtatjuk Dockerben.
*   **Parancs:** A feladatleírásban lévő hosszú `docker run ... colab-runtime ...` sor.
*   **Csatlakozás:** A parancs kiír egy URL-t (`http://127.0.0.1:9000/?token=...`). Ezt kell bemásolni a Google Colab felületén a "Connect to local runtime" ablakba.

#### 4. lépés: Python Kód (A screenshot elemzése)
Ha a rendszer fut, a kódolás már egyszerű. Az `ollama` Python könyvtárat használjuk.

**A kód magyarázata:**
```python
# 1. Könyvtár importálása
from ollama import Client

# 2. Csatlakozás a szerverhez
# FONTOS: Ha Docker hálózatban vagyunk, a host nem 'localhost', 
# hanem a konténer neve (pl. 'ollama')!
ollama_client = Client(host='http://ollama:11434')

# 3. Üzenetküldés (Chat)
response = ollama_client.chat(
    model='llama3.2:latest',  # A modellnek egyeznie kell azzal, amit letöltöttél!
    messages=[
        {'role': 'user', 'content': 'Tell me a joke!'}
    ]
)

# 4. Válasz kinyerése
print(response['message']['content'])
```

---

### 3. Gyakori hibák és megoldásaik (Troubleshooting)

A képernyőképen egy piros `ConnectionError` látható. Mit jelent ez?

1.  **Hiba:** `ConnectionRefused` vagy `Failed to connect`.
    *   **Ok:** A Python kód nem éri el az Ollama szervert.
    *   **Megoldás 1 (Cím):** Rossz a `host`. Ha Dockerben van mindkettő, használd a `http://ollama:11434`-et. Ha a gépedről futtatod a Pythont (pl. VS Code-ból), használd a `http://localhost:11434`-et.
    *   **Megoldás 2 (Hálózat):** A két Docker konténer nincs közös hálózaton (`network`). Ellenőrizd a `docker-compose.yml`-t!
    *   **Megoldás 3 (Állapot):** Nem fut az Ollama. (Nézd meg: `docker ps`).

2.  **Hiba:** `Model not found`.
    *   **Ok:** A kódban `llama3.2`-t hívsz, de a szerverre még nem töltötted le.
    *   **Megoldás:** Futtasd a terminálban: `docker exec -it ollama ollama pull llama3.2`.

3.  **Hiba:** Lassú válasz.
    *   **Ok:** CPU-n fut a modell GPU helyett.
    *   **Megoldás:** Ez normális, ha nincs NVIDIA kártyád vagy nincs bekonfigurálva a Docker GPU passthrough. A vizsgán türelem kell hozzá.

### Összefoglalva a stratégiát:
Ez a feladat **Rendszerintegrációról** szól.
1.  **Docker Compose**-zal elindítod a szolgáltatást.
2.  **Parancssorban** letöltöd a modellt (`pull`).
3.  **Pythonban** rácsatlakozol a megfelelő címen (`host`), és JSON üzeneteket küldesz.
A kódolás minimális, a hangsúly a környezet helyes beállításán van.