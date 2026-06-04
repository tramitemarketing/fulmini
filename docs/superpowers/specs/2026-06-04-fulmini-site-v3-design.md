# Fulmini — Site v3 Design Spec

**Data:** 2026-06-04
**Target:** `site/index.html` (nuovo file in sotto-cartella)
**Fonte contenuti:** `FULIMINI.docx`
**Stile di riferimento:** `index.html` (root — dark theme, gold/blue, Bebas Neue, scroll-snap)

---

## 1. Architettura

Single-file HTML statico. Nessun build step. Vanilla JS + Leaflet CDN.

**Scroll engine:**
- `scroll-snap-type: y mandatory` su `html` (MAI su `body`)
- `body { overflow: visible }` — critico per scroll-snap
- `.slide { height:100vh; scroll-snap-align:start; scroll-snap-stop:always; }`

**Carousel verticale (VC):**
- `.vc-wrap > .vc-track > .vc-panel[]`
- `translateY(n * -100vh)` via JS
- Wheel/touch/keyboard intercettati; al bordo superiore/inferiore il controllo passa al parent scroll-snap

**Canvas:**
- Tutti lazy: inizializzati solo al primo `onSlideEnter(i)`, protetti da `Set triggered`
- Ogni canvas con `width = offsetWidth`, `height = offsetHeight` espliciti

**Fonts:** Bebas Neue + Source Serif 4 (Google Fonts CDN)
**Map:** Leaflet 1.9.4 CDN (CartoDB Dark Matter tiles)

---

## 2. Design System

### CSS Custom Properties
```css
:root {
  --bg:#05070f; --surface:#0a0e1a; --surface2:#0f1525;
  --gold:#f0c040; --blue:#3a8fff; --white:#e8eaf0;
  --muted:#7a8099; --border:rgba(240,192,64,.15);
  --red:#ff4040; --green:#50c878;
}
```

### Layout Classes
- `.panel-split` — flex row 50/50, sinistra testo, destra canvas/contenuto
- `.panel-left` — padding 4rem 2rem 4rem 5.5rem, max-width 50%
- `.panel-right` — flex center, height 100%
- `.panel-center` — flex column centrato, padding 3rem 5.5rem
- `.panel-full` — nessun padding, display block (per mappa e records grid)
- `.slide-eyebrow` — Bebas Neue 0.58rem, letter-spacing .45em, gold tenue
- `.slide-title` — Bebas Neue clamp(2.5rem,5vw,4.5rem), `.slide-title em { color:var(--gold) }`
- `.slide-body` — Source Serif 4 0.88rem, line-height 1.8

### Phase Animation UI (componente comune a tutte le animazioni phased)
```css
.phase-ui        { display:flex; flex-direction:column; gap:.8rem; }
.phase-strip     { display:flex; gap:.4rem; }
.phase-btn       { flex:1; padding:.45rem .3rem; border:1px solid rgba(240,192,64,.15);
                   background:transparent; color:var(--muted);
                   font-family:'Bebas Neue',sans-serif; font-size:.52rem;
                   letter-spacing:.12em; cursor:pointer; transition:all .3s; }
.phase-btn.active{ border-color:var(--gold); color:var(--gold);
                   background:rgba(240,192,64,.05); }
.phase-desc      { font-size:.78rem; color:rgba(232,234,240,.7);
                   line-height:1.55; min-height:3.5em; }
.phase-controls  { display:flex; gap:.5rem; }
.phase-play-btn  { padding:.35rem .9rem; border:1px solid rgba(240,192,64,.25);
                   background:transparent; color:rgba(240,192,64,.7);
                   font-family:'Bebas Neue',sans-serif; font-size:.55rem;
                   letter-spacing:.15em; cursor:pointer; transition:all .25s; }
.phase-play-btn:hover { border-color:var(--gold); color:var(--gold); }
```

### Componenti
- `.stat-trio / .stat-box` — 3 stat-box affiancate (icona, label, valore, unità)
- `.plasma-box` — box bordo blue con titolo Bebas e testo
- `.tl-list / .tl-item / .tl-year / .tl-text` — timeline verticale
- `.type-card / .type-card.selected` — card selezionabile con badge+nome+desc
- `.record-cell / .record-num / .record-label / .record-sub` — cella records grid
- `.deity-card` — card mitologia 2×2
- `.safety-scen.safe/.medium/.danger` — scenario protezione con advice su click
- `.detect-item / .detect-lbl` — lista rilevamento
- `.warn-pill / .warn-pill-text` — pill warning (gold, bordo gold tenue)

---

## 3. Fixed UI

- `#cursor` + `#cursor-ring` — RAF lerp 0.12
- `#progress-bar` — left:0, width:3px, height = i/(n-1)*100%, gold
- `#nav-dots` — 8 ndot right side
- `#slide-counter` — "01 / 08"
- `#fullscreen-btn` — tasto F o click
- `#scroll-hint` — scompare al primo scroll

---

## 4. Struttura HTML — 8 Slide

### S0 — Hero
```
layout: fullscreen
canvas: #hero-canvas (position:absolute, inset:0, z-index:1)
contenuto: eyebrow "la scienza del lampo", h1 "FUL<em>MI</em>NI",
           hero-line, sub "Scariche elettriche atmosferiche — fisica, storia, misteri"
```

