# Fulmini Site v5 — Design Spec

**Data:** 2026-06-04  
**Fonte contenuti:** `FULMINI_dispensa.pdf` (Parte A + Parte B)  
**Design di riferimento:** stesso sistema v4 (dark theme, CSS variables, HC carousel, scroll-snap)  
**Output:** `index.html` — singolo file HTML/CSS/JS, deploy su Cloudflare Pages

---

## Architettura

Singolo `index.html`. Tutto il CSS in `<style>`, tutto il JS in `<script>` in fondo al body. Nessuna dipendenza esterna tranne:
- Google Fonts CDN: Cormorant Garamond + JetBrains Mono
- Leaflet 1.9.4 CDN (mappa §7)

**Scroll engine:** `html { scroll-snap-type: y mandatory; overflow-y: scroll }`, `body { overflow: visible }`. Snap container è `html`, NON body.

**HC Carousel:** `translateX` pixel-based (`-current * window.innerWidth`). Mai percentuale. Guard `if(carousels[si]) return` per idempotenza.

**Canvas lazy-init:** `IntersectionObserver` (threshold 0.5) dispatcha `section-enter` CustomEvent. Ogni sezione ascolta `{once:true}`.

**Canvas guard pattern:** `if(canvas._init) return; canvas._init=true;` su ogni funzione init.

**Stato globale:**
```javascript
const TOTAL_SECTIONS = 10;
const state = {
  currentSection: 0,
  slideCurrent: new Array(TOTAL_SECTIONS).fill(0),
  slideTotal: [1, 3, 4, 5, 3, 3, 4, 5, 3, 3]
};
const carousels = {};
```

---

## CSS Design System

**Custom properties:**
```css
--bg: #0a0a0f
--surface: #0f0f18
--surface2: #14141f
--gold: #c9a84c
--blue: #4a9eff
--white: #e8e6e0
--muted: #666680
--border: rgba(201,168,76,.15)
--red: #ff4444
--green: #44cc88
--plasma: #7b2fff
--mono: 'JetBrains Mono','Courier New',monospace
```

**Tipografia:** Cormorant Garamond (body/headings), JetBrains Mono (labels/dati/nav)

**Layout components:** `.slide-split` (50/50), `.slide-full` (100%), `.section-header`, `.hc-wrap`, `.hc-track`, `.hc-slide`, `.hc-arrows`, `.hc-dots`, `.eyebrow`, `.reveal`/`.visible`

**Reveal animation:** `.reveal { opacity:0; transform:translateY(20px) }` → `.reveal.visible { opacity:1; transform:none }` con delay classi `.reveal-d1`–`.reveal-d4`

---

## Sezioni e Slide

### §0 — Hero (`data-section="0"`, 1 slide)

**Layout:** fullscreen, sfondo `--bg`, bolt SVG al centro con `@keyframes flashbolt`

**Contenuto:**
- Headline: "I FULMINI"
- Sottotitolo: "Fisica · Tipi · Geografia · Storia e curiosità"
- Stat strip: 30.000 K · 30.000 A · 300.000 km/s
- TOC: link `data-scroll-to` alle 9 sezioni
- CTA: ↓ Inizia

**JS:** hero IIFE che anima lo stat strip + `bindScrollToLinks()`

---

### §1 — Formazione (`data-section="1"`, 3 slide)

**Slide 1 — Cos'è un fulmine**
- Left: testo introduttivo dal PDF §A.1 (scarica elettrica transitoria, temperature, correnti)
- Right: canvas `charge-field-canvas` — campo elettrico visualizzato come linee di forza che si addensano tra nube (−) e suolo (+) fino alla rottura dielettrica (flash)

**Slide 2 — La collisione triboelettrica**
- Left: testo PDF §A.2 (collisione, effetto triboelettrico, graupel)
- Right: canvas `triboelec-canvas` — cristalli di ghiaccio (piccoli, +) e gocce d'acqua/graupel (grandi, −) che si scontrano con separazione cariche animata

**Slide 3 — La nube come condensatore**
- Left: testo PDF §A.2 (polarizzazione, carica indotta nel suolo, condensatore naturale)
- Right: canvas `capacitor-canvas` — nube con zone +/− colorate, frecce del campo elettrico verso suolo, suolo con segni + per induzione

**JS:** `initFormazioneSection` IIFE — 3 canvas init, `section-enter {once:true}`, `initCarousel(sec)`

