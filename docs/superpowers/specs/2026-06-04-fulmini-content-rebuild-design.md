# Fulmini — Content Rebuild Design Spec

**Date:** 2026-06-04
**Target file:** `index.html` (single-file, no build system)
**Source content:** `FULIMINI.docx` (17+ sezioni)
**Reference design:** `../terremoti/index.html`

---

## Obiettivo

Riscrivere completamente `index.html` mantenendo il motore slide esistente (scroll-snap, cursor, nav dots, progress bar) ma sostituendo tutto il contenuto HTML con i testi del DOCX e aggiungendo l'infrastruttura dei **caroselli verticali** (sub-panel dentro ogni slide).

---

## Architettura generale

- **8 slide verticali** (`scroll-snap-type: y mandatory` su `html`)
- **Carosello verticale (VC)** su 7 slide (S1–S7): `translateY` su `.vc-track`, wheel interception `{ passive: false }`
- **S0 Hero**: nessun VC, solo canvas + titolo (invariata)
- Motore CSS/JS esistente **mantenuto** (non toccare: scroll-snap, cursor, nav-dots, progress-bar, keyboard nav)
- Keyboard nav **modificata**: `ArrowDown` controlla prima il VC interno; solo al bordo inferiore scorre alla slide successiva
- Tutte le funzioni JS degli strumenti interattivi esistenti **sostituite** con versioni aggiornate

---

## Struttura CSS da aggiungere (vertical carousel)

```css
/* Aggiungere DOPO la sezione /* ── CAROUSEL INFRASTRUCTURE ── */ */

.vc-wrap  { width:100%; height:100%; overflow:hidden; position:relative; }
.vc-track { display:flex; flex-direction:column;
            transition:transform .65s cubic-bezier(.4,0,.2,1);
            will-change:transform; }
.vc-panel { height:100vh; min-height:100vh; flex-shrink:0;
            overflow:hidden; position:relative;
            display:flex; align-items:center; justify-content:center; }

/* Indicatori di panel (lato sinistro, dentro la slide) */
.vc-dots  { position:absolute; left:1.4rem; top:50%;
            transform:translateY(-50%);
            display:flex; flex-direction:column; gap:.5rem; z-index:50; }
.vc-dot   { width:3px; height:3px; background:rgba(240,192,64,.2);
            border-radius:50%; cursor:pointer; transition:all .3s; }
.vc-dot.active { background:rgba(240,192,64,.8); transform:scale(2); }
```

### Layout utility (aggiungere dopo .vc-dot.active)