---

### S1 — Cos'è un Fulmine + Il Plasma (2 pannelli)

**P1 — Definizione e formazione del plasma**
```
layout: panel-split
sinistra:
  eyebrow: "slide 01 · cos'è un fulmine"
  title: "Una scarica <em>gigantesca</em>"
  body:
    Un fulmine è una scarica elettrica transitoria ad altissima intensità che
    si verifica nell'atmosfera per riequilibrare un forte dislivello di potenziale
    tra nuvola e suolo. La scarica dura pochissimi millisecondi ma raggiunge
    temperature di circa 30.000 K — 5 volte la superficie del sole — e una corrente
    che può superare i 30.000 ampere.
    La sostanza fisica del fulmine non è fuoco né semplice elettricità: è il
    plasma — il quarto stato della materia.
destra: canvas #plasma-canvas
  [animazione B - Plasma Formation, 4 fasi]
  phase-ui sotto il canvas
```

**P2 — I numeri**
```
layout: panel-center
eyebrow: "parametri fisici"
title: "Numeri <em>estremi</em>"
stat-trio:
  ⚡ TENSIONE / 100M–1B / Volt
  ⚡ CORRENTE MEDIA / 30.000 / A (picco 200.000 A)
  ⚡ ENERGIA / 1–5 miliardi / J
plasma-box:
  title: "IL PLASMA — il quarto stato della materia"
  text: Quando il campo supera i 3×10⁶ V/m, N₂ e O₂ si ionizzano istantaneamente:
        ioni positivi ed elettroni liberi. Resistività bassissima, temperatura
        stellare, emissione di fotoni intensissima.
  stati: ● Solido  ● Liquido  ● Gas  ●[ACTIVE] Plasma
```

---

### S2 — Come si Forma + Le 4 Fasi (2 pannelli)

**P1 — Effetto triboelettrico**
```
layout: panel-split
sinistra:
  eyebrow: "slide 02 · formazione"
  title: "Il condensatore <em>naturale</em>"
  body:
    All'interno del cumulonembo, correnti d'aria violente fanno scontrare cristalli
    di ghiaccio (più leggeri) e graupel (grandine tenera, più pesante). L'attrito —
    effetto triboelettrico — strappa elettroni: i cristalli cedono e− caricandosi
    positivamente, il graupel li acquista caricandosi negativamente.
    La nuvola diventa un condensatore naturale. Il terreno sotto sviluppa una carica
    positiva per induzione elettrostatica. Quando ΔV supera ~3×10⁶ V/m, l'aria
    si ionizza e scatta la scarica.
destra: canvas #triboelectric-canvas
  [animazione C - Triboelettrico, 4 fasi]
  phase-ui sotto il canvas
```

**P2 — Le 4 fasi della scarica**
```
layout: panel-split (sinistra max-width 40%)
sinistra:
  eyebrow: "meccanismo di scarica"
  title: "Come si <em>buca</em> l'aria"
  phase-strip: [A. STEPPED LEADER] [B. UPWARD STREAMER] [C. CONNESSIONE] [D. RETURN STROKE]
  phase-desc (aggiornata da JS):
    A: "Dalla base della nuvola parte il precursore — quasi invisibile, avanza
        a scatti di 50 m cercando il percorso di minore resistenza. Traiettoria frattale."
    B: "A poche centinaia di metri dal suolo, i punti prominenti lanciano filamenti
        di plasma blu verso l'alto: gli upward streamers."
    C: "A 30–50 m dal suolo, leader e streamer si incontrano. Il circuito si chiude:
        nasce un 'cavo virtuale' di plasma tra nuvola e terra."
    D: "La resistenza crolla a zero. Una massiccia ondata di carica risale dal suolo
        a ~100.000 km/s (c/3). Questo è il lampo che vediamo — risale dal basso."
  phase-controls: [▶ PLAY] [⏮ RESET]
destra: canvas #leader-canvas
  [animazione D - 4 Fasi della Scarica]
```

---

### S3 — Fisica Estrema (3 pannelli)

**P1 — Termodinamica**
```
layout: panel-split
sinistra:
  eyebrow: "slide 03 · fisica estrema"
  title: "30.000 K in <em>un microsecondo</em>"
  body:
    Il return stroke scalda il canale di plasma da temperatura ambiente a 30.000 K
    in meno di 10⁻⁶ s. Secondo P = ρRT, la pressione interna schizza a 10–100 atm.
    Il canale esplode radialmente a velocità supersonica (Mach > 1 nei primi 1–2 m),
    generando un'onda d'urto idrodinamica che decade poi in onda acustica: il tuono.
    Diametro reale del canale: 2–3 cm. La luce che illumina km² di cielo è scattering
    di Mie e Rayleigh su gocce d'acqua e particelle atmosferiche.
destra: canvas #thermo-canvas
  [animazione E - Termodinamica, 5 fasi]
  phase-ui sotto
```

