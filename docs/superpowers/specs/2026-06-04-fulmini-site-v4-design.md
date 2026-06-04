# Fulmini — Site v4 Design Spec

**Data:** 2026-06-04
**Target:** `index.html` (root, single-file)
**Fonte contenuti:** `FULIMINI.docx`
**Stile di riferimento:** tramitemarketing/terremoti (design shell only — fonts, palette, carousel, fixed UI)

---

## 1. Architettura

Single-file HTML statico. Nessun build step. Vanilla JS + Leaflet CDN.

**Scroll engine:**
- `scroll-snap-type: y mandatory` su `html` (MAI su `body`)
- `body { overflow: visible }` — critico per scroll-snap
- `.section { height: 100vh; scroll-snap-align: start; scroll-snap-stop: always; }`

**Carousel orizzontale (HC) per ogni sezione:**
- `.hc-wrap > .hc-track > .hc-slide[]`
- `translateX(n * -100vw)` via JS (pixel-based per evitare drift)
- Frecce ←→ da tastiera, click frecce UI, swipe touch
- Dots/counter mostrano slide corrente

**Canvas animations:**
- Tutti lazy: inizializzati solo al primo `onSectionEnter(i)` (IntersectionObserver)
- Protetti da flag `initialized` per evitare doppio init
- `canvas.width = canvas.offsetWidth; canvas.height = canvas.offsetHeight` espliciti al resize

**Fonts:** Cormorant Garamond (corpo/titoli) + JetBrains Mono (label/dati/nav) — Google Fonts CDN
**Map:** Leaflet 1.9.4 CDN + CartoDB Dark Matter tiles

---

## 2. Design System

### CSS Custom Properties
```css
:root {
  --bg: #0a0a0f;
  --surface: #0f0f18;
  --surface2: #14141f;
  --gold: #c9a84c;
  --blue: #4a9eff;
  --white: #e8e6e0;
  --muted: #666680;
  --border: rgba(201,168,76,0.15);
  --red: #ff4444;
  --green: #44cc88;
  --plasma: #7b2fff;
}
```

### Tipografia
- `font-family: 'Cormorant Garamond', Georgia, serif` — tutto il testo corpo e titoli sezione
- `font-family: 'JetBrains Mono', monospace` — label, dati numerici, nav, counter, eyebrow
- Titoli hero: `font-size: clamp(5rem, 12vw, 11rem); font-weight: 300; letter-spacing: -0.02em`
- Titoli sezione: `font-size: clamp(2.5rem, 5vw, 4.5rem); font-weight: 300`
- Body: `font-size: clamp(1rem, 1.4vw, 1.2rem); line-height: 1.75`
- Label/eyebrow: `font-family: JetBrains Mono; font-size: 0.7rem; letter-spacing: 0.15em; text-transform: uppercase; color: var(--gold)`

### Layout Classes
- `.slide-split` — flex row 50/50, sinistra testo, destra canvas/grafica
- `.slide-left` — padding 4rem 2rem 4rem 5.5rem
- `.slide-right` — flex center, overflow hidden
- `.slide-center` — flex column centrato, padding 3rem 5.5rem, text-align center
- `.slide-full` — nessun padding aggiuntivo (per mappa, grid full-width)

### Reveal animation
Tutti i paragrafi, titoli e card hanno classe `.reveal`:
```css
.reveal { opacity: 0; transform: translateY(24px); transition: opacity 0.6s ease, transform 0.6s ease; }
.reveal.visible { opacity: 1; transform: none; }
```
Triggerate da `IntersectionObserver` al 20% di visibilità.

---

## 3. Fixed UI (identico a terremoti)

```html
<div id="progress-bar"></div>
<div id="nav-dots"><!-- un dot per sezione --></div>
<div id="slide-counter">01 / 05</div>
<button id="fullscreen-btn">⛶</button>
<div id="scroll-hint">↓ scorri</div>
```