```css
/* Split 50/50 */
.panel-split { display:flex; width:100%; height:100%; align-items:center; }
.panel-left  { flex:1; padding:4rem 2rem 4rem 5.5rem;
               display:flex; flex-direction:column; justify-content:center;
               max-width:50%; }
.panel-right { flex:1; display:flex; align-items:center;
               justify-content:center; padding:2rem 3rem 2rem 2rem;
               position:relative; height:100%; }

/* Centro */
.panel-center { display:flex; flex-direction:column; align-items:center;
                justify-content:center; padding:3rem 5.5rem;
                width:100%; height:100%; }

/* Tipografia slide */
.slide-eyebrow { font-family:'Bebas Neue',sans-serif; font-size:.58rem;
                 letter-spacing:.45em; color:rgba(240,192,64,.4);
                 margin-bottom:.6rem; display:block; }
.slide-title   { font-family:'Bebas Neue',sans-serif;
                 font-size:clamp(2.5rem,5vw,4.5rem);
                 line-height:1; color:var(--white); margin-bottom:1.5rem; }
.slide-title em { color:var(--gold); font-style:normal; }
.slide-body    { font-size:.88rem; line-height:1.8;
                 color:rgba(232,234,240,.78); }
.slide-body strong { color:var(--gold); font-weight:600; }
.slide-body p + p  { margin-top:1rem; }

/* Stat box trio */
.stat-trio { display:flex; gap:1.5rem; margin:2rem 0; flex-wrap:wrap; }
.stat-box  { flex:1; min-width:150px; border:1px solid var(--border);
             padding:1.4rem 1.2rem; text-align:center;
             background:var(--surface2); }
.stat-icon  { font-size:1.3rem; margin-bottom:.3rem; }
.stat-label { font-family:'Bebas Neue',sans-serif; font-size:.52rem;
              letter-spacing:.3em; color:var(--muted);
              display:block; margin-bottom:.25rem; }
.stat-value { font-family:'Bebas Neue',sans-serif;
              font-size:1.75rem; color:var(--gold); line-height:1; }
.stat-unit  { font-size:.65rem; color:var(--muted); }

/* Plasma box (S1P2) */
.plasma-box   { border:1px solid rgba(58,143,255,.25);
                padding:1.5rem; margin-top:1.5rem; max-width:680px; }
.plasma-title { font-family:'Bebas Neue',sans-serif; font-size:.9rem;
                letter-spacing:.2em; color:var(--blue); margin-bottom:.8rem; }
.plasma-text  { font-size:.82rem; line-height:1.7;
                color:rgba(232,234,240,.7); }
.plasma-states { display:flex; gap:1.5rem; margin-top:1rem;
                 font-size:.72rem; color:var(--muted); }
.plasma-state-active { color:var(--blue); font-weight:600; }

/* Phase buttons (S2P2) */
.phase-strip { display:flex; gap:.4rem; margin-bottom:1.2rem; }
.phase-btn   { flex:1; padding:.45rem .3rem;
               border:1px solid rgba(240,192,64,.15);
               background:transparent; color:var(--muted);
               font-family:'Bebas Neue',sans-serif; font-size:.55rem;
               letter-spacing:.12em; cursor:pointer; transition:all .3s; }
.phase-btn.active { border-color:var(--gold); color:var(--gold);
                    background:rgba(240,192,64,.05); }

/* Physics 3-col (S2P3) */
.physics-cols    { display:flex; width:100%; height:100%; }
.physics-col     { flex:1; padding:4rem 2rem 3rem 2.5rem;
                   display:flex; flex-direction:column;
                   border-right:1px solid rgba(240,192,64,.06); }
.physics-col:last-child { border-right:none; }
.physics-col-icon  { font-size:2rem; margin-bottom:.75rem; }
.physics-col-title { font-family:'Bebas Neue',sans-serif; font-size:1.3rem;
                     color:var(--gold); letter-spacing:.1em; margin-bottom:1rem; }
.physics-col ul    { list-style:none; display:flex; flex-direction:column;
                     gap:.7rem; }
.physics-col li    { font-size:.8rem; color:rgba(232,234,240,.72);
                     line-height:1.55; padding-left:1rem;
                     border-left:2px solid rgba(240,192,64,.18); }
.physics-col li strong { color:var(--white); }

/* Type cards (S3) */
.types-cards { display:flex; flex-direction:column; gap:.7rem; width:100%; }
.type-card   { border:1px solid rgba(240,192,64,.1); padding:.8rem 1rem;
               cursor:pointer; transition:all .3s;
               background:rgba(10,14,26,.5); }
.type-card:hover    { border-color:rgba(240,192,64,.3); }
.type-card.selected { border-color:var(--gold);
                      background:rgba(240,192,64,.04); }
.type-card-head { display:flex; align-items:baseline;
                  gap:.6rem; margin-bottom:.25rem; }
.type-badge { font-family:'Bebas Neue',sans-serif; font-size:.7rem;
              letter-spacing:.15em; color:var(--gold); }
.type-name  { font-size:.8rem; color:var(--white); }
.type-pct   { font-family:'Bebas Neue',sans-serif; font-size:.6rem;
              color:var(--muted); margin-left:auto; }
.type-desc  { font-size:.73rem; color:var(--muted); line-height:1.45; }

/* Sliders (S4P1) */
.slider-block { display:flex; flex-direction:column; gap:1.5rem; }
.slider-item label { font-family:'Bebas Neue',sans-serif; font-size:.55rem;
                     letter-spacing:.25em; color:var(--muted);
                     display:block; margin-bottom:.35rem; }
.slider-item input[type=range] { width:100%; accent-color:var(--gold); cursor:pointer; }
.slider-readout { font-family:'Bebas Neue',sans-serif;
                  font-size:1.5rem; color:var(--gold); }
.slider-readout span { font-size:.55rem; color:var(--muted); letter-spacing:.1em; }
.lich-controls { display:flex; gap:.5rem; margin-top:.75rem; }
.lich-btn { flex:1; padding:.4rem; border:1px solid rgba(240,192,64,.25);
            background:transparent; color:rgba(240,192,64,.7);
            font-family:'Bebas Neue',sans-serif; font-size:.55rem;
            letter-spacing:.15em; cursor:pointer; transition:all .25s; }
.lich-btn:hover { border-color:var(--gold); color:var(--gold); }

/* Myth box (S4P2) */
.myth-list { display:flex; flex-direction:column; gap:1rem; }
.myth-item { border-left:3px solid rgba(240,192,64,.3);
             padding:.6rem 1rem; }
.myth-claim { font-size:.8rem; color:var(--muted);
              font-style:italic; margin-bottom:.25rem; }
.myth-truth { font-size:.82rem; color:var(--white); }
.myth-truth strong { color:var(--gold); }

/* Franklin timeline (S5P1) */
.tl-list { display:flex; flex-direction:column; gap:1.2rem; }
.tl-item { display:flex; gap:1rem; align-items:flex-start; }
.tl-year { font-family:'Bebas Neue',sans-serif; font-size:.95rem;
           color:var(--gold); min-width:3rem; padding-top:.1rem; }
.tl-text { font-size:.8rem; color:rgba(232,234,240,.72); line-height:1.5; }

/* Warning pill */
.warn-pill { display:inline-flex; align-items:center; gap:.6rem;
             border:1px solid rgba(240,192,64,.3); padding:.5rem 1rem;
             margin-top:.75rem; background:rgba(240,192,64,.04); }
.warn-pill-text { font-size:.75rem; color:rgba(240,192,64,.85); }

/* Records grid (S6P1) */
.records-grid { display:grid; grid-template-columns:repeat(3,1fr);
                width:100%; height:100%; }
.record-cell  { display:flex; flex-direction:column; justify-content:center;
                padding:2.5rem 1.8rem;
                border:1px solid rgba(240,192,64,.06);
                background:var(--surface); transition:background .3s; }
.record-cell:hover { background:var(--surface2); }
.record-num   { font-family:'Bebas Neue',sans-serif;
                font-size:clamp(2rem,3.5vw,3rem);
                color:var(--gold); line-height:1; margin-bottom:.3rem; }
.record-label { font-size:.75rem; color:var(--muted); line-height:1.4; }
.record-sub   { font-size:.62rem; color:rgba(122,128,153,.55);
                margin-top:.2rem; }

/* Thunder calc (S6P2) */
.thunder-calc { display:flex; flex-direction:column;
                align-items:center; gap:1.5rem;
                width:100%; max-width:560px; }
.thunder-calc label { font-family:'Bebas Neue',sans-serif; font-size:.55rem;
                      letter-spacing:.3em; color:var(--muted);
                      display:block; text-align:center; margin-bottom:.4rem; }
.thunder-calc input[type=range] { width:100%; accent-color:var(--gold); }
.thunder-output { font-family:'Bebas Neue',sans-serif;
                  font-size:4rem; color:var(--gold);
                  text-align:center; line-height:1; }
.thunder-output span { font-size:.75rem; color:var(--muted);
                       letter-spacing:.1em; }

/* Map overlay counter (S7P1) */
.map-counter { position:absolute; top:1.5rem; left:50%;
               transform:translateX(-50%);
               background:rgba(5,7,15,.88);
               border:1px solid var(--border);
               padding:.5rem 1.4rem; z-index:1000;
               font-family:'Bebas Neue',sans-serif; font-size:.85rem;
               letter-spacing:.2em; color:var(--gold); white-space:nowrap; }

/* Safety grid (S7P2) */
.safety-grid { display:grid; grid-template-columns:repeat(3,1fr);
               gap:.7rem; width:100%; }
.safety-scen { border:2px solid rgba(240,192,64,.1); padding:1rem;
               text-align:center; cursor:pointer; transition:all .3s; }
.safety-scen:hover  { border-color:rgba(240,192,64,.3); }
.safety-scen.safe   { border-color:#50c878; background:rgba(80,200,120,.05); }
.safety-scen.medium { border-color:#c8a820; background:rgba(200,168,32,.05); }
.safety-scen.danger { border-color:#c84040; background:rgba(200,64,64,.05); }
.safety-icon    { font-size:2rem; margin-bottom:.3rem; display:block; }
.safety-label   { font-family:'Bebas Neue',sans-serif; font-size:.55rem;
                  letter-spacing:.15em; color:var(--muted); }
.safety-advice  { font-size:.72rem; color:rgba(232,234,240,.75);
                  margin-top:.5rem; line-height:1.4; display:none; }
.safety-scen.selected .safety-advice { display:block; }

/* Detection list (S7P2) */
.detect-list { display:flex; flex-direction:column; gap:.6rem;
               margin-top:1rem; }
.detect-item { display:flex; gap:.8rem; align-items:flex-start;
               font-size:.8rem; color:rgba(232,234,240,.72); }
.detect-label { font-family:'Bebas Neue',sans-serif; font-size:.6rem;
                letter-spacing:.12em; color:var(--gold); min-width:3.5rem; }
```