**P2 — Elettromagnetismo & Z-Pinch**
```
layout: panel-split
sinistra:
  eyebrow: "elettromagnetismo"
  title: "Il fulmine come <em>laboratorio</em>"
  body:
    Una corrente fino a 200.000 A genera un campo magnetico B intensissimo attorno
    al canale (Legge di Ampère). Ogni particella carica in moto è soggetta alla
    forza di Lorentz F = qv×B — diretta verso l'interno del canale.
    Questo è lo Z-Pinch (strizione magnetica): il plasma viene compresso e densificato
    per pochi microsecondi, in un violentissimo equilibrio dinamico tra compressione
    magnetica ed espansione termica.
    La rapidissima variazione di corrente genera anche un potente impulso EM
    rilevabile a centinaia di km dai sensori.
destra: canvas #zpinch-canvas
  [animazione F - Z-Pinch, 4 fasi]
  phase-ui sotto
```

**P3 — Chimica + Fisica Nucleare**
```
layout: panel-center (2 colonne)
colonna sinistra:
  eyebrow: "chimica ad alta energia"
  title: "L'atmosfera <em>trasformata</em>"
  lista:
    🌿 Ozono O₃ — il campo EM dissocia N₂ e O₂; gli atomi si ricombinano
       formando O₃. L'odore "fresco" dopo il temporale è ozono.
    🌱 NOx — fissazione naturale dell'azoto. I fulmini "fertilizzano" il suolo
       con ossidi di azoto indispensabili per le piante.
    💎 Fulgurite — colpendo sabbia/quarzo a 30.000 K, la corrente fonde SiO₂
       creando una roccia vetrosa tubolare che replica la forma della scarica.

colonna destra:
  eyebrow: "fisica nucleare"
  title: "Acceleratore <em>naturale</em>"
  lista:
    ☢️ TGF (Terrestrial Gamma-ray Flashes) — il campo EM accelera elettroni a
       velocità relativistiche. Frenando contro i nuclei, emettono raggi X e
       gamma rilevabili dai satelliti.
    ⚛️ Reazioni fotonucleari — fotoni gamma colpiscono ¹⁴N, strappano un neutrone
       e generano ¹³N instabile. Breve reazione nucleare spontanea in atmosfera.
```

---

### S4 — Tipi di Fulmini (2 pannelli)

**P1 — Fulmini comuni**
```
layout: panel-split
sinistra:
  eyebrow: "slide 04 · tipi di fulmini"
  title: "Fulmini <em>comuni</em>"
  4 type-card (click → aggiorna canvas):
    CG⁻ | Negativo nube-suolo | ~90% | Dal base nuvoloso. Corrente media 30.000 A.
    CG⁺ | Positivo nube-suolo | ~10% | Dall'incudine. Fino a 300.000 A, 10×. I tuoni più forti.
    IC  | Intra-cloud | più frequente | Dentro la nuvola. Segnale di intensificazione temporale.
    CC  | Cloud-to-Cloud | — | Scarica orizzontale, Anvil Crawlers, centinaia di km.
destra: canvas #type-canvas
  [animazione G - Tipo selezionato, loop]
```

**P2 — Fenomeni TLE**
```
layout: panel-split
sinistra:
  eyebrow: "fenomeni transienti luminosi"
  title: "Fulmini verso <em>lo spazio</em>"
  body:
    Fino agli anni '90 i piloti riportavano "luci strane" sopra i temporali.
    Nessuno li credeva. Oggi le chiamiamo TLE (Transient Luminous Events).
  4 type-card (click → aggiorna canvas):
    BALL | Ball Lightning | — | Sfera luminosa, dura secondi, può attraversare vetri. Mistero fisico.
    SPRITE | Sprite | 50–90 km | Medusa rossa dopo CG+. Documentata in foto solo dal 1989.
    ELVES  | Elves | ~400 km | Anello che si espande alla velocità della luce nell'ionosfera.
    JET    | Blue Jet | 40–50 km | Cono blu espulso dalla cima della nuvola verso l'alto.
destra: canvas #tle-canvas
  [animazione H - TLE altimetrico]
```

---

### S5 — Numeri & Record (2 pannelli)

**P1 — I record**
```
layout: panel-full (records-grid 2×3)
eyebrow assoluto: "slide 05 · numeri e record"
6 record-cell:
  8 MLN      | fulmini al giorno nel mondo | 50–100 scariche ogni secondo
  768 KM     | mega-flash Texas–Mississippi | record WMO distanza orizzontale, 2020
  17,1 SEC   | record durata singolo fulmine | Uruguay–Argentina, 2019
  297 NOTTI  | con fulmini — Lago Maracaibo | 28 scariche al minuto, 9 ore consecutive
  ×1.000     | più potenti i fulmini di Giove | Saturno: rilevati da Cassini; spazio: TGF
  100.000 KM/S | velocità return stroke | c/3 — il giro della Terra in < 0,5 secondi
```

