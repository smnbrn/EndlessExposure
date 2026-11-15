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

- [x] **1. Nuove sale e artisti** — 38 sale (24 nuove), redirect, titolo dinamico, opere simbolo in `SYM_SEED`; ogni sala nuova ha tutti i suoi artisti con opere, nessuna unione necessaria
- [x] **2. «Entrati nel museo quest'anno»** — provato: Pechstein e Léger, 8 opere del 1930, apertura dell'opera, seconda visita dalla cache (0 richieste)
- [x] **3. Rijksmuseum** — integrato e provato su Rembrandt (CORS aperto); zoom OpenSeadragon sul IIIF v3 di micr.io provato (anche SMK, IIIF 2)
- [x] **4. Altre fonti europee** — verificate; SMK integrato (vedi sotto)
- [x] **Chiusura** — verifica con Playwright superata (1440×900 e 390×844), `app-version` 19

## Stato

### 1. Nuove sale e artisti
- 82 artisti candidati verificati su Wikidata (wbsearchentities + wbgetentities): tutti esseri umani, pittori (P106 = Q1028181), morti entro il 1955. Nessuno escluso.
- Barocco diviso in Barocco italiano, Barocco fiammingo (Rubens, Snyders, Jordaens, van Dyck), Secolo d'oro olandese, Siglo de Oro spagnolo.
- Avanguardie sciolte: Kandinskij e Klee in Espressionismo (Cavaliere azzurro, con Marc, Macke, Jawlensky); Modigliani in una sala residua «Scuola di Parigi» con Pascin, Kisling, Soutine (verificati).
- `parent`: Gotico internazionale → Gotico e Protorinascimento; Paesaggio sublime americano → Romanticismo; Macchiaioli → Realismo; Nabis e Divisionismo → Postimpressionismo.
- Redirect: `#m/barocco` → `#m/barocco-italiano`, `#m/avanguardie` → `#m/espressionismo` (provato). Slug degli artisti invariati, quindi `#a/…`, `#w/…` e la collezione funzionano.
- Soglie: `PD_DEATH = anno − 71`, `PD_WORK = anno − 96`. Artisti oltre la soglia tolti all'avvio; opere filtrate in `allWorks` (anno più tardo scritto nella data), anche nella collezione (nascoste, non cancellate), opera del giorno e opere simbolo.
- Titolo «Le sale, dal 1260 al 1930» calcolato da dati e soglia.

### Opere per sala nuova (15/11/2026, dall'app in Chromium, tutte le fonti, dopo il filtro del pubblico dominio)
Numero di opere che l'app trova per gli artisti di ogni sala (somma dei «N in tutto» delle pagine degli artisti).
Una prima passata aveva numeri sbagliati: lo script leggeva la pagina dell'artista precedente (cambio di solo hash). Corretto aspettando il nome giusto.
Il Met e l'Art Institute non hanno risposto per tutta la prova (il Met limita le richieste, l'Art Institute è dietro Cloudflare): i numeri contano Wikidata, Cleveland, Rijksmuseum e SMK.

| Sala | Opere | Artisti con opere | Wikidata non raggiungibile durante la prova |
|---|---|---|---|
| Gotico internazionale | 111 | 2/2 | — |
| Barocco italiano | 1151 | 7/7 | — |
| Barocco fiammingo | 863 | 4/4 | — |
| Secolo d'oro olandese | 958 | 5/5 | — |
| Siglo de Oro spagnolo | 596 | 4/4 | Murillo (3, solo Cleveland) |
| Classicismo francese del Seicento | 444 | 2/2 | — |
| Settecento inglese | 761 | 5/5 | Reynolds (14, solo Cleveland) |
| Paesaggio sublime americano | 347 | 3/3 | Bierstadt (3) |
| Macchiaioli | 151 | 3/3 | — |
| Preraffaelliti | 652 | 4/4 | — |
| Pittura accademica | 574 | 4/4 | Cabanel (2) |
| Pittura russa | 640 | 4/4 | — |
| Realismo americano | 593 | 3/3 | Sargent (10) |
| Nabis | 394 | 2/2 | — |
| Divisionismo italiano | 64 | 3/3 | — |
| Pittura scandinava | 526 | 3/3 | — |
| Fauvismo | 460 | 4/4 | — |
| Espressionismo | 1661 | 12/12 | — |
| Cubismo e Orfismo | 642 | 6/6 | — |
| Futurismo | 85 | 2/2 | — |
| Scuola di Parigi | 387 | 4/4 | — |
| Astrattismo russo | 207 | 4/4 | — |
| Dada | 42 | 3/3 | — |
| De Stijl | 376 | 2/2 | — |

