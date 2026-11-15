# PROGRESS — branch `nuove-sale`

Lavoro su contenuti e fonti di `index.html`: nuove sale e artisti, regola del pubblico dominio, sezione «Entrati nel museo quest'anno», Rijksmuseum, verifica di altre fonti europee. Lo stile visivo non cambia.

Il branch **non è unito a `main`**: la versione pubblicata su GitHub Pages non cambia finché non lo decide il proprietario.
(Il PROGRESS.md del lavoro precedente, branch `fable-estetica`, resta nella cronologia di Git.)

## Regola sul pubblico dominio (vale per tutto)

- Artista morto entro `annoCorrente − 71` (nel 2026: entro il 1955).
- Opera datata entro `annoCorrente − 96` (nel 2026: fino al 1930).
- Opera senza data: entra solo se l'artista è morto entro la soglia delle opere.
- Soglie calcolate dall'anno corrente nel codice, mai scritte a mano.

## Checklist

- [~] **1. Nuove sale e artisti** — scritto e provato (38 sale, redirect ok, titolo dinamico). Manca: conteggio opere per sala (`rooms.js`, interrotto), `SYM_SEED` per le sale nuove, unione delle sale con meno di 2 artisti con opere
- [~] **2. «Entrati nel museo quest'anno»** — scritto, non ancora provato nel browser
- [~] **3. Rijksmuseum** — integrato e provato su Rembrandt (CORS aperto, IIIF v3 di micr.io); manca la prova dello zoom OpenSeadragon
- [x] **4. Altre fonti europee** — verificate; SMK integrato (vedi sotto)
- [ ] **Chiusura** — verifica con Playwright, `app-version` 19

## Stato

### 1. Nuove sale e artisti
- 82 artisti candidati verificati su Wikidata (wbsearchentities + wbgetentities): tutti esseri umani, pittori (P106 = Q1028181), morti entro il 1955. Nessuno escluso.
- Barocco diviso in Barocco italiano, Barocco fiammingo (Rubens, Snyders, Jordaens, van Dyck), Secolo d'oro olandese, Siglo de Oro spagnolo.
- Avanguardie sciolte: Kandinskij e Klee in Espressionismo (Cavaliere azzurro, con Marc, Macke, Jawlensky); Modigliani in una sala residua «Scuola di Parigi» con Pascin, Kisling, Soutine (verificati).
- `parent`: Gotico internazionale → Gotico e Protorinascimento; Paesaggio sublime americano → Romanticismo; Macchiaioli → Realismo; Nabis e Divisionismo → Postimpressionismo.
- Redirect: `#m/barocco` → `#m/barocco-italiano`, `#m/avanguardie` → `#m/espressionismo` (provato). Slug degli artisti invariati, quindi `#a/…`, `#w/…` e la collezione funzionano.
- Soglie: `PD_DEATH = anno − 71`, `PD_WORK = anno − 96`. Artisti oltre la soglia tolti all'avvio; opere filtrate in `allWorks` (anno più tardo scritto nella data), anche nella collezione (nascoste, non cancellate), opera del giorno e opere simbolo.
- Titolo «Le sale, dal 1260 al 1930» calcolato da dati e soglia.

### 2. «Entrati nel museo quest'anno»
- In home sotto le sale: artisti morti nel `PD_DEATH` (nel 2026 Pechstein e Léger) e opere del `PD_WORK` (una interrogazione Wikidata, cache `ee.newyear.v1` per 14 giorni). Meno di 2 voci: nascosta.

### 3. Rijksmuseum
- Ricerca `data.rijksmuseum.nl/search/collection` (solo id) + resolver profilo `edm-framed` (~20 KB), max 20 opere, 4 in parallelo. Autore riconosciuto dal QID Wikidata del creatore.
- L'opera non espone il proprio QID: doppioni per titolo normalizzato + anno; se l'opera Wikidata è nello stesso museo basta il 75% delle parole del titolo più corto e ±3 anni. Rembrandt: 20 risolte, 6 nuove dopo i doppioni.

### 4. Altre fonti europee
- **SMK**: nessuna chiave, CORS aperto, IIIF 2, Public Domain Mark → integrato. Solo attribuzione principale senza riserve (lo SMK mette sotto «Rembrandt» anche bottega, scuola, copie).
- **Paris Musées**: serve un account e un token (risposta 403 senza); immagini aperte CC0. Servirebbe un token, che in un sito statico sarebbe pubblico: non integrato.
- **Europeana**: serve una chiave `wskey` gratuita (pro.europeana.eu); si userebbe `api.europeana.eu/record/v2/search.json?query=who:"…"&reusability=open&media=true`. Non integrato.

## Decisioni
- Il servizio SPARQL di Wikidata il 15/11/2026 limitava a 1 richiesta/minuto gli script (disservizio in corso); dal browser rispondeva.
- In console resta un errore di risorsa preesistente: la prova immagine dell'Art Institute bloccata da Cloudflare (`ERR_BLOCKED_BY_RESPONSE`).

## Artisti esclusi
