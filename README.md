# 🌐 AgriTech Backend

## 📖 Descrizione
Questo progetto costituisce la parte backend del sistema di **digital-twin** per la protezione delle vigne dagli eventi atmosferici (🌧️ pioggia e 🌨️ grandine). Il sistema utilizza sensori collegati a un **ESP32** per monitorare in tempo reale le condizioni meteo e attivare meccanismi intelligenti di protezione con tende smart ⛺️. Inoltre, queste tende raccolgono l'acqua piovana 💧 e la convogliano in cisterne, migliorando la gestione idrica 🌱.

---

### 💻 Conoscenze Preliminarie Necessarie

Prima di procedere, è utile avere familiarità con alcuni strumenti e concetti:

- 🐙 **Git**: familiarità con Actions e runner self-hosted.
- 🌩️ **Cloudflare**: per configurare servizi esterni e protezione API.
- 🌐 **Reti e DNS**: conoscere i concetti base di rete, tunnel DNS e routing applicativo.
- 🐧 **Linux**: conoscenze di base per l’uso di terminale e comandi admin.

## ⚙️ Requisiti minimi

🐧 **Linux server** (ambiente di produzione)  
🐳 **Docker** e **Docker Compose** installati

---

## 🚀 Come iniziare passo per passo

Segui questi passaggi per avviare l'ambiente di sviluppo e produzione del backend AgriTech:

### 1️⃣ Fork del progetto