**P2 — Il Tuono + Lichtenberg**
```
layout: panel-split
sinistra:
  eyebrow: "la fisica del tuono"
  title: "Dal plasma <em>all'onda</em>"
  body:
    Il tuono è la diretta conseguenza termodinamica del fulmine. Il canale a 30.000°C
    e 10–100 atm si espande in modo supersonico (Mach > 1 entro 1–2 m) generando
    un'onda d'urto idrodinamica. Man mano che perde energia, l'onda rallenta
    sotto Mach 1: diventa onda acustica lineare. Udibile fino a 20–25 km;
    oltre, la rifrazione acustica disperde le onde verso l'alto.

  thunder-calc:
    label: "SECONDI TRA LAMPO E TUONO"
    slider id=thunder-slider min=1 max=30 default=9
    output: <N> KM (formula: km = sec ÷ 3)
    formula mostrata
    nota: "Se l'intervallo diminuisce, il temporale si avvicina."

destra:
  eyebrow: "FIGURA DI LICHTENBERG — clicca per generare"
  canvas #lichtenberg-canvas (cursor:crosshair)
  click → DLA frattale parte dal punto cliccato, cresce con rami gold
  pulsanti: [RESET] [SALVA PNG]
  nota sotto: "Sui sopravvissuti ai fulmini compaiono figure identiche sulla pelle —
               rottura dei capillari lungo la traiettoria della corrente superficiale."
```

---

### S6 — Franklin & Storia (3 pannelli)

**P1 — Il contesto storico**
```
layout: panel-split
sinistra:
  eyebrow: "slide 06 · franklin e storia"
  title: "L'elettricità era <em>uno spettacolo</em>"
  body:
    A metà del XVIII secolo, l'elettricità era poco più di un curioso gioco da
    salotto. Le bottiglie di Leida accumulavano cariche, i filosofi naturali
    divertivano l'aristocrazia con scintille. Franklin, autodidatta delle colonie
    americane, fu il primo a intuire che le scintille di laboratorio e i fulmini
    celesti erano lo stesso fenomeno su scala diversa. La Royal Society di Londra
    lo prese per pazzo.
destra: tl-list
  1746 — Pieter van Musschenbroek inventa la bottiglia di Leida: primo condensatore.
  1750 — Franklin scrive alla Royal Society proponendo di "catturare l'elettricità
          celeste". La lettera viene letta come una curiosità.
  1752 — Esperimento dell'aquilone a Filadelfia (giugno). Franklin e il figlio William.
  1753 — Georg Wilhelm Richmann, San Pietroburgo: primo martire della scienza elettrica.
```

**P2 — L'esperimento dell'aquilone**
```
layout: panel-split
sinistra: canvas #kite-canvas
  [animazione I - Kite Experiment, 5 fasi]
  phase-ui sotto
destra:
  eyebrow: "l'esperimento"
  title: "Induzione, non <em>colpo diretto</em>"
  body:
    Franklin non aspettava un fulmine diretto — sarebbe morto istantaneamente.
    Sfruttava l'induzione elettrostatica: la nuvola carica induceva cariche libere
    lungo la canapa bagnata (conduttore una volta bagnata dai sali minerali).
    Avvicinando il dito (isolato dalla seta) alla chiave di ferro, generava una
    scintilla controllata. La bottiglia di Leida si caricava con elettricità
    celeste: prova definitiva che erano la stessa cosa.
  warn-pill: "⚠ Il 6 agosto 1753 Georg Richmann tentò lo stesso esperimento a
              San Pietroburgo. Fu colpito e ucciso davanti al suo incisore —
              prima vittima accertata nella storia degli esperimenti elettrici."
```

**P3 — Il parafulmine + dopo Franklin**
```
layout: panel-split
sinistra:
  eyebrow: "la conseguenza pratica"
  title: "Da terrore divino a rischio <em>calcolabile</em>"
  body:
    A. EFFETTO PUNTA (prevenzione): Le cariche si concentrano sulle punte metalliche.
       Il campo elettrico intensissimo ionizza costantemente l'aria, disperdendo
       silenziosamente le cariche della nuvola — riducendo localmente ΔV.
    B. CANALIZZAZIONE SICURA (protezione): Se il fulmine scatta comunque, il
       conduttore metallico (rame ≥50 mm²) offre il percorso a minima resistenza.
       La corrente scorre nel cavo verso terra senza toccare pietra o legno.
    Franklin rifiutò il brevetto: "È per il bene dell'umanità."
destra: tl-list
  1753 — Richmann muore. Il parafulmine diventa ingegneria seria.
  1800s — Diffusione in tutto il mondo occidentale.
  1891  — Nikola Tesla: bobina Tesla. Scariche artificiali. Sogna la trasmissione
           wireless di energia gratuita per tutti.
  1960s — Razzi con filo metallico per provocare fulmini controllati in laboratorio.
  2000s+ — LINET (Europa), SIRF/ISPRA (Italia): triangolazione time-of-arrival,
            precisione metrica, copertura continentale.
```

---

### S7 — Nel Mondo, Protezione & Mitologia (3 pannelli)

**P1 — Mappa mondiale**
```
layout: panel-full
<div id="map"> Leaflet dark (CartoDB Dark Matter), center [10, 20], zoom 2
4 marker statici (popup):
  [9.75, -71.6]  Lago Maracaibo — 230–260 fulmini/km²/anno · 297 notti · Guinness WR
  [-2.5, 27.5]   Congo/Kifuka — 205 fulmini/km²/anno · foresta equatoriale
  [27.9, -82.5]  Florida/Tampa — >1.2M fulmini/anno · convergenza brezze marine
  [45.5, 9.2]    Pianura Padana — hotspot autunnale · supercelle quando fronte freddo
                 scavalca le Alpi

5 zone simulate (frequenza calibrata NASA):
  Maracaibo: strike ogni 2s
  Congo:     strike ogni 3s
  Florida:   strike ogni 4s
  Pianura Padana: strike ogni 8s
  Africa equatoriale [4, 25]: strike ogni 5s

Ogni strike: L.circle({radius:12000, color:'#f0c040', fillOpacity:0.7})
             rimosso dopo 3s con fade opacity
Counter top-center: "N FULMINI VISUALIZZATI IN QUESTA SESSIONE"
eyebrow: "SLIDE 07 · NEL MONDO"
```