---

## Struttura HTML — 8 slide

### S0 — Hero (invariata)

```html
<section id="s-hero" class="slide">
  <canvas id="hero-canvas" style="position:absolute;inset:0;z-index:1;pointer-events:none;"></canvas>
  <div style="position:relative;z-index:2;text-align:center;">
    <span class="hero-eyebrow">la scienza del lampo</span>
    <h1 class="hero-title">FUL<em>MI</em>NI</h1>
    <div class="hero-line"></div>
    <p class="hero-sub">Scariche elettriche atmosferiche — fisica, storia, misteri</p>
  </div>
</section>
```

Stili hero esistenti: `.hero-eyebrow`, `.hero-title`, `.hero-line`, `.hero-sub` — da aggiungere/mantenere nel CSS.

---

### S1 — Cos'è un Fulmine (2 panel)

**Panel 1 — La scarica**

Layout: `.panel-split` — testo a sinistra, canvas `#charge-canvas` a destra.

Testo (sinistra):
- Eyebrow: `SLIDE 01 · COS'È`
- Titolo: `Una scarica <em>gigantesca</em>`
- Body:
  > Il fulmine è una scarica elettrica che porta istantaneamente centinaia di milioni di volt attraverso l'atmosfera. Il canale di plasma — largo solo **2–3 centimetri** — raggiunge **30.000 K** in meno di un microsecondo: 5 volte la temperatura della superficie del Sole.
  >
  > La corrente media supera i **30.000 ampere**: quasi 2.000 volte quella di un forno domestico. Eppure l'energia totale è sorprendentemente limitata — ciò che la rende letale è la brutalità della potenza istantanea, non la quantità totale di energia.

  Nota in italic: `← Clicca il canvas per resettare la carica`

Canvas destra (`#charge-canvas`): animazione cloud-charge (vedi JS `initCloudCharge`).

**Panel 2 — Parametri fisici**

Layout: `.panel-center` — contenuto centrato.

- Eyebrow: `PARAMETRI FISICI`
- Titolo: `Numeri <em>estremi</em>`
- Stat trio (3 box):
  - `⚡` / TENSIONE / `100M–1B` Volt
  - `⚡` / CORRENTE MEDIA / `30.000` A
  - `⚡` / ENERGIA / `1–5 miliardi` J
- Plasma box:
  - Titolo: `IL PLASMA — il quarto stato della materia`
  - Testo: `Quando il campo supera i 3×10⁶ V/m, azoto (N₂) e ossigeno (O₂) si ionizzano istantaneamente: si separano in ioni carichi positivamente ed elettroni liberi. Il canale risultante ha resistività bassissima e conduce liberamente la corrente.`
  - Stati: `● Solido  ● Liquido  ● Gas  ● Plasma` (ultimo in `--blue`)

---

### S2 — Come si Forma (3 panel)

**Panel 1 — La carica (triboelettrico)**

Layout: `.panel-split` — testo sinistra, canvas `#formation-canvas` destra.

- Eyebrow: `SLIDE 02 · FORMAZIONE`
- Titolo: `Il condensatore <em>naturale</em>`
- Body:
  > All'interno di un cumulonembo le correnti d'aria trascinano verso l'alto i cristalli di ghiaccio e verso il basso le gocce d'acqua pesanti. L'attrito tra queste particelle — effetto **triboelettrico** — separa le cariche: **ioni positivi** si accumulano nella sommità del cloud, **ioni negativi** nella base.
  >
  > La nuvola diventa un condensatore naturale. La terra sotto di essa, per **induzione elettrostatica**, sviluppa una carica positiva speculare. Quando la differenza di potenziale tra base della nuvola e suolo supera la soglia di rottura dielettrica dell'aria (~**3×10⁶ V/m**), scatta la scarica.

Canvas destra (`#formation-canvas`): sezione trasversale cumulonembo con frecce che mostrano ± in movimento. Animazione semplice: particelle + (oro) salgono verso la sommità, particelle - (blu) scendono verso la base; quota indicata con label.

**Panel 2 — Il meccanismo (4 fasi)**

Layout: `.panel-split` — fase indicator sinistra, canvas `#leader-canvas` destra (o full-width canvas con pannello testo sovrapposto).

Sinistra:
- Eyebrow: `MECCANISMO DI SCARICA`
- Phase strip con 4 bottoni:
  1. `STEPPED LEADER`
  2. `UPWARD STREAMER`
  3. `CONNESSIONE`
  4. `RETURN STROKE`
- Descrizione della fase attiva (aggiornata via JS):
  - Fase 0: "Il precursore scende a gradini da 50 m, invisibile a occhio nudo, a 200.000 km/h. Il percorso è frattale e ramificato."
  - Fase 1: "Quando il leader si avvicina a qualche centinaio di metri, streamer positivi salgono dal suolo dai punti sporgenti."
  - Fase 2: "A 30–50 m di quota avviene la connessione. Il circuito si chiude in un flash."
  - Fase 3: "Il Return Stroke risale dalla terra verso la nuvola a ~100.000 km/s (c/3). È ciò che vediamo come 'lampo'."
