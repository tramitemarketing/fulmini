# Fulmini — Slide System + Interactive Tools Design

**Date:** 2026-06-03  
**File target:** `fulmini.html` (single-file, no build system)  
**Reference:** `../terremoti/index.html` + `../terremoti/css/global.css` + `../terremoti/js/main.js`

---

## Goal

1. Trasformare `fulmini.html` da pagina scrollabile normale a presentazione full-page slide identica nella struttura a terremoti (scroll-snap, nav dots, progress bar, cursor, keyboard nav).
2. Aggiungere uno strumento interattivo per ogni slide — canvas animati, mappe live, slider fisici, simulatori.
3. Carousel orizzontale: infrastruttura scritta e pronta ma **non attiva** — una riga per abilitarla.

---

## Slide Structure — 8 slide verticali

| Index | ID | Contenuto | Strumento interattivo |
|---|---|---|---|
| 0 | `s-hero` | Hero (titolo, bolt animati) | Canvas procedurale: fulmini frattali in loop sul paesaggio |
| 1 | `s-cosae` | Cos'è — intro + numbers strip | Cloud charge buildup: particelle ±, barra tensione, scarica |
| 2 | `s-formazione` | Come si forma — 4 fasi | Stepped Leader + Return Stroke canvas (4 fasi, pause/play) |
| 3 | `s-tipi` | Tipi — 6 card | Lightning type visualizer: click card → canvas animato del tipo |
| 4 | `s-fisica` | Fisica — tabella + myth box | Slider parametri (V↔A↔K) + Lichtenberg fractal generator |
| 5 | `s-franklin` | Franklin — SVG esperimento | EM pulse visualizer: cerchi radio post-scarica |
| 6 | `s-mondo` | Nel Mondo — hotspot + mitologia | Mappa live Blitzortung (WebSocket) + Heatmap densità storica |
| 7 | `s-protezione` | Protezione — 2 colonne | Safety scenario simulator (6 SVG) + calcolatore tuono |

---

## Fixed UI Elements

Tutti `position: fixed`, `z-index: 200`. Palette fulmini: `--gold: #f0c040`, `--blue: #3a8fff`.

| Elemento | Posizione | Comportamento |
|---|---|---|
| `#progress-bar` | left:0, top:0, w:3px | `height` = `currentIdx / 7 * 100%`, colore gold |
| `#nav-dots` | right:1.8rem, top:50% | 8 dot, active = gold + scale(1.8), cliccabili |
| `#slide-counter` | left:1.8rem, bottom:0.5rem | `"01 / 08"`, monospace, opacity bassa |
| `#fullscreen-btn` | top:1.4rem, right:1.4rem | SVG icon, border gold on hover |
| `#scroll-hint` | bottom:1.8rem, left:50% | Mouse animato + "Scorri", `.hidden` dopo primo scroll |
| `#cursor` + `#cursor-ring` | fixed, no pointer-events | Dot 10px gold + ring 32px con lag 0.12 lerp |

---

## CSS Changes

Aggiungere prima di `/* HERO */`:

```css
html {
  scroll-snap-type: y mandatory;
  overflow-y: scroll;
  scrollbar-width: none;
  cursor: none;
}
html::-webkit-scrollbar { display: none; }

.slide {
  height: 100vh; min-height: 100vh; max-height: 100vh;
  scroll-snap-align: start;
  scroll-snap-stop: always;
  overflow: hidden;
}
```

Ogni `<section>` acquisisce classe `.slide` e id corrispondente. Il `.hero` esistente diventa `.slide#s-hero`.

CSS carousel aggiunto ma con `.c-nav { display: none }`:
```css
.c-track { display: flex; width: 100%; transition: transform 0.6s cubic-bezier(0.4,0,0.2,1); }
.c-slide  { min-width: 100vw; height: 100vh; overflow: hidden; }
.c-nav    { display: none; position: absolute; bottom: 2rem; ... }
.c-dot    { width: 5px; height: 5px; ... }
.c-dot.active { background: var(--gold); }
```

---

## JavaScript Architecture

Un solo blocco `<script>` che sostituisce lo script esistente. Sezioni:

### 1. Cursor
RAF loop: `#cursor` segue il mouse (immediato), `#cursor-ring` con lerp 0.12.

### 2. Slide Tracking
`IntersectionObserver` threshold 0.5 → aggiorna `currentIdx`, chiama `updateUI(i)` e `onSlideEnter(i)`.

`onSlideEnter(i)` inizializza lo strumento della slide una sola volta (Set `triggered`):
```
0 → initHeroCanvas()
1 → initCloudCharge()
2 → initSteppedLeader()
3 → initTypeVisualizer()
4 → initPhysicsSliders(); initLichtenberg()
5 → initEMPulse()
6 → initBlitzortung(); initHeatmap()
7 → initSafetySimulator(); initThunderCalc()
```

### 3. Keyboard Navigation
```
ArrowDown / PageDown → slide successiva (verticale)
ArrowUp / PageUp     → slide precedente (verticale)
←→                   → no-op (delegato a carousel se attivo)
F                    → fullscreen toggle
R                    → reload con sessionStorage idx
```

### 4. Carousel Infrastructure (disabilitata)

```js
function initCarousel(sectionId, total) {
  // gestisce wheel, touch, keydown ←→ quando sezione attiva
  // naviga .c-track con translateX(calc(idx * -100vw))
  // aggiorna .c-dot, .c-counter, prev/next buttons
  // NON CHIAMATA — aggiungere in onSlideEnter per attivare
}
```