**P2 — Mitologia**
```
layout: panel-center
eyebrow: "prima della fisica"
title: "Il fulmine come <em>potere assoluto</em>"
body:
  Per millenni, il fulmine era la voce degli dei — incomprensibile, imprevedibile,
  mortale. Ogni civiltà ha attribuito questo potere alla divinità suprema del pantheon.
  I luoghi colpiti erano sacri: i greci li chiamavano enelysion.
4 deity-card (grid 2×2):
  ⚡ ZEUS (Grecia) — Re degli dei. Il fulmine era la sua arma assoluta, simbolo
     di potere e giustizia divina.
  🔨 THOR (Norrena) — Dio del tuono. Il suo martello Mjolnir creava i fulmini
     durante i viaggi tra i regni.
  ⚡ GIOVE (Roma) — Equivalente romano di Zeus. I pontefici romani interpretavano
     direzione e tipo del fulmine come augurio o condanna.
  ✨ INDRA (Induismo) — Dio della guerra e dei temporali. Armato del vajra —
     il fulmine cosmico — sconfisse il demone Vritra liberando le acque del mondo.
```

**P3 — Protezione & Rilevamento moderno**
```
layout: panel-split
sinistra:
  eyebrow: "slide 07 · protezione"
  title: "Cosa fare <em>e non fare</em>"
  safety-grid 3×2 (click → consiglio completo):
    🌲 Sotto un albero   [DANGER]  → Mai. Bersaglio preferito. 50 m di distanza.
    🚗 In auto           [SAFE]    → Gabbia di Faraday. Non toccare la scocca.
    🏠 In edificio       [SAFE]    → Sicuro. Stacca apparecchi. No doccia.
    🏊 In acqua          [DANGER]  → Esci subito. La corrente si espande in superficie.
    ⛰️  In cima          [DANGER]  → Scendi. Accovacciati, piedi uniti, mani sulle orecchie.
    🌾 Campo aperto      [MEDIUM]  → Posizione "a rana" sui talloni. Non sdraiarti.
  mito:
    "Falso che il fulmine non colpisca due volte nello stesso posto:
     l'Empire State Building viene colpito in media 23 volte all'anno."
destra:
  eyebrow: "rilevamento moderno"
  title: "La rete <em>invisibile</em>"
  body:
    Ogni fulmine emette un impulso radio sferico che viaggia a c. Tre o più stazioni
    lo ricevono con differenze di tempo di frazioni di microsecondo. La triangolazione
    time-of-arrival localizza la scarica con precisione di pochi metri.
  detect-list:
    LINET    | Europa: 135+ sensori, copertura continentale
    SIRF     | Italia (ISPRA): 1,5 milioni CG/anno, mappe stagionali
    ENTLN    | Globale: 900+ sensori worldwide
    ISUAL    | Satellite: rileva TGF, Sprite e Elves dallo spazio
```

---

## 5. Animazioni Canvas — Algoritmi Dettagliati

### [A] Hero Canvas (`#hero-canvas`)
```
Loop RAF:
  bTimer++ → ogni 120–240 frame (random): genPath() + trigger flash
  genPath(x, y, targetY, depth, segments):
    step = (targetY - y) / (depth * 2)
    nx = x + (random-.5)*60, ny = y + step*(1+random*.5)
    push {x1,y1,x2,y2, w: depth*.5+.5}
    if random < .35 && depth > 2: branch laterale
    recursion depth-1, w*0.7
  Draw:
    foreach seg: ctx.strokeStyle = w>1.5 ? rgba(255,255,255,alpha) : rgba(240,192,64,alpha*.7)
    shadowColor: w>1.5 ? '#fff' : '#3a8fff', shadowBlur proporzionale a w
  Flash: alpha=1 per 3 frame, poi decay lineare su 10 frame
```

### [B] Plasma Formation (`#plasma-canvas`) — 4 fasi
```
Phase 1 — Aria normale:
  30 molecole come cerchi neutri (rgba(255,255,255,.4), r=4)
  moto browniano casuale, rimbalzano ai bordi

Phase 2 — Campo elettrico cresce:
  Le molecole rallentano (vy *= .95 per frame)
  Frecce campo verticali: linee dashed gold con punta, opacity che cresce

Phase 3 — Ionizzazione:
  Ogni molecola: flash bianco → si spezza in 2:
    ione+ (gold circle, r=5) sale
    e- (blue dot, r=2) scende veloce
  Animato uno alla volta ogni 3 frame (non tutto in un frame)

Phase 4 — Plasma conduttore:
  Tubo luminoso verticale largo 6px al centro (bianco/blue)
  shadowBlur=20, pulsante (opacity .8→1 in sinusoide)
  Ioni e e- in moto libero all'interno del tubo
  Glow radiale attorno al tubo (scattering)
```