Per i cinque artisti segnati Wikidata ha risposto con errore (limite di richieste del 15/11): l'errore non resta in cache e la pagina riprova alla visita successiva.

### 2. «Entrati nel museo quest'anno»
- In home sotto le sale: artisti morti nel `PD_DEATH` (nel 2026 Pechstein e Léger) e opere del `PD_WORK` (una interrogazione Wikidata, cache `ee.newyear.v1` per 14 giorni). Meno di 2 voci: nascosta.

### 3. Rijksmuseum
- Ricerca `data.rijksmuseum.nl/search/collection` (solo id) + resolver profilo `edm-framed` (~20 KB), max 20 opere, 4 in parallelo. Autore riconosciuto dal QID Wikidata del creatore.
- L'opera non espone il proprio QID: doppioni per titolo normalizzato + anno; se l'opera Wikidata è nello stesso museo basta il 75% delle parole del titolo più corto e ±3 anni. Rembrandt: 20 risolte, 6 nuove dopo i doppioni.

### 4. Altre fonti europee
- **SMK**: nessuna chiave, CORS aperto, IIIF 2, Public Domain Mark → integrato. Solo attribuzione principale senza riserve (lo SMK mette sotto «Rembrandt» anche bottega, scuola, copie).
- **Paris Musées**: serve un account e un token (risposta 403 senza); immagini aperte CC0. Servirebbe un token, che in un sito statico sarebbe pubblico: non integrato.
- **Europeana**: serve una chiave `wskey` gratuita (pro.europeana.eu); si userebbe `api.europeana.eu/record/v2/search.json?query=who:"…"&reusability=open&media=true`. Non integrato.

## Verifica finale (15/11/2026, Playwright + Chromium, rete attiva, 1440×900 e 390×844)
- Home: titolo «Le sale, dal 1260 al 1930», 38 sale, nessuno scorrimento orizzontale, sezione «Entrati nel museo quest'anno» visibile.
- Redirect: `#m/barocco` → `#m/barocco-italiano`, `#m/avanguardie` → `#m/espressionismo`.
- 8 sale nuove (Preraffaelliti, Cubismo e Orfismo, Secolo d'oro olandese, Pittura russa, Espressionismo, Barocco italiano, Settecento inglese, Pittura scandinava): per ognuna un artista e un'opera aperta e caricata; nessuna opera mostrata oltre il 1930.
- Rijksmuseum: zoom OpenSeadragon sul IIIF del museo (micr.io, IIIF 3); anche SMK (IIIF 2).
- Collezione con L e C, tasto M, opera del giorno, passeggiata con P (passa all'opera successiva) ed Esc, aree sicure (`viewport-fit=cover`, `env(safe-area-inset-*)`), `app-version` 19.
- Nessun errore JavaScript. In console resta un solo messaggio di risorsa, uguale su `main`: l'immagine di prova dell'Art Institute bloccata dal loro server (`ERR_BLOCKED_BY_RESPONSE.NotSameOrigin`), che l'app usa apposta per capire se saltare quella fonte.

## Decisioni
- Il servizio SPARQL di Wikidata il 15/11/2026 limitava a 1 richiesta/minuto gli script (disservizio in corso); dal browser rispondeva.
- In console resta un errore di risorsa preesistente: la prova immagine dell'Art Institute bloccata da Cloudflare (`ERR_BLOCKED_BY_RESPONSE`).

## Artisti esclusi
Nessuno: tutti gli 82 artisti richiesti o proposti sono morti entro il 1955 e risultano pittori su Wikidata.