- **Progress bar**: larghezza % = sezione corrente / totale sezioni
- **Nav dots**: click → scroll a quella sezione; dot attivo evidenziato in gold
- **Slide counter**: `JetBrains Mono`, bottom-right, aggiornato dal carousel
- **Keyboard**: `ArrowUp`/`ArrowDown` → sezione prev/next; `ArrowLeft`/`ArrowRight` → slide HC prev/next; `F` → fullscreen
- **Scroll hint**: freccia animata in hero, scompare dopo primo scroll

---

## 4. Sezioni e Contenuto

### Sezione 0 — Hero `#hero`
**Layout:** split: sinistra titolo + nav guide, destra TOC
**Contenuto sinistra:**
- Eyebrow: `FISICA DEI FULMINI`
- Titolo: `FULMINE` (monumentale)
- Sottotitolo: "Dalla separazione di carica al canale plasma da 30.000°C"
- Nav guide: `← → slide` / `↑ ↓ sezione`
- CTA: `↓ Inizia`

**Contenuto destra (TOC):**
```
01  Formazione
02  La Scarica
03  Fisica Estrema
04  I Dati
05  Tassonomia
06  L'Esperimento
07  Nel Mondo
08  Mito e Storia
09  Sicurezza
```

**Background:** SVG bolt animato con `@keyframes flashbolt` (opacity flash + glow)
**Nessun carousel** — sezione singola

---

### Sezione 1 — Formazione `#formazione`
**Titolo sezione:** COME NASCE UN FULMINE
**Slide count:** 4

**Slide 1 — Il temporale**
- Titolo: "Il cumulonembo"
- Testo: convezione intensa, colonna d'aria calda umida che sale fino a 15 km, formazione dei cristalli di ghiaccio
- Grafica: canvas statico con silhouette nuvola e frecce updraft/downdraft

**Slide 2 — Separazione di carica (INTERATTIVA)**
- Titolo: "La triboelettricità"
- Testo: urto tra cristalli di ghiaccio (↑) e graupel (↓), trasferimento di carica, dipolo elettrico
- **Interattivo:** slider "Intensità Updraft" (0–100%) → canvas anima particelle che si separano, zona + in alto e − in basso si intensificano

**Slide 3 — Il campo elettrico**
- Titolo: "Il campo da 100.000 V/m"
- Testo: campo elettrico tra base nube (−) e suolo (+), ionizzazione progressiva dell'aria, breakdown elettrico a ~3 MV/m

**Slide 4 — Phase list interattiva**
- Titolo: "Le 4 fasi"
- **Bottoni fase:** [1 Separazione] [2 Accumulo] [3 Ionizzazione] [4 Scarica]
- **▶ Play** → animazione sequenziale automatica di tutte le fasi (2s per fase)
- Ogni fase: icona SVG + testo descrittivo che compare

---

### Sezione 2 — La Scarica `#scarica`
**Titolo sezione:** LA SCARICA
**Slide count:** 5

**Slide 1 — Stepped Leader**
- Titolo: "Il leader a gradini"
- Testo: ionizzazione a step di 50m ogni 50μs, canale invisibile, si ramifica cercando il percorso di minor resistenza
- **Canvas:** leader discendente con biforcazioni stocastiche (algoritmo random-walk con bias downward). **Bottoni step:** [Step 1] [Step 2] ... [Step N] + [▶ Play]. Click → restart con nuova geometria casuale.

**Slide 2 — Lo streamer ascendente**
- Titolo: "Il canale di connessione"
- Testo: quando il leader è a ~50m dal suolo, streamer ascendente parte dagli oggetti elevati (alberi, edifici, parafulmine), connessione → circuito chiuso
- Canvas: canvas indipendente (stessa palette del leader canvas), mostra leader già a metà discesa + streamer ascendente che sale, animazione connessione con flash al punto di incontro

**Slide 3 — Return stroke**
- Titolo: "Il ritorno della carica"
- Testo: 200 MA/s di dI/dt, propagazione verso l'alto a 1/3 c, durata ~70μs, temperatura 30.000K, pressione 10 atm, il flash visibile
- Canvas: flash bianco intenso che risale il canale, onda di pressione radiale