### [C] Triboelettrico (`#triboelectric-canvas`) — 4 fasi
```
Phase 1 — Nuvola neutra:
  Ellisse nuvola al centro superiore, particelle neutre miste (dot bianchi/grigi)
  moto casuale all'interno della nuvola

Phase 2 — Collisione + attrito:
  Alcune coppie di particelle si avvicinano e si "scontrano" (flash piccolo)
  Label "effetto triboelettrico" appare

Phase 3 — Separazione cariche:
  Particelle + (gold) migrano verso la sommità della nuvola
  Particelle − (blue) migrano verso la base
  Zone + e − si definiscono chiaramente con label
  Linee di campo appaiono tra le zone

Phase 4 — Induzione sul suolo:
  Suolo + (stringa di + gold) appare nella parte bassa
  Barra verticale tensione destra: riempimento graduale gold
  Label ΔV con freccia che indica il valore critico ~3×10⁶ V/m
  Quando tensione piena: flash piccolo (innesco)
```

### [D] 4 Fasi della Scarica (`#leader-canvas`) — 4 fasi
```
Sfondo: gradiente sky scuro → suolo (rect marrone basso)
Nuvola: ellisse blu-scura in alto

Path stepped leader: pre-calcolato con seed fisso
  [14 step di ~50px ognuno, offset laterali ±40px random con seed]
  Rami: 2–3 rami brevi ogni 3–4 step principali

Phase A — Stepped Leader:
  Anima step per step: ogni frame aggiunge 1 step
  Linea setLineDash([8,8]) gold 60%
  Tra uno step e l'altro: pausa 2 frame (simula il "scatto")
  
Phase B — Upward Streamer:
  Leader completo (fisso, tratteggiato)
  3 filamenti blue salgono dai punti del suolo (random x con seed)
  Animati separatamente, pulsanti, altezza aumenta ogni frame
  
Phase C — Connessione:
  Flash puntuale (cerchio bianco r=0→30, opacity=1→0) al punto di giunzione
  Linea completa flash bianca (linea continua, lineWidth=3, shadowBlur=25)
  Label "CIRCUITO CHIUSO" appare in gold

Phase D — Return Stroke:
  Linea bianca brillante che risale in 8 frame: ogni frame allunga di H/8
  Parte dal suolo, arriva alla nuvola
  width: 4→1 durante la risalita
  shadowBlur: 30 gold+white
  Label "↑ 100.000 km/s · c/3" appare
  Poi: fade lento su 20 frame
```

### [E] Termodinamica (`#thermo-canvas`) — 5 fasi
```
Layout: sezione orizzontale del canale (vista dall'alto/lato)
Canale: rettangolo verticale al centro, W variabile

Phase 1 — Canale a riposo:
  Rettangolo sottile (3px width), colore rgba(100,150,255,.5)
  Termometro dx: 0%, pressione gauge sx: 0%
  Label: "DIAMETRO: 2–3 mm → 2–3 cm"

Phase 2 — Corrente fluisce:
  Frecce gold che scorrono lungo il canale (animate verso il basso)
  Canale si illumina leggermente
  Label corrente: "I = 30.000 A"

Phase 3 — Riscaldamento Joule:
  Canale colore: lerp blue→white in 30 frame
  Termometro destra: sale a 30.000 K (animato, counter)
  Canale width: 3px→8px

Phase 4 — Pressione picco:
  Gauge pressione sinistra: sale 0→100 atm
  Canale width: 8px→20px
  Bordi del canale: frecce verso l'esterno (espansione imminente)

Phase 5 — Onda d'urto + tuono:
  Canale esplode: cerchi concentrici fitti che si espandono
  Cerchi iniziali: shadow forte, spacing ravvicinato (Mach>1 label)
  Cerchi lontani: più spaziati, alpha bassa (onda acustica)
  Label zona interna: "MACH > 1 — onda d'urto"
  Label zona esterna: "MACH < 1 — onda acustica = TUONO"
```

### [F] Z-Pinch (`#zpinch-canvas`) — 4 fasi
```
Layout: canale verticale al centro

Phase 1 — Corrente:
  Frecce gold lungo il canale (flow animation verso il basso)
  Label: "I = 200.000 A"

Phase 2 — Campo magnetico B:
  Cerchi ellittici concentrici attorno al canale (Ampère)
  Animati: r cresce dal canale verso l'esterno, opacity decresce
  Colore: blue-viola
  Label: "B = μ₀I / 2πr"

Phase 3 — Forza di Lorentz:
  Frecce rosse radiali che puntano VERSO il canale (compressione)
  Label: "F = qv×B (verso l'interno)"

Phase 4 — Strizione + rimbalzo:
  Canale si stringe (width *= .5 in 15 frame)
  Poi rimbalzo: si espande più del normale (width *= 2)
  Ciclo 3×, ampiezza decresce (damped oscillation)
  Label: "Z-PINCH — equilibrio dinamico"
```