- Button `▶/⏸` con id `leader-play`

Canvas destra (`#leader-canvas`): animazione 4-fasi (vedi JS `initSteppedLeader`).

**Panel 3 — Fisica avanzata**

Layout: `.physics-cols` — 4 colonne.

- Eyebrow: `SLIDE 02 · FISICA AVANZATA`
- Colonna 1 — **Termodinamica** (🌡️):
  - Canale: inizialmente mm, si espande a cm
  - Temperatura: 30.000 K in <1 µs
  - Pressione: 10–100 atm
  - Espansione supersonica → onda d'urto → tuono
- Colonna 2 — **Elettromagnetismo** (⚡):
  - Equazioni di Maxwell: variazione rapida di corrente → intenso campo EM
  - Legge di Ampère: campo magnetico attorno al canale
  - **Effetto Z-Pinch**: strizione magnetica che comprime il plasma
  - Impulso EM rilevato a centinaia di km dai sensori
- Colonna 3 — **Chimica** (🧪):
  - Produzione di **ozono (O₃)** — odore dopo il temporale
  - Produzione di **NOx** — fissazione dell'azoto (concime naturale)
  - **Fulgurite**: sabbia/roccia vetrificata dal calore (fino a 2 m di profondità)
- Colonna 4 — **Fisica nucleare** (☢️):
  - **TGF** (Terrestrial Gamma-ray Flashes): rilevati dai satelliti
  - Il campo EM accelera elettroni a velocità relativistiche
  - Reazioni fotonucleari: N-14 → N-13 (emissione di positroni)

---

### S3 — Tipi di Fulmine (2 panel)

**Panel 1 — Fulmini comuni**

Layout: `.panel-split` — cards sinistra, canvas `#type-canvas` destra.

Cards sinistra (`.types-cards`, 4 `.type-card` con `data-type`):

| data-type | Badge | Nome | Percentuale | Descrizione |
|---|---|---|---|---|
| `cg-neg` | CG⁻ | Negativo nube-suolo | ~90% | Dal base nuvoloso verso terra. Corrente media 30.000 A. Il tipo più comune e studiato. |
| `cg-pos` | CG⁺ | Positivo nube-suolo | ~10% | Dall'incudine verso terra. Fino a 300.000 A, 10× più potente, udibile a distanze maggiori. |
| `ic` | IC | Intra-cloud | — | Il tipo più frequente in assoluto. Rimane interno alla nuvola. Segnale per i meteorologi: precede l'intensificazione. |
| `cc` | CC | Cloud-to-Cloud | — | Scarica orizzontale tra due nuvole separate. Anvil Crawlers: percorrono centinaia di km con geometria frattale. |

Canvas destra (`#type-canvas`): animazione del tipo selezionato (click su card).

**Panel 2 — Fenomeni rari**

Layout: `.panel-split` — cards sinistra, canvas `#tle-canvas` destra.

Cards sinistra (4 `.type-card`):

| data-type | Badge | Nome | Descrizione |
|---|---|---|---|
| `ball` | BL | Ball Lightning | Sfera luminosa (arancione→bianca), dimensione arancia→pallone, dura secondi, può passare attraverso il vetro. Teoria: plasma di silicio. |
| `sprite` | TLE | Sprite | Medusa rossa, 50–90 km quota, appare dopo un CG positivo, dura <100 ms. Scoperto in foto nel 1989. |
| `elves` | TLE | Elves | Anello luminoso a ~400 km (ionosfera), si espande alla velocità della luce. Dura <1 ms. |
| `blue-jet` | TLE | Blue Jet | Getto conico blu dalla sommità della nuvola verso l'alto, 40–50 km. Blu = eccitazione dell'N₂. |

Canvas destra (`#tle-canvas`): animazione del tipo TLE selezionato.

---

### S4 — Fisica & Simulazione (2 panel)

**Panel 1 — Parametri & Lichtenberg**

Layout: `.panel-split` — sliders sinistra, canvas `#lichtenberg-canvas` destra.

Sinistra:
- Eyebrow: `SLIDE 04 · FISICA`
- Titolo: `Simula i <em>parametri</em>`
- `.slider-block` con 3 slider:
  1. **Tensione** `id="v-slider"` range 100–1000 (MV) — readout: `<span id="v-out">350</span> MV`
  2. **Corrente** `id="i-out"` (calcolata, non slider): `I = V / 15` (kΩ aria, risultato in kA) — readout: `<span id="i-out">23.3</span> kA`
  3. **Temperatura** — barra colorata: `T ∝ I²`, colore da `#3a8fff` (bassa) a `#ffffff` (alta), larghezza = % del max; testo readout `<span id="t-out">12.000</span> K`
  
  Nota: il marker "fulmine tipico" è fisso a 350 MV sulla barra tensione.

Destra:
- Label: `FIGURA DI LICHTENBERG — clicca per generare`
- Canvas `#lichtenberg-canvas`
- `.lich-controls`:
  - Button `id="lich-reset"` → `RESET`
  - Button `id="lich-save"` → `SALVA PNG`

**Panel 2 — Debunking & Curiosità**

Layout: `.panel-split` — miti sinistra, canvas `#em-canvas` destra.