1. Vai alla [repository originale su GitHub](https://github.com/Aletheia-Startup/AgriTech/tree/backend).
2. Fai clic sul pulsante `Fork` per creare una copia del progetto nel tuo account GitHub.

---

### 2️⃣ Clona la repository sul server

Sul tuo **server Linux**, apri il terminale e posizionati nella directory dove vuoi clonare il progetto:

```bash
git clone https://github.com/TUO-UTENTE/AgriTech.git
cd AgriTech/backend
```

> 🔎 **Nota**: Sostituisci `TUO-UTENTE` con il tuo username GitHub!

### 3️⃣ Configura Cloudflare

- Accedi a [Cloudflare](https://www.cloudflare.com/) e aggiungi il tuo dominio.
- Imposta i record DNS, come A o CNAME, per puntare al tuo server. Per approfondire consulta la guida [Cloudflare DNS Docs](https://developers.cloudflare.com/dns/manage-dns-records/how-to/create-dns-records/).
    - (Opzionale) Configura regole firewall per protezione avanzata.
- Crea un **Tunnel** direttamente da Cloudflare (tramite la dashboard) e inserisci il **Token del Tunnel** nel file `.env` come `CF_TOKEN`. Puoi seguire la guida ufficiale [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/).

### 4️⃣ Imposta le variabili d’ambiente

Per il corretto funzionamento del backend, è necessario settare il file `.env` nella directory `backend/` con le seguenti variabili:

### 📄 Significato delle variabili

| Variabile                         | Descrizione                                                                                                                                                            |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`MONGO_INITDB_ROOT_USERNAME`**  | Nome utente utilizzato per l’autenticazione al database MongoDB.                                                                                                        |
| **`MONGO_INITDB_ROOT_PASSWORD`**  | Password corrispondente al nome utente per l’accesso a MongoDB.                                                                                                         |
| **`PORT`**                        | Porta su cui è in ascolto il server MongoDB. Valore predefinito: `27017`.                                                                                               |
| **`HOSTNAME`**                    | Indirizzo IP o hostname del server MongoDB.                                                                                                                              |
| **`DATABASE`**                    | Nome del database MongoDB che ospita i dati dei sensori.                                                                                                                |
| **`COLLEZIONE`**                  | Nome della collezione principale per i dati dei sensori (ad esempio, letture di temperatura e umidità).                                                                   |
| **`COLLEZIONE_METEO`**            | Nome della collezione per i dati meteorologici esterni (ad esempio, pioggia, grandine, vento).                                                                           |
| **`COLLEZIONE_STATI`**            | Nome della collezione che tiene traccia dello stato delle tende smart (ad esempio, aperte `1` o chiuse `0`).                                                              |
| **`WEATHER_API_KEY`**             | Chiave API per accedere al servizio meteo esterno, come [weatherapi.com](https://www.weatherapi.com/docs/).                                                              |
| **`ENV`**                         | Ambiente di esecuzione dell’applicazione: `development` per log dettagliati in console, `production` per un ambiente stabile e con meno log.                            |
| **`CF_TOKEN`**                    | Token di autenticazione per servizi esterni ([Cloudflare](https://www.cloudflare.com/)) usato per la comunicazione sicura con dispositivi o API esterne.      |

### 5️⃣ Passaggi per modificare e salvare un file con `nano`

1️⃣ **Apri il file con privilegi di amministratore:**

```bash
sudo nano ~/agritch/backend/.env
```

2️⃣ **Modifica le variabili d’ambiente** presenti nel file `.env`.

3️⃣ **Salva le modifiche senza cambiare il nome del file:**
- Premi i tasti `Ctrl` + `O` (vedrai in basso la scritta “File Name to Write: .env”).
- **Non modificare il nome**: premi `Invio` per confermare.

4️⃣ **Esci da nano:**
- Premi `Ctrl` + `X` per chiudere l’editor.

### 6️⃣ Configura il runner self-hosted di GitHub Actions

Per eseguire le pipeline di **GitHub Actions** direttamente sul tuo **server Linux** (self-hosted runner), segui questi passaggi:

1️⃣ Vai alla sezione runner di questa repository e segui le istruzioni fornite nella pagina:  
👉 **Modifica l’URL** aggiungendo il tuo nome utente (ad esempio `https://github.com/TUO-UTENTE/AgriTech/settings/actions/runners/new?arch=x64&os=linux`).

> 🔎 **Nota**: Sostituisci `TUO-UTENTE` con il tuo username GitHub!

### 7️⃣ Avvia tutti i servizi

I servizi definiti nel `docker-compose.yml` includono:
- **fastapi-app** (backend principale)
- **mongodb** (database MongoDB)
- **cloudflared** (per il tunnel Cloudflare)

Per avviarli:

```bash
cd ~/AgriTech/backend
sudo docker-compose up -d --build
```

Questo comando avvia **tutti i servizi** in modalità detached e li collega tra loro nella rete `agritech_network`.

Puoi controllare lo stato dei servizi con:

```bash
sudo docker-compose ps
```

o i log in tempo reale:

```bash
sudo docker-compose logs -f
```
Per fermare tutti i servizi:

```bash
sudo docker-compose down
```

## 🗂️ Struttura del progetto

```plaintext
.github/
└── workflows/
    └── deploy.yml

backend/
├── app/
│   ├── __init__.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── irrigation_receive.py
│   │   ├── manual_weather_fetch.py
│   │   ├── receive_state.py
│   │   └── send_data.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── html_generator.py
│   │   ├── logger_config.py
│   │   └── start_page.py
│   ├── crud/
│   │   ├── __init__.py
│   │   ├── get_irrigation.py
│   │   ├── get_status.py
│   │   ├── meteo_requests.py
│   │   └── value_insert.py
│   ├── database/
│   │   ├── __init__.py
│   │   ├── db_connection.py
│   │   └── db_setup.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── pydantic_schema.py
│   │   └── record_schema.py
│   ├── tests/
│   │   ├── __init__.py
│   │   └── function_testing.py
│   ├── __init__.py
│   └── main.py
├── .dockerignore
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
├── .gitignore
└── README.md
```

## 📞 Contatti

In caso di difficoltà o domande, puoi contattarmi:

✉️ **Email:** [me@checcoconf.it](mailto:me@checcoconf.it)  
💬 **Telegram:** [@checcoconf](https://t.me/checcoconf)

---