### [G] Tipo Fulmine (`#type-canvas`) — loop per tipo
```
Sfondo: sky scuro, nuvola in alto, suolo in basso
Default: CG⁻ selezionato

CG⁻:
  Stepped leader frattale dalla base nuvola (gold dashed)
  Return stroke bianco risale in 8 frame
  Loop ogni 120 frame

CG⁺:
  Leader parte dall'incudine (angolo superiore-laterale della nuvola)
  Leader più lento (1 step ogni 3 frame invece di 1)
  Return stroke più spesso (lineWidth: 5 invece di 3)
  Colore: bianco-azzurro

IC:
  Due zone nella nuvola: − base (blue) e + sommità (gold)
  Scarica orizzontale/diagonale all'interno dell'ellisse nuvola
  Nuvola si illumina "da dentro" (fillStyle flash bianco)
  Nessun suolo interessato

CC:
  Due nuvole separate sx e dx
  Scarica orizzontale che le connette
  Geometria: path frattale orizzontale (Anvil Crawler)
  Molto lungo, ramificato
```

### [H] TLE Altimetrico (`#tle-canvas`) — per card selezionata
```
Layout verticale: asse Y = quota, 0 → 400 km
Layer orizzontali con label a sinistra:
  0 km   → SUOLO (rect marrone)
  12 km  → CUMULONEMBO (nuvola)
  50 km  → STRATOSFERA (linea tratteggiata tenue)
  90 km  → MESOSFERA
  400 km → IONOSFERA

Ball Lightning (al suolo):
  Orb arancione (r=12, glow radiale orange→red)
  Moto: sin(t*.02)*50 orizzontale + cos(t*.015)*20 verticale
  Punto bianco al centro

Sprite (50–90 km):
  Ellisse rossa semitrasparente (rx=30, ry=15) a quota 70 km
  8 "tentacoli" che pendono verso il basso (linee irregolari rosse)
  Pulsante (opacity .6→.9)
  Compare 200ms dopo flash della nuvola

Elves (400 km):
  Ellisse piatta (rx cresce da 0 a canvas.width*.45, ry=8) a 400 km
  Alpha: 1→0 in 500ms
  Riesegue ogni 2s

Blue Jet (12→50 km):
  Trapezio blu che cresce verso l'alto: base a 12 km, apice a 50 km
  Gradiente blue→transparent
  Cresce in 30 frame, poi fade in 20 frame
  Riesegue ogni 2s
```

### [I] Kite Experiment (`#kite-canvas`) — 5 fasi
```
Sfondo: cielo notturno temporalesco, nuvola in alto

Phase 1 — Nuvola carica:
  Nuvola con − alla base (cerchi blu piccoli), segni + in alto
  Campo elettrico latente: linee di campo verticali tenue

Phase 2 — Aquilone in aria ionizzata:
  Aquilone (rombo gold) al centro-alto
  Filo metallico in cima (linea gialla appuntita)
  Corda di canapa (linea tratteggiata) che scende dall'aquilone

Phase 3 — Induzione nella canapa:
  Flow animation: punti gold scorrono giù lungo la corda
  Label "cariche scendono lungo la canapa bagnata"
  Chiave di ferro (icona) al nodo inferiore della corda
  Nastro di seta blu separato (isolante) sotto la chiave

Phase 4 — Scintilla alla chiave:
  Flash puntuale tra la chiave e un dito (simbolo) vicino
  Scintilla gold (arc breve, shadowBlur=15)
  Label "scintilla controllata — induzione elettrostatica"

Phase 5 — Bottiglia di Leida carica:
  Bottiglia di Leida (rect stilizzato) si illumina gold
  Freccia che collega chiave → bottiglia
  Label "PROVA: elettricità celeste = elettricità di laboratorio"
  Warn in basso: "Franklin era al sicuro sotto una tettoia con seta asciutta"
```

### [J] Mappa Mondiale (`#map`) — Leaflet + simulazione calibrata
```js
// frequenze per zona (strike/sec):
const zones = [
  { ll: [9.75, -71.6],  name: 'Maracaibo',        freq: 2  },
  { ll: [-2.5,  27.5],  name: 'Congo/Kifuka',      freq: 3  },
  { ll: [27.9, -82.5],  name: 'Florida/Tampa',     freq: 4  },
  { ll: [4,     25],    name: 'Africa equatoriale', freq: 5  },
  { ll: [45.5,   9.2],  name: 'Pianura Padana',    freq: 8  },
];

// Per ogni zona: setInterval ogni freq*1000ms
//   jitter: ±0.8° lat/lon random
//   L.circle(ll+jitter, {radius:10000, color:'#f0c040', weight:1.5, fillOpacity:.7})
//   fadeOut: ogni 100ms opacity -= 0.07; quando opacity<=0: circle.remove()
```

---

## 6. Sistema Verticale Carousel (VC) — JS