Sinistra:
- Eyebrow: `MITI VS REALTÀ`
- Titolo: `Cosa <em>non</em> è vero`
- `.myth-list` con 3 item:
  1. Claim: *"Il fulmine colpisce il punto più alto"* → Truth: **Falso.** Segue il percorso di **minima resistenza** (massima conduttività dell'aria ionizzata), non necessariamente il punto più elevato.
  2. Claim: *"Il canale del fulmine è largo"* → Truth: **Falso.** Il canale plasma è **2–3 cm di diametro**. L'apparente larghezza è diffusione della luce (scattering di Mie + Rayleigh) che illumina km² di atmosfera in un'unica immagine.
  3. Claim: *"I fulmini producono solo luce e calore"* → Truth: **Falso.** Producono **TGF** (gamma ray), raggi X, ozono, NOx, e persino reazioni fotonucleari (N-14 → N-13).

Destra:
- Label: `IMPULSO EM — clicca Avvia`
- Canvas `#em-canvas` con button `id="em-play"` sotto

---

### S5 — Benjamin Franklin (3 panel)

**Panel 1 — Il contesto storico**

Layout: `.panel-split` — testo sinistra, timeline destra.

Sinistra:
- Eyebrow: `SLIDE 05 · FRANKLIN`
- Titolo: `L'elettricità era <em>uno spettacolo</em>`
- Body:
  > Nel 1750 l'elettricità era un gioco da salotto: le bottiglie di Leida accumulavano la carica, e i filosofi naturali divertivano il pubblico aristocratico con scintille e piccole scosse. L'idea che il fulmine e l'elettricità fossero la stessa cosa era considerata bizzarra, quasi blasfema.

Destra — `.tl-list`:

| Anno | Testo |
|---|---|
| 1746 | Pieter van Musschenbroek inventa la **bottiglia di Leida** — primo condensatore della storia. |
| 1750 | Franklin scrive alla Royal Society proponendo di "catturare" l'elettricità celeste con un'asta di ferro. La lettera viene **letta come una curiosità**. |
| 1752 | Franklin esegue l'esperimento dell'aquilone a Filadelfia (o molto probabilmente lo descrive senza eseguirlo in prima persona). |
| 1753 | Georg Wilhelm Richmann di San Pietroburgo tenta di ripetere l'esperimento: viene **ucciso** dal fulmine il 6 agosto. |

---

**Panel 2 — L'esperimento**

Layout: `.panel-split` — SVG kite sinistra, testo destra.

SVG sinistra (`#franklin-svg`): disegno stilizzato (nessuna animazione SMIL — troppo complessa). Elementi:
- Cielo scuro con nuvola grigio-blu in alto
- Aquilone romboidale in oro (3/4 in alto al centro)
- Corda di canapa tratteggiata (gold, 60% opacity) che scende
- Chiave di ferro al nodo inferiore della corda
- Nastro di seta (blu) nella parte bassa (isolante)
- Bottiglia di Leida a terra
- Label SVG: "Seta (kite)", "Canapa bagnata (conduttore)", "Chiave di ferro", "Seta (isolante)", "Bottiglia di Leida"

Testo destra:
- Eyebrow: `L'ESPERIMENTO`
- Titolo: `Induzione, non <em>colpo diretto</em>`
- Body:
  > Franklin non aspettava un fulmine diretto. Sfruttava l'**induzione elettrostatica**: la nuvola carica induceva cariche uguali e contrarie lungo la corda bagnata. La chiave di ferro fungeva da raccoglitore; toccandola (con la seta come isolante) si sentiva la scossa e si caricava la bottiglia di Leida.
  >
  > La scoperta chiave: il cielo è un oceano di carica elettrica. Non un dio, non un capriccio — un **fenomeno fisico misurabile e riproducibile**.

Warn pill: `⚠ Georg Richmann morì nel tentare lo stesso esperimento — l'acqua nella sua bottiglia condusse troppo bene.`

**Panel 3 — Il parafulmine & dopo**

Layout: `.panel-split` — parafulmine sinistra, aftermath destra.

Sinistra:
- Eyebrow: `IL PARAFULMINE`
- Titolo: `Due principi <em>fisici</em>`
- Body:
  > **A. Effetto Punta:** Le punte metalliche acuminate concentrano il campo elettrico locale, favorendo la dispersione silenziosa della carica accumulata — riducendo il rischio di scarica violenta.
  >
  > **B. Canalizzazione sicura:** Se la scarica avviene comunque, il conduttore metallico (rame/alluminio, sezione ≥ 50 mm²) offre un percorso a bassa resistenza che porta la corrente a terra senza danni strutturali.

Destra — aftermath `.tl-list`:

| Anno | Testo |
|---|---|
| 1753 | Richmann muore. Il parafulmine diventa serio. |
| 1800s | Il parafulmine si diffonde in tutta Europa e America. Franklin rifiuta il brevetto: "È per il bene dell'umanità." |
| 1891 | **Nikola Tesla** sviluppa la bobina Tesla — risonanza ad alta frequenza. Sogna la trasmissione wireless di energia. |
| 1960s | Prime reti di rilevamento fulmine sistematiche (radio direction finding). |
| 2000s+ | **LINET** (Europa), **SIRF/ISPRA** (Italia): triangolazione time-of-arrival, precisione metrica, copertura totale. |

---

### S6 — Numeri & Tuono (2 panel)

**Panel 1 — I record**

Layout: `.records-grid` (3×2 = 6 celle), full-height.

Eyebrow sopra la grid: `SLIDE 06 · NUMERI`

Celle:

| record-num | record-label | record-sub |
|---|---|---|
| `8 MLN` | fulmini al giorno nel mondo | 50–100 scariche/secondo in ogni istante |
| `768 KM` | mega-flash Texas–Mississippi | record distanza orizzontale, 2020 |
| `17,1 SEC` | record durata | Uruguay–Argentina, 2019 |
| `297` | notti/anno con fulmini | Lago Maracaibo, Venezuela — capitale mondiale |
| `×1.000` | più potenti su Giove | Saturn anche: fulmine rilevato da Cassini |
| `100.000` | km/s return stroke | c/3 — percepito come discendente, in realtà sale |

**Panel 2 — Il tuono**

Layout: `.panel-split` — testo fisico sinistra, thunder calc destra.

Sinistra:
- Eyebrow: `TERMODINAMICA DEL TUONO`
- Titolo: `Dal plasma <em>all'onda</em>`
- Body:
  > Il Return Stroke riscalda il canale a **20.000–30.000°C** in meno di 1 µs. Per la legge dei gas ideali (P = ρRT), la pressione schizza a **10–100 atmosfere** in uno spazio di pochi centimetri.
  >
  > L'espansione supersonica (Mach > 1 entro 1–2 m) genera un'**onda d'urto** che decade rapidamente in onda acustica: il tuono. Si propaga a ~340 m/s e rimane udibile fino a 20–25 km (oltre questa distanza la rifrazione acustica lo disperde).

Destra:
- Label: `CALCOLA LA DISTANZA`
- `.thunder-calc`:
  - Slider `id="thunder-slider"` range 1–30, label: `SECONDI TRA LAMPO E TUONO`
  - Readout: `<span id="thunder-out">3.3</span> <span>KM</span>` (formula: `km = sec / 3`)
  - Canvas `#thunder-canvas` (240px height): animazione bolt a sinistra + cerchi concentrici lenti che si espandono a destra
  - Nota: `Formula: km = secondi ÷ 3`

---

### S7 — Nel Mondo & Protezione (2 panel)

**Panel 1 — Mappa mondiale**

Layout: `.vc-panel` full-size, nessun `.panel-split`.

- Div `#map` (100% width, 100% height) — Leaflet dark tile (CartoDB Dark Matter)
- Div `.map-counter` sovrapposto: `<span id="bolt-count">0</span> FULMINI NEGLI ULTIMI 5 MIN`
- Eyebrow sopra il counter (piccolo): `SLIDE 07 · NEL MONDO`

Blitzortung WebSocket: `wss://ws1.blitzortung.org:8082/` — ogni strike: cerchio gold che si espande + svanisce in 2s. Se WS fallisce dopo 5s: avvia `startFallbackStrikes()` (vedi JS).

Marker statici Leaflet (`.bindPopup()`):

| Coordinate | Nome | Popup |
|---|---|---|
| 9.75, -71.6 | Lago Maracaibo | `230–260 fulmini/km²/anno · 297 notti/anno` |
| -2.5, 27.5 | Congo (Kifuka) | `205 fulmini/km²/anno · foresta + orografia` |
| 27.9, -82.5 | Tampa, Florida | `>1.2M fulmini/anno · convergenza brezze marine` |
| 45.5, 9.2 | Pianura Padana | `Hotspot autunnale italiano · convezione tardiva` |

**Panel 2 — Protezione**

Layout: `.panel-split` — protezione sinistra, rilevamento destra.

Sinistra:
- Eyebrow: `SLIDE 07 · PROTEZIONE`
- Titolo: `Cosa fare <em>e non fare</em>`
- `.safety-grid` (3×2 = 6 scenari `.safety-scen`, click → `.selected`):

| data-scenario | icon | label | classe | advice |
|---|---|---|---|---|
| `tree` | 🌲 | Sotto un albero | `danger` | Mai ripararsi sotto alberi isolati. Sono i bersagli preferiti. Allontanarsi almeno 50 m. |
| `car` | 🚗 | In auto | `safe` | La carrozzeria metallica funge da gabbia di Faraday. Rimani dentro, non toccare la scocca. |
| `building` | 🏠 | In edificio | `safe` | Sicuro. Stacca elettrodomestici, evita docce e rubinetti (le tubature conducono). |
| `lake` | 🏊 | In acqua/lago | `danger` | Uscire immediatamente. L'acqua conduce la corrente in superficie per centinaia di metri. |
| `hill` | ⛰️ | In cima a una collina | `danger` | Scendi subito. Accovacciati sui talloni, piedi uniti, mani sulle orecchie. |
| `field` | 🌾 | In campo aperto | `medium` | Accovacciati a rana (talloni uniti, bassa quota). Non sdraiarti — il passo di contatto è mortale. |

Default: tutte le classi sono già impostate (non neutrale) — `.safety-advice` visible solo su click (classe `.selected`).

Destra:
- Eyebrow: `RILEVAMENTO MODERNO`
- Titolo: `La rete <em>invisibile</em>`
- Body:
  > Ogni fulmine emette un impulso elettromagnetico (sferico) che viaggia a c. Tre o più stazioni ricevono l'impulso con differenze di tempo di frazioni di microsecondo: la **triangolazione time-of-arrival** localizza la scarica con precisione di **pochi metri**.
- `.detect-list`:
  - `LINET` | Europa: 135+ sensori, copertura continentale
  - `SIRF/ISPRA` | Italia: 1,5 milioni di CG/anno, mappe stagionali
  - `ENTLN` | Globale: 900+ sensori worldwide
  - `ISUAL` | Satellite: rileva TGF e Sprite dallo spazio

---

## JavaScript — Architettura

### Stato globale

```js
let cx=0, cy=0, rx=0, ry=0; // cursor positions
let currentIdx = 0;
const triggered = new Set();
const vcMap = new Map();
const slides = [...document.querySelectorAll('.slide')];
```

### Vertical Carousel — `initVerticalCarousel(slideEl)`

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

  return {
    atTop:    () => idx === 0,
    atBottom: () => idx === total-1,
    next:     () => go(idx+1),
    prev:     () => go(idx-1),
    reset:    () => go(0),
  };
}

