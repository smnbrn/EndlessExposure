# PROGRESS — branch `fable-estetica`

Lavoro sull'estetica di `index.html`. Lo stile esistente si mantiene (palette, Bodoni Moda, Instrument Sans, cartellino).

## Checklist

- [x] **0. Base** — `index.html` allineato al file più recente del proprietario, screenshot «prima»
- [x] **1. La home mostra i quadri** — un'opera simbolo per ogni sala in `#moveList`
- [x] **2. Transizioni tra le viste** — View Transitions API, miniatura ↔ quadro
- [ ] **3. (non indicato)** — la richiesta parla di «tre punti» ma ne elenca due: il terzo resta da chiedere al proprietario
- [x] **Chiusura** — verifica con Playwright (1440×900 e 390×844), `app-version` da 17 a 18

Il branch **non è unito a `main`**: la versione pubblicata su GitHub Pages non cambia finché non lo decide il proprietario.

## Stato

### 0. Base (fatto)
- Il file ricevuto (`indexEE.html`, 15/11/2026) è più recente di `main`: ha la griglia giustificata delle opere (`makeWall`, `.jrow`) e le copertine degli artisti senza ritaglio. È la base di questo branch.

### 1. La home mostra i quadri (fatto)
- Ogni riga di `#moveList` ha a sinistra una miniatura quadrata dell'opera simbolo (`.th`): 52 px su desktop, 48 px su telefono. Il posto è riservato da subito, l'immagine entra in dissolvenza: niente salti.
- Desktop: la riga resta alta 72 px (era 70–74); la linea del tempo ha la stessa lettura di prima (asse e barre allineati, una colonna in più a sinistra).
- Telefono: riga da 100–104 px a 69–71 px. Nome su una riga sola, sotto artisti e anni, sotto ancora la barra del tempo (senza etichetta: gli anni sono scritti a destra).
- Scelta dell'opera: la più nota (numero di voci di Wikipedia, lo stesso criterio di «Le più note») tra i dipinti degli artisti della sala. Le righe della risposta passano da `wdRows`, come le opere di un artista.
- Immagini: `loading="lazy"`, larghezza 250 px da Commons, caricate solo quando la riga sta per entrare nello schermo (`IntersectionObserver`).
- Opera del giorno: su schermi larghi il posto di quadro e cartellino è riservato (`--today-h`), così quando l'immagine arriva la pagina sotto non si sposta (prima: salto misurato 0,067; ora 0).

### 2. Transizioni tra le viste (fatto)
- `onHash` avvia `document.startViewTransition` a ogni cambio di vista (pianta ↔ sala ↔ artista ↔ opera, collezione). Da un'opera all'altra resta la dissolvenza di `paintWork`.
- Elemento condiviso `hero`: il nome è assegnato da codice solo ai due elementi coinvolti e tolto a fine passaggio (prima era fisso su `#paint`). Andata: miniatura toccata → quadro. Ritorno: quadro → la sua miniatura nella parete (o nell'opera del giorno, tornando alla pianta).
- Direzione: entrando più a fondo la vista nuova sale dal basso (`data-vt="fwd"`), tornando scende dall'alto (`back`); tra viste dello stesso livello solo dissolvenza.
- Durate: 300 ms (vista che esce), 360 ms (vista che entra), 340 ms (barra in alto), 420 ms (quadro). Curva `--ease` ovunque.
- `prefers-reduced-motion`: nessun passaggio avviato (provato: 0 chiamate). Senza l'API: cambio vista normale, nessun errore (provato togliendo `startViewTransition`).

### Chiusura (fatto)
- Prove automatiche con Playwright e Chromium, rete attiva, a 1440×900 e 390×844:
  - 9 passaggi tra viste per formato: elemento condiviso presente dove previsto, durate e curva giuste, nomi tutti tolti alla fine;
  - 23 controlli di non-regressione, tutti superati: hash `#m/`, `#a/`, `#w/`, `#c/`; tasti R, C, M, P, I, Z, L, F, frecce, Esc, Invio nella ricerca; indietro del browser; zoom (anche OpenSeadragon sulla Notte stellata, 16.000 px); passeggiata; collezione in `localStorage`; inclinazione del quadro; opera del giorno; «Riprendi da»; aree sicure (`env(safe-area-inset-*)`, `viewport-fit=cover`);
  - salti di layout nella home: 0 su desktop (prima 0,067), 0,006 su telefono;
  - nessuno scorrimento orizzontale, nessun errore JavaScript.
- `app-version`: 17 → 18.

## Decisioni

- **Opere simbolo scritte nel file (`SYM_SEED`) + Wikidata come riserva.** Una sola interrogazione per tutti i 94 artisti va in timeout (504 dopo 60 s); una per sala risponde in 1–3 s la prima volta, a volte in più di 9 s. Per non far aspettare la home, le 16 scelte di oggi (risultato di quella stessa interrogazione al 15/11/2026) sono scritte nel file. L'interrogazione resta, con cache in `localStorage` (`ee.symbols.v1`, 30 giorni), per le sale nuove e per le immagini che non esistono più: provato togliendo un file, 1 richiesta e poi cache.
- **Una coda a parte (`sym`) con al massimo due richieste insieme e 9 s di tempo massimo**: non rallenta la coda `wd` degli artisti e resta sotto il limite di Wikidata.
- **`SYM_SKIP`**: «L'origine du monde» è la più nota del Realismo ma non è adatta a fare da insegna nella pianta; al suo posto la seconda, «Funerale a Ornans».
- **Miniatura quadrata con ritaglio** (`object-fit: cover`, centro un po' in alto): righe tutte uguali e linea del tempo allineata; il quadro intero si vede entrando.
- **Telefono: tolto «N artisti» dalla riga** per stare nei 72 px; al suo posto gli anni.
- **Opera del giorno un po' più bassa su desktop** (al massimo 380 px invece di 430) per riservarle il posto senza allungare la home.
- **Il nome `hero` va al pulsante del quadro (`#paintBtn`), non all'immagine**: così il quadro vola insieme alla sua ombra; con la sola immagine restava per un attimo il riquadro dell'ombra vuoto.
- **La barra in alto ha un nome suo (`topbar`) solo durante il passaggio**: resta ferma mentre la pagina sotto si muove. Per quei 340 ms i suoi pulsanti perdono la sfocatura dello sfondo (dietro c'è la parete scura: non si nota).
- **Se i dati dell'opera non sono ancora sul dispositivo** (es. «Riprendi da» a cache vuota) il passaggio non tiene fermo lo schermo: dopo 280 ms prosegue e la vista cambia quando arrivano i dati, senza animazione.
- **Sala → artista senza elemento condiviso**: solo il movimento della pagina. La copertina dell'artista potrebbe diventare la prima opera della parete, ma non era richiesto.

## Problemi aperti

- Terzo punto della richiesta non specificato.
- Errori di rete in console che c'erano già e non dipendono da queste modifiche: immagini dell'Art Institute of Chicago bloccate (`ERR_BLOCKED_BY_RESPONSE.NotSameOrigin`), ogni tanto 429/502 di Wikidata sotto carico (durante le prove hanno lasciato alcune copertine su «Carico…» finché non si è sbloccata) e un errore CORS dell'API di Cleveland.
- Il passaggio miniatura → quadro è stato provato solo in Chromium. Safari e Firefox recenti hanno la stessa API ma non sono stati provati qui.
- Sulla porta 8765 del PC c'è un altro server locale (non di questo lavoro) che serve la versione vecchia: le prove si sono fatte sulla 8791.