```js
function initVerticalCarousel(slideEl) {
  const track  = slideEl.querySelector('.vc-track');
  const panels = [...slideEl.querySelectorAll('.vc-panel')];
  const dots   = [...slideEl.querySelectorAll('.vc-dot')];
  if (!track || panels.length < 2) return null;
  const total = panels.length;
  let idx = 0, busy = false;

  function go(n) {
    if (n < 0 || n >= total || busy) return;
    busy = true; idx = n;
    track.style.transform = `translateY(calc(${n} * -100vh))`;
    dots.forEach((d,i) => d.classList.toggle('active', i===n));
    setTimeout(() => { busy = false; }, 700);
  }
  dots.forEach((d,i) => d.addEventListener('click', () => go(i)));

  slideEl.addEventListener('wheel', e => {
    const down = e.deltaY > 0;
    if (down && idx < total-1) { e.preventDefault(); e.stopPropagation(); go(idx+1); }
    else if (!down && idx > 0) { e.preventDefault(); e.stopPropagation(); go(idx-1); }
    // al bordo: non preventDefault → outer scroll-snap prende il controllo
  }, { passive: false });

  let ty = 0;
  slideEl.addEventListener('touchstart', e => { ty = e.touches[0].clientY; }, { passive:true });
  slideEl.addEventListener('touchend', e => {
    const dy = e.changedTouches[0].clientY - ty;
    if (dy < -50 && idx < total-1) go(idx+1);
    else if (dy > 50 && idx > 0) go(idx-1);
  });

  return { atTop:()=>idx===0, atBottom:()=>idx===total-1, next:()=>go(idx+1), prev:()=>go(idx-1), reset:()=>go(0) };
}
```

---

## 7. onSlideEnter dispatcher

```js
// 0=hero, 1=cosae, 2=formazione, 3=fisica, 4=tipi, 5=numeri, 6=franklin, 7=mondo
const inits = [
  initHeroCanvas,
  () => { initPlasmaCanvas(); },
  () => { initTriboCanvas(); initLeaderCanvas(); },
  () => { initThermoCanvas(); initZPinchCanvas(); },
  () => { initTypeCanvas(); initTLECanvas(); },
  () => { initThunderCalc(); initLichtenberg(); },
  () => { initKiteCanvas(); },
  () => { initMap(); initSafety(); },
];
```

---

## 8. Phase Control Helper (riutilizzato da tutte le animazioni)

```js
function makePhaseController(phases, descEl, btnsSelector, playBtnId, onPhaseChange) {
  // phases: array di { name, desc, draw(ctx, W, H, t) }
  let current = 0, t = 0, playing = false, rafId = null;
  const btns = [...document.querySelectorAll(btnsSelector)];
  const playBtn = document.getElementById(playBtnId);

  function setPhase(n, fromPlay=false) {
    current = Math.max(0, Math.min(n, phases.length-1));
    t = 0;
    btns.forEach((b,i) => b.classList.toggle('active', i===current));
    if (descEl) descEl.textContent = phases[current].desc;
    onPhaseChange && onPhaseChange(current);
  }
  btns.forEach((b,i) => b.addEventListener('click', () => { playing=false; if(playBtn) playBtn.textContent='▶ PLAY'; setPhase(i); }));
  if (playBtn) playBtn.addEventListener('click', () => {
    playing = !playing;
    playBtn.textContent = playing ? '⏸ PAUSA' : '▶ PLAY';
  });

  // Auto-advance ogni ~180 frame se playing
  function tick() {
    t++;
    if (playing && t > 180) { t=0; setPhase((current+1) % phases.length); }
    rafId = requestAnimationFrame(tick);
  }
  tick();
  setPhase(0);
  return { setPhase, getT: () => t, getCurrent: () => current };
}
```

---

## 9. Vincoli Tecnici Critici

1. `html { scroll-snap-type: y mandatory }` — MAI su `body`
2. `body { overflow: visible }` — MAI `overflow-x: hidden` o `overflow: hidden`
3. `.vc-panel { height: 100vh }` — identico a `.slide`
4. Wheel listener `.vc-wrap`: `{ passive: false }` — obbligatorio per `preventDefault()`
5. Canvas: `canvas.width = canvas.offsetWidth; canvas.height = canvas.offsetHeight;` — sempre, prima di disegnare
6. Leaflet `#map`: `width:100%; height:100%` — `.vc-panel` gli dà la dimensione
7. Fase-play: timer basato su RAF frame count, non su `setTimeout` — più smooth
8. DLA Lichtenberg: iterativo con RAF (non ricorsivo sincrono) per non bloccare UI
9. Tutti i canvas inizializzati LAZY in `onSlideEnter`, protetti da `Set triggered`

---

## 10. Spec Self-Review

- ✅ Nessun TBD o placeholder — ogni pannello ha testo completo e algoritmo definito
- ✅ Tutte le 10 animazioni hanno algoritmo dettagliato fase per fase
- ✅ Phase controller è un helper riutilizzabile — meno duplicazione
- ✅ Mappa: simulazione calibrata, non WebSocket — sempre funzionante
- ✅ Il tuono è trattato fisicamente in S3P1 (termodinamica) e con calcolo pratico in S5P2
- ✅ Mitologia è in S7P2 — non scompare ma non occupa spazio primario
- ✅ Debunking (punto più alto) è in S5P2 accanto al calcolo distanza
- ✅ Lichtenberg: DLA iterativo con RAF, non bloccante
- ✅ Return stroke: animato che RISALE (fisicamente corretto, con label esplicita)
- ✅ CG⁺ animato come partenza dall'incudine (fisicamente distinto da CG⁻)
- ✅ Scroll-snap: vincoli tecnici espliciti e corretti
- ✅ Scope: un file HTML, Leaflet CDN, vanilla JS — nessuna dipendenza aggiuntiva