**Slide 4 — Multipli stroke**
- Titolo: "I colpi multipli"
- Testo: dopo il return stroke il canale rimane ionizzato, dart leader può rifluire: mediamente 3–4 stroke per fulmine, fino a 26 registrati
- Grafica: timeline animata con impulsi multipli

**Slide 5 — Il plasma**
- Titolo: "Il canale plasma"
- Testo: 1–2 cm di diametro, 30.000K (5× la superficie solare), emissione UV/ottica/IR/radio, durata ~200ms totale
- **Canvas Z-pinch:** corrente nel canale + campo magnetico che lo stringe (frecce circolari), colore canale da rosso → bianco al picco. Bottoni: [Corrente] [Campo B] [Compressione] [Picco] + [▶ Play]

---

### Sezione 3 — Fisica Estrema `#fisica`
**Titolo sezione:** FISICA ESTREMA
**Slide count:** 4

**Slide 1 — Temperatura e pressione**
- Titolo: "30.000°C"
- Testo: confronto: superficie solare 5.500°C, nucleo Terra 6.000°C; onda di pressione sonica → tuono
- **Stat counter animato:** temperatura da 0 → 30.000, velocità da 0 → 100.000 km/s

**Slide 2 — TGF: raggi gamma dai fulmini**
- Titolo: "Terrestrial Gamma-ray Flashes"
- Testo: scoperta BATSE/Compton 1994, emissione gamma e antimateria (positroni) verso lo spazio, bremsstrahlung da elettroni relativistici, durata < 1ms
- **Canvas TGF:** nuvola temporalesca con sciame di particelle (e⁻ blu, γ giallo, e⁺ rosso) che si propagano verso lo spazio. Bottoni: [Elettroni] [Gamma] [Positroni] [Sciame] + [▶ Play]

**Slide 3 — Chimica del fulmine**
- Titolo: "Il laboratorio atmosferico"
- Testo: N₂ + O₂ → NO + NO₂ (NOx, fertilizzante naturale), O₂ + O → O₃ (ozono), SiO₂ fuso → fulguriti (vetro naturale), ~10⁹ J per fulmine
- **Barra spettrale:** spettro ottico animato con righe di emissione (N₂, O, H, Na), hover su riga → elemento + lunghezza d'onda

**Slide 4 — Elettromagnetismo**
- Titolo: "Il Z-pinch atmosferico"
- Testo: auto-compressione magnetica del canale plasma (effetto Bennett), ∇×B = μ₀J, campo ~200T nel canale, sferics (impulsi ELF) che si propagano nella cavità Terra-ionosfera
- Formula: `J × B → pressione radiale inward`

---

### Sezione 4 — I Dati `#dati`
**Titolo sezione:** I NUMERI
**Slide count:** 3

