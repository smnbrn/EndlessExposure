# PROGRESS — branch `fable-estetica`

Lavoro sull'estetica di `index.html`. Lo stile esistente si mantiene (palette, Bodoni Moda, Instrument Sans, cartellino).

## Checklist

- [ ] **0. Base** — `index.html` allineato al file più recente del proprietario, screenshot «prima»
- [ ] **1. La home mostra i quadri** — un'opera simbolo per ogni sala in `#moveList`
- [ ] **2. Transizioni tra le viste** — View Transitions API, miniatura ↔ quadro
- [ ] **3. (non indicato)** — la richiesta parla di «tre punti» ma ne elenca due: il terzo resta da chiedere al proprietario
- [ ] **Chiusura** — verifica con Playwright (1440×900 e 390×844), console senza errori, `app-version` incrementata

## Stato

### 0. Base
- Il file ricevuto (`indexEE.html`, 15/11/2026) è più recente di `main`: ha la griglia giustificata delle opere (`makeWall`, `.jrow`) e le copertine degli artisti senza ritaglio. È la base di questo branch.

## Decisioni

- (nessuna ancora)

## Problemi aperti

- Terzo punto della richiesta non specificato.