Per attivare carousel su S6 (mondo): aggiungere `initCarousel('s-mondo', 2)` in `onSlideEnter(6)` + cambiare `.c-nav { display: none }` → `display: flex` nel CSS.

### 5. Scroll Reveal
Il `IntersectionObserver` per `.reveal → .visible` viene **mantenuto separato** dallo slide tracking — classi e observer diversi, nessun conflitto.

---

## Interactive Tools — Dettaglio implementativo

### S0 · Hero Canvas procedurale
Sostituisce i bolt SVG statici. Canvas full-size, loop RAF:
- Ogni N ms genera un nuovo stepped leader (algoritmo ricorsivo, max 6 livelli di branch)
- Discesa a gradini → flash bianco → dissolvenza
- Colori: branch gold, return stroke bianco, glow blu

### S1 · Cloud Charge Buildup
Canvas con cumulonembo stilizzato:
- Particelle animate: + (oro, salgono) e − (blu, scendono)
- Barra tensione verticale a destra che sale con le particelle
- Quando tensione > threshold: parte fulmine (stepped leader canvas ridotto)
- Click: reset + rebuild

### S2 · Stepped Leader Canvas (4 fasi)
Canvas diviso con pannello sinistro testuale (fase attiva evidenziata) e destra canvas:
- Fase 1: leader invisibile scende a gradini (linea tratteggiata animata)
- Fase 2: streamer sale dal suolo
- Fase 3: connessione (flash)
- Fase 4: return stroke (linea luminosa che risale rapidamente)
- Button pause/play + navigazione fasi manuale

### S3 · Lightning Type Visualizer
Le 6 card esistenti acquisiscono `cursor: pointer`. Click → canvas centrale (destra) mostra animazione specifica:
- **CG**: leader verticale discende, return stroke
- **IC**: scarica orizzontale dentro forma nuvola
- **CC**: scarica tra due nuvole separate
- **Ball**: sfera luminosa che fluttua, poi esplode
- **Sprite**: forma medusa rossa sopra nuvola
- **Blue Jet**: getto conico blu verso l'alto

### S4 · Slider Parametri + Lichtenberg

**Slider (colonna sinistra):**
- Slider tensione 100MV → 1GV
- Corrente: `I = V / R_aria` (R costante) → aggiornata in tempo reale
- Temperatura: `T ∝ I²` → barra con colore da blu a bianco
- Marker "fulmine tipico" fisso sulla barra energia

**Lichtenberg (colonna destra):**
- Click su canvas → algoritmo DLA (Diffusion Limited Aggregation) o dielectric breakdown model
- Genera ramo frattale unico ogni click
- Colore: gold con glow blur
- Tasto "Reset" + "Salva PNG" (canvas.toDataURL)

### S5 · EM Pulse Visualizer
Sotto il SVG Franklin esistente (che rimane):
- Canvas con epicentro fulmine al centro
- Click "Avvia" → fulmine cade, poi cerchi concentrici EM si espandono
- Velocità c (istantanea visivamente), attenuazione con distanza (opacity)
- Label: "Questo impulso viene catturato dai sensori Blitzortung a centinaia di km"

### S6 · Blitzortung Live + Heatmap
**Slide principale: Mappa live**
- Leaflet mappa dark tile (CartoDB Dark Matter)
- WebSocket Blitzortung: `wss://ws1.blitzortung.org:8082/` (pubblica, no auth)
- Ogni strike: cerchio gold che si espande + svanisce in 2s
- Counter in alto: "N fulmini negli ultimi 5 min"
- Fallback se WS offline: replay di dati storici mock

**Carousel S6 slide 2: Heatmap densità (quando attivato)**
- Leaflet con GeoJSON regions colorato da densità (dataset statico incluso nel file)
- Marker interattivi: Maracaibo, Congo, Florida, Pianura Padana con popup

### S7 · Safety Simulator + Thunder Calc

**Safety Simulator (top):**
- 6 SVG scenari: albero isolato, auto, edificio, lago, collina, campo aperto
- Click → bordo colorato (🔴 pericolo alto / 🟡 medio / 🟢 sicuro) + testo consiglio
- Default: tutti neutri

**Thunder Distance Calc (bottom):**
- Slider 1–30 secondi
- Output: distanza km (`km = sec / 3`)
- Animazione: bolt sul lato sinistro, onda sonora (cerchi concentrici lenti) che si propaga

---

## What Does NOT Change

- Contenuto testuale di tutte le sezioni
- Palette CSS (`--bg`, `--gold`, `--blue`, ecc.)
- SVG Franklin con SMIL animations
- Font Google (Bebas Neue, Source Serif 4)
- La nav `<nav>` esistente (potrebbe essere nascosta in slide mode o rimossa — da decidere)

---

## Dipendenze esterne (già presenti in terremoti)

| Lib | Uso | Source |
|---|---|---|
| Leaflet.js | Mappe S6 | CDN o `lib/leaflet.js` locale |
| Leaflet.css | Stili mappa | CDN o `lib/leaflet.css` locale |

Tutto il resto: vanilla JS, nessuna dipendenza aggiuntiva.

---

## Spec Self-Review

- ✅ Nessun TBD o placeholder rimasto
- ✅ Carousel infrastruttura inequivocabile: funzione scritta, non chiamata; `.c-nav { display:none }`
- ✅ Ogni slide ha uno strumento definito con algoritmo/approccio concreto
- ✅ Slide tracking e reveal observer sono separati — nessun conflitto
- ✅ Blitzortung WS ha fallback esplicito
- ✅ Scope: un file HTML, Leaflet da CDN/locale, tutto vanilla JS
- ✅ La nav `<nav>` esistente: nascosta in slide mode (overlap con nav-dots)