// Registra tutti i VC dopo il DOM
slides.forEach(sl => {
  const vc = initVerticalCarousel(sl);
  if (vc) vcMap.set(sl, vc);
});
```

### Keyboard Navigation (sostituisce quella esistente)

```js
document.addEventListener('keydown', e => {
  const vc = vcMap.get(slides[currentIdx]);
  if (e.key === 'ArrowDown' || e.key === 'PageDown') {
    e.preventDefault();
    if (vc && !vc.atBottom()) vc.next();
    else slides[Math.min(currentIdx+1, slides.length-1)]
         .scrollIntoView({ behavior:'smooth' });
  } else if (e.key === 'ArrowUp' || e.key === 'PageUp') {
    e.preventDefault();
    if (vc && !vc.atTop()) vc.prev();
    else slides[Math.max(currentIdx-1, 0)]
         .scrollIntoView({ behavior:'smooth' });
  } else if (e.key === 'f' || e.key === 'F') {
    document.fullscreenElement
      ? document.exitFullscreen()
      : document.documentElement.requestFullscreen();
  }
});
```

### `onSlideEnter(i)` — dispatcher

```js
function onSlideEnter(i) {
  const vc = vcMap.get(slides[i]);
  if (vc) vc.reset();
  if (triggered.has(i)) return;
  triggered.add(i);
  const dispatch = [
    initHeroCanvas,          // 0 - hero
    initCloudCharge,         // 1 - cos'è
    initSteppedLeader,       // 2 - formazione
    () => { initTypeVisualizer(); initTLEVisualizer(); }, // 3 - tipi
    () => { initPhysicsSliders(); initLichtenberg(); initEMPulse(); }, // 4 - fisica
    () => {},                // 5 - franklin (SVG statico)
    () => { initThunderCalc(); initThunderCanvas(); },   // 6 - numeri
    () => { initBlitzortung(); initSafetySimulator(); }, // 7 - mondo
  ];
  if (dispatch[i]) dispatch[i]();
}
```

---

## Interactive Tools — Algoritmi

### `initHeroCanvas()` — Canvas S0

```
canvas #hero-canvas: position:absolute, inset:0
RAF loop: ogni 2000–4000ms:
  1. clearRect
  2. Genera bolt:
     - punto di partenza: x = random(20%,80%) * W, y = 0
     - Funzione ricorsiva bolt(x1,y1,x2,y2,width,depth):
         midX = (x1+x2)/2 + random*dx*0.5
         midY = (y1+y2)/2 + |dy|*0.3 random
         disegna linea x1→mid→x2 in gold (opacity proporzionale a width)
         con prob 0.4: genera branch laterale
         ricorsione depth-1, width*0.7
     - shadowBlur=15, shadowColor='#3a8fff'
  3. return stroke (flash bianco, 100ms poi clearRect)