**Slide 1 — Numbers strip**
- **Stat counters** (contano da 0 all'entrata nel viewport):
  - `40` fulmini/secondo sul pianeta
  - `1.4 miliardi` fulmini/anno
  - `30.000°C` temperatura canale
  - `300 kA` corrente picco massima
  - `20 kA` corrente media
  - `100.000 km/s` velocità return stroke
  - `0.2 s` durata media fulmine
  - `1-2 cm` diametro canale

**Slide 2 — Data table**
| Parametro | Valore tipico | Record |
|-----------|--------------|--------|
| Corrente | 20 kA | 300 kA |
| Tensione | 100–300 MV | 1 GV (stima) |
| Energia | 1–5 GJ | — |
| Temperatura | 27.000°C | ~30.000°C |
| Durata totale | 0.2 s | >1 s |
| N° stroke | 3–4 | 26 |
| Lunghezza | 2–3 km | >100 km (orizzontale) |

**Slide 3 — Record mondiali**
- Il fulmine più lungo: 768 km (USA, 2020, certificato WMO)
- Il più lungo in durata: 17.1 secondi (Uruguay-Argentina, 2019)
- Il luogo con più fulmini: Lago Maracaibo, Venezuela (280 notti/anno)
- L'Italia: Appennino Tosco-Emiliano, ~20 fulmini/km²/anno

---

### Sezione 5 — Tassonomia `#tipi`
**Titolo sezione:** TIPI DI FULMINE
**Slide count:** 5

**Slide 1 — CG⁻ (Cloud-to-Ground negativo)**
- 80% di tutti i fulmini CG
- Leader negativo dalla base della nube
- Card con mini-animazione CSS: freccia che scende con ramificazioni

**Slide 2 — CG⁺ (Cloud-to-Ground positivo)**
- 10–20% dei CG, molto più potente (>300 kA)
- Parte dalla sommità della nube (zona +)
- Pericolosità maggiore: durata più lunga, danno meccanico > incendio
- Card con animazione: freccia lunga dall'alto senza ramificazioni

**Slide 3 — IC, CC e Intracloud**
- IC (Intra-Cloud): il più frequente in assoluto (70% di tutti i fulmini)
- CC (Cloud-to-Cloud): tra nubi diverse
- Animazioni CSS: flash interno alla nube (IC) e arco tra due nubi (CC)

**Slide 4 — Ball Lightning e Curiosità ottiche**
- Ball Lightning: sfera luminosa 10–50 cm, durata 1–20s, fisica ancora dibattuta
- Elmo di Sant'Elmo: corona discharge su superfici appuntite (alberi, antenne)
- Animazioni CSS: sfera pulsante (ball lightning), alone verde-blu (elmo)

**Slide 5 — TLE: Transient Luminous Events**
- Sprite: colonne rosse sopra temporali (80 km quota)
- Elve: disco luminoso espansivo (90 km, durata 1ms)
- Blue jet: getto blu dalla sommità nube (50 km)
- **Canvas TLE:** sezione verticale dell'atmosfera (nube → troposfera → stratosfera → mesosfera) con TLE animati nelle zone corrette. Bottoni: [Sprite] [Elve] [Blue Jet] + [▶ Play tutto]

---

### Sezione 6 — L'Esperimento `#franklin`
**Titolo sezione:** L'ESPERIMENTO DI FRANKLIN
**Slide count:** 3

**Slide 1 — Il contesto**
- Titolo: "Filadelfia, 1752"
- Testo: prima metà '700, il fulmine è fenomeno divino o magico; Franklin ipotizza natura elettrica; sfida il consenso accademico europeo
- Timeline orizzontale: 1600 (Gilbert, De Magnete) → 1729 (Gray, conduzione) → 1745 (Leyden jar) → 1752 (Franklin) → 1753 (Richmann, folgorazione mortale)

**Slide 2 — L'aquilone (SVG animato)**
- Titolo: "L'aquilone e la chiave"
- SVG: cielo nuvoloso, aquilone con filo, chiave appesa, Franklin che tiene il filo
- **SMIL animations:** lampi nel cielo, scintille dalla chiave, mano di Franklin che si ritrae
- Testo: la carica scorre lungo il filo bagnato, scintille dalla chiave, bottiglia di Leyden caricata → prova che il fulmine è elettricità

**Slide 3 — Il parafulmine**
- Titolo: "L'eredità: il parafulmine"
- Testo: 1753, primo parafulmine installato; principio: asta conduttrice connessa a terra offre percorso preferenziale e scarica gradualmente il campo; cono di protezione ~45°
- **Canvas parafulmine:** edificio con asta, click per "generare fulmine" → stepped leader scende → deviato verso parafulmine → scarica a terra; mostra cono di protezione

---

### Sezione 7 — Nel Mondo `#mondo`
**Titolo sezione:** NEL MONDO
**Slide count:** 4

**Slide 1 — Mappa hotspot (Leaflet)**
- Mappa a schermo pieno, CartoDB Dark Matter tiles
- Pin pulsanti (CSS animation) sui 4 hotspot principali
- Click su pin → popup con dati chiave
- Legenda: gradiente colore = densità fulmini (verde basso → rosso alto)

**Slide 2 — Lago Maracaibo**
- Titolo: "Il lampo del Catatumbo"
- 280 notti/anno, fino a 280 fulmini/ora, visibile a 400 km, usato come faro dai navigatori coloniali
- Canvas: silhouette del lago di notte con flash periodici (rate variabile)

**Slide 3 — Congo Basin e Florida**
- Congo: massima densità assoluta (~158 fulmini/km²/anno), convergenza intertropicale
- Florida: "lightning capital" USA, 1.2 milioni/anno, cultura della protezione e record mortalità
- Two-column layout con stat per ciascuno

**Slide 4 — Italia**
- Appennino Tosco-Emiliano: ~20 fulmini/km²/anno
- Rete di rilevamento LINET (600+ sensori in Europa)
- Embed live: Blitzortung.org iframe (fulmini in tempo reale sull'Europa)

---

### Sezione 8 — Mito e Storia `#mitologia`
**Titolo sezione:** MITO E STORIA
**Slide count:** 4

**Slide 1 — Le divinità del fulmine**
- Card grid: Zeus/Giove (Grecia/Roma), Thor (Norrena), Indra (Vedica), Tlaloc (Azteca), Raijin (Giapponese)
- Ogni card: nome, cultura, attributi, testo breve
- Hover: card si solleva con glow dorato

**Slide 2 — Il fulmine come simbolo**
- Titolo: "Fulmine come potere"
- Attributo divino nelle culture di tutto il mondo → associazione con regalità e guerra
- Simbolismo moderno: elettricità (logo standard), velocità (sport), pericolo

**Slide 3 — Storia della comprensione scientifica**
- Timeline verticale CSS: 1600 Gilbert → 1729 Gray → 1745 Musschenbroek (Leyden jar) → 1752 Franklin → 1836 Peltier → 1900 Wilson (CTR) → 1994 BATSE/TGF → 2020 record WMO
- Ogni nodo: click → espande con dettaglio

**Slide 4 — Debunking**
- **Myth flip cards** (click per girare):
  1. "Il parafulmine attira i fulmini" → FALSO: il parafulmine intercetta e scarica in sicurezza
  2. "La gomma della macchina isola" → FALSO: la protezione è la gabbia di Faraday del telaio metallico
  3. "Sotto un albero si è al sicuro" → FALSO: l'albero è conduttore, pericolo passo (step voltage)
  4. "Il fulmine non colpisce due volte lo stesso punto" → FALSO: l'Empire State Building è colpito ~20 volte/anno
  5. "In acqua si è al sicuro dal fulmine" → FALSO: l'acqua conduce, corrente laterale pericolosissima
  6. "I fulmini orizzontali sono meno pericolosi" → FALSO: stessa corrente, range > 10 km

---

### Sezione 9 — Sicurezza `#sicurezza`
**Titolo sezione:** SICUREZZA
**Slide count:** 4

**Slide 1 — La regola 30/30**
- Titolo: "30 / 30"
- Se tra lampo e tuono < 30 secondi → sei in pericolo (< 10 km)
- Aspetta 30 minuti dopo l'ultimo tuono prima di uscire
- Thunder calculator: inserisci secondi → mostra distanza in km + livello rischio (verde/giallo/rosso)

**Slide 2 — Risk Meter**
- Titolo: "Sei al sicuro?"
- 5 domande rapide (checkbox):
  1. Sei all'aperto?
  2. Vicino ad alberi o pali alti?
  3. In acqua o vicino ad acqua?
  4. Su un campo aperto o altura?
  5. Stai usando strumenti metallici lunghi?
- Score 0–5 → semaforo: Verde (0–1), Giallo (2–3), Rosso (4–5) con consigli specifici

**Slide 3 — Cosa fare / non fare**
- Layout two-column: ✓ verde / ✗ rosso
- DO: cerca rifugio in edificio solido, auto metallica (chiudi finestrini), posizione di sicurezza (accovacciato, piedi uniti, mani sulle orecchie)
- DON'T: sotto alberi isolati, vicino a recinzioni metalliche, in acqua aperta, su picchi e creste, telefono fisso durante il temporale

**Slide 4 — Rilevamento moderno**
- Titolo: "Monitoraggio in tempo reale"
- LINET (Lightning Detection Network): >600 sensori in Europa, localizzazione a <200m
- Blitzortung.org: rete citizen science, 2000+ stazioni mondiali
- Satelliti (GOES-R GLM, Meteosat LI): copertura globale
- Applicazioni: aviazione, protezione civile, ricerca, allerta precoce

---

## 5. Animazioni Specifiche — Specifiche Tecniche

### Canvas: Stepped Leader (`#scarica` slide 1)
```
- Canvas 100% width/height del .slide-right
- Algoritmo: random-walk con bias downward (0.7 down, 0.15 left, 0.15 right per step)
- Biforcazioni: ogni step ha P=0.3 di generare ramo secondario (max depth 2)
- Colore: rgba(100, 180, 255, alpha) con alpha che decresce per rami secondari
- Glow: shadowBlur=20, shadowColor=#4a9eff
- Step mode: bottoni [1][2][3][4][5] aggiungono step progressivamente
- Play mode: requestAnimationFrame, 50ms per step
- Al termine: flash bianco (opacity 0→1→0 in 200ms) = return stroke
```

### Canvas: Separazione di Carica (`#formazione` slide 2)
```
- Nuvola statica in alto (SVG o canvas path)
- Particelle: 50 cristalli (+ blu) in zona alta, 50 graupel (- arancio) in zona bassa
- Slider updraft: forza verticale che separa sempre più le particelle
- Linee di campo: disegnate con densità proporzionale allo slider
- Heatmap sfondo: colore rosso (alta carica -) in basso, blu (alta carica +) in alto
```

### Canvas: Z-Pinch (`#scarica` slide 5)
```
- Vista sezione trasversale del canale (cerchio centrale)
- Frecce corrente verticale (rosse) lungo il canale
- Frecce campo magnetico B circolari (blu)
- Forza J×B radiale inward (frecce verdi che si accorciano)
- Colore canale: step 1=#ff4400, step 2=#ff6600, step 3=#ffaa00, step 4=#ffffff
- Phase buttons: [Corrente] mostra solo frecce J, [Campo B] aggiunge B, [Compressione] aggiunge J×B, [Picco] canale bianco max glow
```

### Canvas: TGF (`#fisica` slide 2)
```
- Vista verticale: suolo (bottom) → troposfera → stratosfera (top)
- Nuvola temporalesca a 12km
- Elettroni (e⁻, punti blu, verso alto) da zona leader
- Fotoni gamma (γ, linee gialle, verso alto)
- Positroni (e⁺, punti rossi, verso alto) — antimateria
- Phase buttons: [e⁻] [γ] [e⁺] [Sciame completo] + [▶ Play]
```

### Canvas: TLE (`#tipi` slide 5)
```
- Vista verticale atmosfera: nube temporale (0km) → 100km quota
- Sprite: colonne rosse ramificate a 70-90km, trigger dopo CG+
- Elve: disco espansivo a 90km (cerchio che si allarga da centro, fade out)
- Blue jet: cono blu stretto dalla sommità nube a 40-50km
- Phase buttons: [Sprite] [Elve] [Blue Jet] + [▶ Play tutti in sequenza]
```

### Canvas: Parafulmine (`#franklin` slide 3)
```
- Edificio silhouette con asta in cima
- Cono di protezione: linee tratteggiate 45° dall'asta, area colorata semi-trasparente
- Click su area fuori cono: stepped leader discende in quella X, colpisce edificio → animazione danno (vibrazione, effetto flash rosso)
- Click su area dentro cono: stepped leader discende → deviato verso asta → scarica a terra (linea gialla lungo l'asta → terra)
- Reset automatico dopo 3s
```

### SVG: Franklin Aquilone (`#franklin` slide 2)
```
- SVG statico con SMIL <animate>
- Elementi: cielo (gradient), nubi, pioggia (linee animate), aquilone (rettangolo inclinato), corda (path), chiave (rect), Franklin (silhouette)
- Animate: 
  - Flash nube: opacity 0→1→0, dur=0.3s, repeatCount=indefinite, begin=random
  - Scintilla chiave: stroke-opacity 0→1→0 su linee piccole dalla chiave, dur=0.2s
  - Pioggia: translateY animato, dur=1s
```

---

## 6. Interattivi — Specifiche Tecniche

### Thunder Calculator
```html
<div class="thunder-calc">
  <label>Secondi tra lampo e tuono:</label>
  <input type="range" min="1" max="60" value="10" id="thunder-slider">
  <span id="thunder-val">10s</span>
  <div id="thunder-result">
    <span id="thunder-km">3.4 km</span>
    <div id="thunder-risk" class="risk-yellow">ATTENZIONE</div>
  </div>
</div>
```
Logica: `km = secondi / 3` (velocità suono ~340m/s)
- < 3s → PERICOLO IMMEDIATO (rosso)
- 3–10s → ATTENZIONE (giallo)
- > 10s → MONITORARE (verde)

### Risk Meter
```html
<div class="risk-meter">
  <div class="risk-question" data-weight="2">Sei all'aperto?</div>
  <div class="risk-question" data-weight="2">Vicino ad alberi o strutture alte?</div>
  <div class="risk-question" data-weight="3">In acqua o vicino ad acqua?</div>
  <div class="risk-question" data-weight="2">Su campo aperto o altura?</div>
  <div class="risk-question" data-weight="1">Usi oggetti metallici lunghi?</div>
  <div id="risk-result"></div>
</div>
```
Logica: somma pesi delle risposte "sì" → Verde (0–2), Giallo (3–5), Rosso (6–10)

### Myth Flip Cards
```css
.myth-card { perspective: 1000px; cursor: pointer; }
.myth-card-inner { transform-style: preserve-3d; transition: transform 0.6s; }
.myth-card.flipped .myth-card-inner { transform: rotateY(180deg); }
.myth-front, .myth-back { backface-visibility: hidden; }
.myth-back { transform: rotateY(180deg); background: var(--surface2); border: 1px solid var(--green); }
```

### Spectrum Bar
```
- Barra orizzontale con gradiente spettro visibile (380nm–700nm)
- Linee verticali colorate sulle righe di emissione principali:
  - 337nm (UV, N₂) — viola
  - 391nm (N₂⁺) — viola-blu
  - 486nm (Hβ) — ciano
  - 589nm (Na D) — giallo
  - 656nm (Hα) — rosso
- Hover su linea → tooltip: elemento, transizione, lunghezza d'onda
- Animazione: linee che "accendono" in sequenza al mount (brightness 0→1)
```

---

## 7. Performance e Accessibilità

- **Lazy canvas init**: ogni canvas inizializzato solo alla prima visita della sezione (IntersectionObserver)
- **prefers-reduced-motion**: se attivo, saltare tutte le animazioni CSS e canvas; mostrare stato finale
- **Leaflet**: script caricato nel `<head>` ma mappa inizializzata (`L.map(...)`) solo quando `#mondo` entra nel viewport per la prima volta (flag `mapInitialized`)
- **Font preload**: `<link rel="preload" as="style">` per Google Fonts
- **No autoplay audio**: nessun suono senza interazione utente
- **Alt text**: tutte le immagini SVG hanno `role="img" aria-label`

---

## 8. File Structure

```
index.html          ← tutto (CSS inline in <style>, JS inline in <script>)
docs/
  PROMPT-ORIGINALE.md
  superpowers/
    specs/
      2026-06-04-fulmini-site-v4-design.md  ← questo file
    plans/
      2026-06-04-fulmini-content-rebuild.md ← da creare con writing-plans
```

---

## 9. Dipendenze Esterne (CDN)

```html
<!-- Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">

<!-- Leaflet -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

Nessuna altra dipendenza. Tutto il resto è vanilla JS/CSS.