---

### §2 — Meccanismo (`data-section="2"`, 4 slide + phase buttons + ▶ Play)

**Slide 1 — Stepped Leader** (fase 1)
- Left: testo PDF §A.3 fase 1 (precursore a gradini, 50 m/scatto, ~50 μs, 150–200 km/s, ramificazioni frattali) + bottoni fase `data-scarica-phase="1/2/3/4"` + `data-scarica-play`
- Right: canvas `leader-canvas` — stepped leader che scende a gradini frattali, blu elettrico

**Slide 2 — Upward Streamer** (fase 2)
- Left: testo PDF §A.3 fase 2 (sale dai punti conduttivi, poche cm, ionizzazione locale)
- Right: canvas `streamer-canvas` — streamer chiari che salgono da alberi/edifici verso il leader discendente

**Slide 3 — Connessione** (fase 3)
- Left: testo PDF §A.3 fase 3 (connessione a 30–50 m dal suolo, "cavetto di rame" virtuale di aria ionizzata)
- Right: canvas `connessione-canvas` — punto di giunzione luminoso tra leader e streamer, flash bianco

**Slide 4 — Return Stroke** (fase 4)
- Left: testo PDF §A.3 fase 4 (100.000 km/s = ⅓ velocità luce, luce accecante, corrente verso l'alto, è il fulmine che "vediamo")
- Right: canvas `return-canvas` — return stroke dorato risalente dal basso verso la nube, glow massimo, dissolvenza rapida

**Phase controls (su slide 1):** bottoni `data-scarica-phase="1/2/3/4"` navigano al slide corrispondente via carousel; `data-scarica-play` avanza automaticamente 1→2→3→4 con delay 1,5 s per fase

**JS:** `initScaricaSection` IIFE — `initScaricaCanvas` con _init guard, fasi 1-4, play sequenziale, `section-enter {once:true}`

---

### §3 — I Tipi (`data-section="3"`, 5 slide)

**Slide 1 — CG⁻ (Cloud-to-Ground negativo)**
- Left: testo PDF §A.4 (80% casi, base nube, 3–4 stroke, 20–30 kA)
- Right: canvas `cg-neg-canvas` — leader ramificato blu discendente, return stroke, rigenerazione ogni 3–5 s

**Slide 2 — CG⁺ (Cloud-to-Ground positivo)**
- Left: testo PDF §A.4 (10–20% casi, sommità nube, >300 kA, senza ramificazioni, responsabile incendi)
- Right: canvas `cg-pos-canvas` — leader dritto dorato, più potente

**Slide 3 — IC e CC**
- Left: testo PDF §A.5–6 (IC 70–75% di tutti i fulmini, CC "anvil crawlers")
- Right: due mini-canvas — `ic-canvas` (flash diffuso pulsante) + `cc-canvas` (arco orizzontale tra due nubi)

**Slide 4 — Ball Lightning + Elmo di Sant'Elmo**
- Left: testo PDF §A.7 (sfera 10–50 cm, 1–20 s, fisica dibattuta) + Elmo di S.Elmo (scarica corona)
- Right: `ball-canvas` (sfera fluttuante) + `elmo-canvas` (corona streaks su asta)

**Slide 5 — TLE (Transient Luminous Events)**
- Left: testo PDF §A.8 (sprite rosse 70–90 km, elve disco 90 km, blue jet cono 50 km)
- Right: canvas `tle-canvas` — atmosfera stratificata con layer labels; bottoni `data-tle="sprite/elve/jet"` + `data-tle="play"`

**JS:** `initTipiSection` IIFE — 7 canvas init, TLE phase controls, `section-enter {once:true}`

---

### §4 — Il Plasma (`data-section="4"`, 3 slide)

**Slide 1 — Cos'è il plasma**
- Left: testo PDF §B.1 (quarto stato della materia, ionizzazione, transizione di stato, bassissima resistività, emettitore di luce)
- Right: canvas `plasma-canvas` — particelle ionizzate (nuclei + e⁻ liberi) che si muovono caoticamente, ricombinazione con emissione fotoni (glow arancio-blu)

**Slide 2 — Termodinamica e il tuono**
- Left: testo PDF §B.2 (P = ρRT, 30.000 K → pressione 10–100 atm, espansione supersonica, onda d'urto → tuono, distanza udibile 20–25 km)
- Right: canvas `thunder-canvas` — canale sottile che esplode in cerchi concentrici (onda d'urto), colori da rosso (caldo) a blu (freddo)

**Slide 3 — Effetto pinch (Z-pinch)**
- Left: testo PDF §B.3 (legge di Ampère, campo magnetico B circolare, forza di Lorentz verso l'interno, strizione → densificazione → esplosione termica)
- Right: canvas `pinch-canvas` — corrente verticale con campo magnetico visualizzato come cerchi concentrici, forze frecce verso l'interno che pulsano

**JS:** `initPlasmaSection` IIFE — 3 canvas init, `section-enter {once:true}`

---

### §5 — Chimica & Nucleare (`data-section="5"`, 3 slide)

**Slide 1 — Chimica ad alta energia**
- Left: testo PDF §B.4 (O₃ dall'O₂ dissociato, NOₓ fertilizzante naturale, fulguriti da SiO₂ fuso)
- Right: layout info card con 3 "prodotti" del fulmine:
  - `O₃ — Ozono` (formula + descrizione)
  - `NOₓ — Ossidi di azoto` (formula + funzione fertilizzante)
  - `SiO₂ — Fulgurite` (descrizione roccia vetrosa)

**Slide 2 — TGF e raggi gamma**
- Left: testo PDF §B.5 (acceleratori di particelle naturali, elettroni relativistici, antimateria, rilevabili da satelliti)
- Right: canvas `tgf-canvas` — colonna temporalesca schematica, frecce di elettroni accelerati verso l'alto, emissione gamma (flash giallo) rilevata da satellite schematico

**Slide 3 — Parametri ed energia**
- Left: tabella dal PDF §B.6:
  | Caratteristica | Valore |
  |---|---|
  | Temperatura canale | ~30.000 K |
  | Corrente media | 20.000–30.000 A (picchi >200.000 A) |
  | Tensione | 100 milioni–1 miliardo di Volt |
  | Durata | ~0,2 s |
  | Energia per fulmine | 1–5 miliardi di Joule |
  | Energia utilizzabile | solo ~1 kWh |
- Right: contatori animati dei 3 valori più spettacolari (temperatura, corrente, energia)

**JS:** `initChimicaSection` IIFE — `initTGFCanvas`, `initCounters`, `section-enter {once:true}`

---

### §6 — Storia & Cultura (`data-section="6"`, 4 slide)

**Slide 1 — Mitologia**
- Layout: `.slide-full`, card grid con 5 carte:
  - Zeus/Giove — Grecia e Roma (PDF §A.9)
  - Thor — Mitologia norrena, martello Mjölnir (PDF §A.9)
  - Indra — Vedismo indù, vajra (PDF §A.9)
  - Tlaloc — Azteca, Tlaloque (PDF §A.9)
  - Raijin — Giappone (conoscenza generale, coerente con PDF)
- Luoghi colpiti considerati sacri (enelysion greco)

**Slide 2 — Franklin: l'esperimento**
- Left: testo PDF §A.10 (1752, configurazione aquilone+chiave+bottiglia di Leyden, fenomeno fisico induzione, isolamento seta)
- Right: SVG animato `franklin-svg` — pioggia SMIL, flash nel nube, scintille dalla chiave, braccio Franklin che si ritrae (SMIL calcMode discrete)
- Timeline laterale: 1600 Gilbert / 1729 Gray / 1745 Leyden jar / 1750 Royal Society / 1752 esperimento / 1753 Richmann (morte)

**Slide 3 — Il parafulmine**
- Left: testo PDF §A.11 (effetto punta, micro-ionizzazione, canalizzazione sicura, rame/acciaio verso terra)
- Right: canvas `parafulmine-canvas` — edificio con asta, cono di protezione ~45°, click per simulare fulmine (dentro cono → oro/deviato, fuori → rosso/impatto)

**Slide 4 — Tesla e la scienza moderna**
- Left: testo PDF §A.12 (bobina di Tesla, trasmissione wireless, studio fulmini artificiali; oggi: reti rilevamento, razzi con filo metallico, satelliti GOES-R)
- Right: layout con due card informative (bobina Tesla + satellite GOES-R)

**JS:** `initStoriaSection` IIFE — `initTimeline` (click expand), `initParafulmine` (canvas interattivo), `section-enter {once:true}`

---

### §7 — Nel Mondo (`data-section="7"`, 5 slide)

**Slide 1 — Distribuzione globale**
- Layout: `.slide-full` con mappa Leaflet dark theme
- `L.map('world-map')` + CartoDB dark tiles
- 4 `circleMarker` pulsanti: Maracaibo (rosso) / Congo (arancio) / Florida (giallo) / Italia-Appennino (blu)
- Overlay: eyebrow + h2 + legenda gradiente densità

**Slide 2 — Lago Maracaibo**
- Left: testo PDF §B.8 (230–260/km²/anno, 297 notti/anno, 28 scariche/minuto, Guinness WR, meccanismo Ande + brezza marina + lago caldo)
- Right: canvas `maracaibo-canvas` — lago di notte con flash periodici luminosi, stelle, profilo montuoso

**Slide 3 — Bacino del Congo**
- Left: testo PDF §B.9 (205 fulmini/km²/anno, RDC, Kabare/Kifuka, ITCZ + foresta pluviale + MCS)
- Right: stat cards (205/km²/anno, macro-regione più attiva del pianeta)

**Slide 4 — La Florida**
- Left: testo PDF §B.10 (1,2 milioni/anno, "Lightning Capital USA", brezze marine convergenti, mortalità più alta USA)
- Right: stat cards (1.2 milioni/anno, 50–70 scariche/km²/anno zona Tampa-Orlando)

**Slide 5 — L'Italia** ✨ **(nuovo)**
- Left: testo PDF §B.11 (1,5 milioni nube-suolo/anno, orografia complessa, Mediterraneo)
  - Tabella 3 macro-aree: Arco Alpino (estate) / Pianura Padana (tarda estate) / Tirreno+Appennino (autunno-inverno)
  - Paradosso Pianura Padana (convergenza aria umida + freddo alpino = supercelle)
  - Fulmini marittimi autunnali (Mediterraneo caldo + aria fredda atlantica)
- Right: canvas `italia-canvas` — mappa schematica Italia (outline SVG o canvas) con frecce di flusso atmosferico per le 3 zone, colorate per stagione

**JS:** `initMondoSection` IIFE — `initMap`, `initMaracaiboCanvas`, `initItaliaCanvas`, `initCarousel(sec)`, `section-enter {once:true}`

---

### §8 — Curiosità & Record (`data-section="8"`, 3 slide)

**Slide 1 — Record WMO**
- Left: testo PDF §B.12
  - Record lunghezza: 768 km (Texas–Mississippi, 2020)
  - Record durata: 17,1 s (Uruguay–Argentina, 2020)
  - Velocità stepped leader "lenta": ~200.000 km/h
- Right: due contatori animati giganti (768 km / 17,1 s) con unità

**Slide 2 — Velocità e il tuono**
- Left: testo PDF §B.12 (return stroke 100.000 km/s = ⅓ c; suono 343 m/s → 1 km ogni 3 s; udibile fino a 20–25 km; rifrazione acustica)
- Right: calcolatore interattivo — slider "secondi tra lampo e tuono" → km + badge rischio (≤10 s rosso/PERICOLO, ≤30 s giallo/ATTENZIONE, >30 s verde/MONITORARE)
  - Regola dal PDF: "dividi per 3"

**Slide 3 — Altre curiosità**
- Left: testo PDF §B.12 lista curiosità
- Right: 4 card:
  - Figure di Lichtenberg (fratali sulla pelle)
  - Fulmini su Giove e Saturno (migliaia di volte più potenti)
  - Fulmini vulcanici (attrito cenere/rocce)
  - Mito "non colpisce due volte" → FALSO (Empire State: ~23 volte/anno)

**JS:** `initCuriositaSection` IIFE — `initRecordCounters` (contatori animati), `initThunderCalc` (slider ÷3), `section-enter {once:true}`

---

### §9 — Sicurezza (`data-section="9"`, 3 slide)

**Slide 1 — All'aperto**
- Left: lista DO/DON'T dal PDF §B.13
  - ✓ Edificio solido / auto (gabbia Faraday)
  - ✓ Posizione di sicurezza (accovacciato, piedi uniti)
  - ✓ Allontanati da oggetti elevati 30+ m
  - ✗ Alberi isolati
  - ✗ Acqua aperta
  - ✗ Campi aperti / cime
  - ✗ Recinzioni metalliche
- Right: canvas `outdoor-canvas` — edificio (protetto) vs albero (pericoloso), zone di rischio visive

**Slide 2 — In casa + Step voltage**
- Left: lista in casa PDF §B.13
  - ✗ Apparecchi collegati alla presa
  - ✗ Doccia/bagno (tubi conducono)
  - ✓ Scaricatori di sovratensione
  - Warning box: **Step voltage** — corrente radiale nel suolo, mortale a 10–15 m dall'impatto
- Right: canvas `stepvoltage-canvas` — punto d'impatto con isopotenziali circolari, gradiente di colore (rosso al centro → verde a distanza), silhouette persona con frecce di corrente ai piedi

**Slide 3 — Rilevamento moderno**
- Left: testo PDF §B.14
  - LINET (600+ sensori EU, <200 m precisione, triangolazione TOA)
  - SIRF/ISPRA (sistema italiano, sensori EM distribuiti)
  - Blitzortung (citizen science, 2.000+ stazioni, open data)
  - Principio: differenza tempi di arrivo segnale → posizione esatta
- Right: iframe Blitzortung live (`id="blitz-iframe"`, src impostato da JS al `section-enter`)

**JS:** `initSicurezzaSection` IIFE — `initOutdoorCanvas`, `initStepVoltageCanvas`, `initBlitzortung`, `section-enter {once:true}`

---

## JS Marker System

```
// === SCROLL ENGINE ===       Task 2
// === CAROUSEL ENGINE ===     Task 3
// === HERO INIT ===           Task 4
// === FORMAZIONE CANVAS ===   Task 5
// === SCARICA CANVAS ===      Task 6
// === PLASMA CANVAS ===       Task 7
// === CHIMICA INIT ===        Task 8
// === TIPI CANVAS ===         Task 9
// === STORIA CANVAS ===       Task 10
// === MONDO MAP ===           Task 11
// === CURIOSITA INIT ===      Task 12
// === SICUREZZA INIT ===      Task 13
// === REVEAL INIT ===         Task 14
// === BOOTSTRAP ===           Task 14 (update)
```

---

## HTML Marker System

```
<!-- HERO CONTENT -->          Task 4
<!-- FORMAZIONE CONTENT -->    Task 5
<!-- SCARICA CONTENT -->       Task 6
<!-- PLASMA CONTENT -->        Task 7
<!-- CHIMICA CONTENT -->       Task 8
<!-- TIPI CONTENT -->          Task 9
<!-- STORIA CONTENT -->        Task 10
<!-- MONDO CONTENT -->         Task 11
<!-- CURIOSITA CONTENT -->     Task 12
<!-- SICUREZZA CONTENT -->     Task 13
```

---

## CSS Classes Richieste (nuovo rispetto a v4)

- `.plasma-glow` — per evidenziare canvas plasma con box-shadow colorato
- `.record-counter` — numero grande animato per record WMO
- `.curiosity-card` — card per le 4 curiosità §8 S3
- `.italia-table` — tabella macro-aree con colori stagione
- Riutilizzati da v4: `.timeline`, `.timeline-item`, `.card-grid`, `.card`, `.do-dont`, `.do-col`, `.dont-col`, `.warning-box`, `.thunder-calc`, `.risk-badge`, `.blitz-wrap`, `.flip-grid` (rimossa — non nel PDF)

---

## Dipendenze CDN

```html
<!-- Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,400&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
<!-- Leaflet CSS -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
<!-- Leaflet JS (prima del main script) -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

---

## Note implementative

1. **`slide-full` per slide §7 S1 (mappa) e §6 S1 (mitologia)** — richiede `position:relative` sul `.hc-slide` per i layer overlay della mappa
2. **Canvas Italia** — usare canvas 2D per disegnare outline semplificata della penisola italiana (path di coordinate) + frecce di flusso stagionali; non serve Leaflet qui
3. **Mappa Leaflet** — inizializzarla solo al `section-enter` (come v4) per evitare problemi di dimensionamento
4. **Franklin SVG** — usare SMIL `<animate>` per pioggia, flash, scintille, braccio (come v4, funziona bene)
5. **Phase controls §2** — stessa implementazione v4: `data-scarica-phase="N"` + `data-scarica-play`
6. **TLE phase controls §3 S5** — `data-tle="sprite/elve/jet/play"` come v4
7. **`prefers-reduced-motion`** — il REVEAL INIT salta tutte le animazioni canvas e marca subito `.visible`