```

### `initCloudCharge()` — Canvas S1P1 (`#charge-canvas`)

```
Inizializza 40 particelle:
  - tipo: '+' (gold, partono dal basso, salgono verso H*0.15)
         '-' (blue, partono dall'alto, scendono verso H*0.75)
  - vx: random small drift
  - loop wrap agli estremi

RAF frame:
  - Disegna nuvola (ellisse blu-scura in alto, H*0.15)
  - Disegna suolo (rettangolo marrone, H*0.85+)
  - Muovi ogni particella (vy costante per tipo)
  - Disegna char '+' o '-' nella posizione (font 11px)
  - tension += 0.003 per frame
  - Barra verticale destra: fill gradiente blue→gold da 0 a tension*H*0.75
  - Se tension >= 0.99:
      disegna bolt semplice (3-4 punti in zigzag, gold/white)
      tension = 0
  
click su canvas: tension = 0
```

### `initSteppedLeader()` — Canvas S2P2 (`#leader-canvas`)

```
Stato: phase=0, t=0, playing=true

phase-btn click → setPhase(n): aggiorna UI + resetta t

RAF drawPhase():
  clearRect
  disegna sfondo (gradiente cielo scuro + suolo)
  
  switch(phase):
    0 - Stepped leader:
        steps = floor(t/10) capped at 12
        linea tratteggiata (setLineDash[6,6]) gold 60%
        da (W/2, H*0.05) scende con offsets casuali fissi (seed)
    1 - Upward streamer:
        mostra leader completo (tratteggiato)
        + streamer blu che sale da (W/2±30, H*0.85) verso su (pct = t/80)
    2 - Connessione:
        flash bianco pulsante (alpha = 0.5+0.5*sin(t*0.3))
        linea full height, shadowBlur 30
    3 - Return stroke:
        linea bianca-gold che risale da terra velocemente
        width decresce con t, opacity decresce
  
  t++
  if playing && t > 120:
      t=0; phase = (phase+1)%4; setPhase(phase)
```

### `initTypeVisualizer()` — Canvas S3P1 (`#type-canvas`)

```
Gestisce 4 card S3P1 (cg-neg, cg-pos, ic, cc)
currentType = 'cg-neg' (default, prima card selected)

card.click → currentType = card.dataset.type; t=0

RAF frame per tipo:
  cg-neg: leader tratteggiato gold scende (t<100), poi return stroke bianco (t>=100)
  cg-pos: stessa cosa ma da anvil laterale, colore bianco-azzurro, più spesso
  ic:     scarica orizzontale dentro ellisse nuvola (linea gold che avanza)
  cc:     due nuvole sx/dx, scarica che le collega (leader + return)

Cloud e suolo sempre presenti nel contesto.
```

### `initTLEVisualizer()` — Canvas S3P2 (`#tle-canvas`)

```
Gestisce 4 card S3P2 (ball, sprite, elves, blue-jet)
currentType = 'ball' (default)

ball:     sfera che fluttua (sin/cos path), glow radiale orange→red, punto bianco al centro
sprite:   ellisse rossa semitrasparente a H*0.35, tendrils verso il basso (8 linee), pulsante
elves:    ellisse piatta che si espande da W*0.45 a W*0.05 di raggio (ionosfera H*0.1), alpha decresce
blue-jet: trapezio blu che cresce verso l'alto da H*0.62, gradiente blue→transparent
```

### `initPhysicsSliders()` — S4P1

```
v-slider input:
  V = slider.value  // MV
  I = V / 15        // kA (R_aria = 15 MΩ approssimato)
  T = Math.min(30000, I * I * 40)  // K approssimato, capped 30000
  
  Aggiorna v-out, i-out, t-out
  Aggiorna barra temperatura (colore lerp da #3a8fff a #ffffff, width = T/30000*100%)
```

### `initLichtenberg()` — Canvas S4P1 (`#lichtenberg-canvas`)

```
canvas.addEventListener('click', evt => {
  const {offsetX:sx, offsetY:sy} = evt
  DLA-like:
    grid booleano W×H
    branch(x, y, depth):
      if depth==0 return
      disegna dot gold con shadowBlur=8
      for each tentative direction (random order):
        nx = x + random_step, ny = y + random_step_y_biased (scende leggermente)
        if !grid[nx,ny] e random<0.7:
          grid[nx,ny] = true
          drawLine(x,y,nx,ny) gold, width proporzionale a depth
          branch(nx,ny,depth-1)
  Usa requestAnimationFrame per disegnare un segmento per frame (non blocca UI)
})

lich-reset: clearRect + reset grid
lich-save:  a = document.createElement('a'); a.href=canvas.toDataURL(); a.download='lichtenberg.png'; a.click()
```

### `initEMPulse()` — Canvas S4P2 (`#em-canvas`)

```
em-play click:
  Sequenza animata:
    1. Disegna bolt al centro (gold, 100ms)
    2. Lancia cerchi: ogni 200ms un nuovo cerchio (max 5)
       - ogni cerchio ha r che cresce (RAF)
       - opacity = 1 - r/maxR
       - colore: gold→blue
  Ripetibile: click riparte
```

### `initThunderCalc()` e `initThunderCanvas()` — S6P2

```
thunder-slider input:
  sec = slider.value
  km = (sec / 3).toFixed(1)
  thunder-out.textContent = km
  
thunder-canvas:
  RAF loop continuo:
    bolt statico a sinistra (cx = W*0.1, gold, height H*0.6)
    cerchi che si espandono da cx verso destra (velocità lenta ~2px/frame)
    ogni cerchio: r cresce, opacity = 1-r/(W*0.9)
    nuovi cerchi ogni 80 frame
```

### `initBlitzortung()` — S7P1 (`#map`)

```
Leaflet map:
  L.map('map', { center:[44,12], zoom:5, zoomControl:false })
  tileLayer: 'https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png'
  attributionControl: false

Marker statici (4) con popup testuali.

WebSocket: ws = new WebSocket('wss://ws1.blitzortung.org:8082/')
  onmessage: parse JSON → {lat,lon}
    - L.circle([lat,lon], {radius:15000, color:'#f0c040', weight:1.5, fillOpacity:0.6})
        .addTo(map)
    - Animazione: setTimeout(circle.remove, 2000)
    - boltCount++ → aggiorna #bolt-count
    - Ogni 5min: boltCount = 0

  onerror / 5s timeout senza messaggi → startFallbackStrikes():
    setInterval ogni 800ms:
      Sceglie random tra zone ad alta attività:
        [9,-71], [-2,27], [27,-82], [45,9], [4,35], [14,30]
      + jitter ±2°
      Crea cerchio come sopra
```

### `initSafetySimulator()` — S7P2

```
document.querySelectorAll('.safety-scen').forEach(el => {
  el.addEventListener('click', () => {
    document.querySelectorAll('.safety-scen').forEach(s => s.classList.remove('selected'));
    el.classList.add('selected');
  });
});
// Le classi safe/medium/danger sono già nel HTML
// .safety-advice è display:none di default, display:block quando .selected (via CSS)
```

---

## File Layout — Ordine sezioni in `index.html`

```
<head>
  fonts, leaflet.css
  <style>
    :root + reset
    slide engine (esistente)
    cursor (esistente)
    fixed UI (esistente)
    vertical carousel (NUOVO)
    horizontal carousel infra disabled (esistente)
    hero styles
    panel layout utilities (NUOVO)
    s1 stat-box, plasma-box
    s2 phase-strip, physics-cols
    s3 type-cards
    s4 sliders, lich-controls, myth-list
    s5 timeline
    s6 records-grid, thunder-calc
    s7 map-counter, safety-grid, detect-list
    animations esistenti (@keyframes flashbolt, scrollBob)
  </style>
</head>
<body>
  fixed UI (cursor, progress-bar, nav-dots x8, slide-counter, fullscreen-btn, scroll-hint)
  s-hero
  s-cosae       (vc: 2 panel)
  s-formazione  (vc: 3 panel)
  s-tipi        (vc: 2 panel)
  s-fisica      (vc: 2 panel)
  s-franklin    (vc: 3 panel)
  s-numeri      (vc: 2 panel)
  s-mondo       (vc: 2 panel)
  <script>
    state
    cursor RAF
    slide observer + updateUI
    keyboard (MODIFICATA)
    initVerticalCarousel + vcMap registration
    onSlideEnter dispatcher
    initHeroCanvas
    initCloudCharge
    initFormationCanvas (S2P1 — animazione statica nuvola)
    initSteppedLeader
    initTypeVisualizer
    initTLEVisualizer
    initPhysicsSliders
    initLichtenberg
    initEMPulse
    initThunderCalc + initThunderCanvas
    initBlitzortung (con fallback)
    initSafetySimulator
    initCarousel (infra disabilitata, non chiamata)
    scroll-hint hide
    fullscreen btn
  </script>
</body>
```

---

## Vincoli tecnici critici

1. `html { scroll-snap-type: y mandatory }` — MAI su body
2. `body { overflow: visible }` — MAI `overflow-x: hidden`
3. `.vc-panel { height: 100vh }` — identico a `.slide`
4. Wheel listener su `.vc-wrap`: `{ passive: false }` — obbligatorio per `preventDefault()`
5. Canvas: `canvas.width = canvas.offsetWidth; canvas.height = canvas.offsetHeight;` — sempre esplicito
6. Leaflet `#map`: deve avere `width:100%; height:100%` — il `.vc-panel` gliene dà la dimensione
7. `initBlitzortung()`: se WebSocket non riceve messaggi entro 5s → fallback automatico
8. `vcMap` registrazione: dopo che il DOM è pronto, prima di `onSlideEnter`

---

## Cosa NON cambia

- Palette CSS (`--bg`, `--gold`, `--blue`, `--white`, `--muted`, `--border`)
- Font (Bebas Neue, Source Serif 4)
- Slide counter formato `"01 / 08"`
- Nav dots (8 dot, destra, gold active)
- Progress bar (sinistra, 3px, gold, height animata)
- Custom cursor (dot + ring con lerp 0.12)
- Fullscreen button (top-right)
- Scroll hint (scompare al primo scroll)

---

## Spec Self-Review

- ✅ Nessun TBD o placeholder
- ✅ Ogni panel ha layout esplicito (split/center/full), contenuto testuale completo in italiano
- ✅ Ogni canvas ha ID definito e algoritmo descritto
- ✅ VC wheel listener ha `passive:false` specificato
- ✅ Blitzortung ha fallback con zone reali
- ✅ Safety simulator: classi safe/medium/danger pre-assegnate nell'HTML
- ✅ Franklin S5P2 usa SVG statico (no SMIL) — più semplice e affidabile
- ✅ Keyboard nav: controlla VC interno prima di scrollare alla slide successiva
- ✅ `onSlideEnter` resetta il VC al panel 0 quando si rientra nella slide
- ✅ `initFormationCanvas` separato da `initCloudCharge` — contenuto diverso
- ✅ Scope: un file HTML, Leaflet da CDN, vanilla JS
