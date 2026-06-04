# Fulmini Rebuild v2 — Design Spec

**Data:** 2026-06-04  
**Fonte contenuti:** `FULIMINI.docx`  
**File target:** `index.html` (rewrite completo)

---

## 1. Architettura

Single-file HTML statico. Nessun build step. Vanilla JS + Leaflet CDN.

**Scroll engine:**
- `scroll-snap-type: y mandatory` su `html` (MAI su body)
- `body { overflow: visible }` — critico per scroll-snap
- `.slide { height:100vh; scroll-snap-align:start; scroll-snap-stop:always; }`

**Carousel systems:**
- **Verticale (VC):** `.vc-wrap > .vc-track > .vc-panel[]` — `translateY(n * -100vh)`, wheel/touch/keyboard
- **Orizzontale (HC):** `.hc-wrap > .hc-track > .hc-slide[]` — `translateX(n * -100%)`, click su tab/frecce

**Canvas:** inizializzati lazy su `onSlideEnter(i)`, protetti da `Set triggered`.

**Fonts:** Bebas Neue (headings/labels), Source Serif 4 (body) — Google Fonts CDN.

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
- `.panel-split` — flex row, metà sinistra testo / metà destra canvas
- `.panel-center` — flex column centrato, per content-heavy panels
- `.panel-full` — full bleed (mappa, griglia record)
- `.slide-eyebrow` — label sopra il titolo (Bebas Neue, 0.58rem, gold tenue)
- `.slide-title` — titolo principale (Bebas Neue, clamp 2.5rem–4.5rem)
- `.slide-body` — testo corpo (Source Serif 4, 0.88rem)

### Horizontal Carousel Classes
```css
.hc-wrap   { position:relative; width:100%; }
.hc-track  { display:flex; transition:transform .5s cubic-bezier(.4,0,.2,1); }
.hc-slide  { min-width:100%; flex-shrink:0; }
.hc-tabs   { display:flex; gap:.5rem; margin-bottom:1rem; }
.hc-tab    { padding:.35rem .8rem; border:1px solid rgba(240,192,64,.2);
             font-family:'Bebas Neue',sans-serif; font-size:.6rem;
             letter-spacing:.15em; cursor:pointer; color:var(--muted);
             background:transparent; transition:all .3s; }
.hc-tab.active { border-color:var(--gold); color:var(--gold);
                 background:rgba(240,192,64,.05); }
```

### Componenti riutilizzabili
- `.stat-box` — box bordo gold con icona, label, valore grande
- `.data-table` — tabella stilizzata dark con header gold
- `.myth-card` — card con claim in corsivo + truth in bold gold
- `.tl-item` — timeline: anno gold + testo
- `.record-cell` — cella record grid (numero grande + label + sub)
- `.deity-card` — card mitologia con nome, cultura, icona, descrizione
- `.safety-scen` — scenario sicurezza (verde/giallo/rosso, click → consiglio)
- `.phase-desc` — testo descrittivo fase carousel orizzontale

---

## 3. Struttura HTML — 10 Slide

### S0: Hero
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
Canvas: fulmini procedurali ramificati, trigger ogni 120–240 frame.

---

### S1: Cos'è un Fulmine (3 pannelli verticali)

**P1 — Definizione + Fisica di base**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 01 · cos'è un fulmine"
  title: "Una scarica <em>gigantesca</em>"
  body:
    Un fulmine è una scarica elettrica transitoria ad altissima intensità che
    si verifica nell'atmosfera per riequilibrare un forte dislivello di potenziale
    elettrico tra nuvole e suolo (o tra diverse nuvole).
    La scarica dura pochissimi millisecondi ma raggiunge temperature di circa
    <strong>30.000 K</strong> — 5 volte più calda della superficie del sole —
    e una corrente che può superare i <strong>30.000 ampere</strong>.
Destra: canvas id="charge-canvas"
  Animazione: nuvola con ioni + che salgono (gold) e ioni − che scendono (blue),
  terra con + per induzione. Barra tensione che si riempie → flash del fulmine.
  Click per resettare.
```

**P2 — Il Plasma: il quarto stato della materia**
```
Layout: panel-center (max-width 800px)
eyebrow: "la sostanza del fulmine"
title: "Non fuoco. <em>Plasma.</em>"
body:
  Il fulmine non è fatto di fuoco, né è semplice "elettricità". La sua sostanza
  fisica è il <strong>plasma</strong> — il quarto stato della materia.
  Quando il campo elettrico supera la rigidità dielettrica dell'aria
  (<strong>3×10⁶ V/m</strong>), gli elettroni vengono strappati dai nuclei
  atomici di N₂ e O₂. Il gas isolante si trasforma in una miscela di ioni
  positivi ed elettroni liberi: plasma conduttore a resistività bassissima,
  super-riscaldato, intensamente luminoso.

Sotto: plasma-states row  → Solido · Liquido · Gas · [Plasma ACTIVE]
Box blue-border:
  "Il canale plasma ha un diametro reale di appena <strong>2–3 centimetri</strong>.
   L'apparente larghezza del lampo è causata dallo scattering di Mie e Rayleigh:
   la luce del plasma rimbalza tra le gocce di pioggia illuminando km² di cielo."
```

**P3 — I Numeri**
```
Layout: panel-center
eyebrow: "parametri fisici"
title: "Numeri <em>estremi</em>"
3 stat-box grandi:
  ⚡ TENSIONE — 100M–1B Volt
  ⚡ CORRENTE MEDIA — 30.000 A  (picco: 200.000 A)
  ⚡ ENERGIA — 1–5 miliardi di Joule (≈ 1 kWh utilizzabile)
Nota sotto:
  "Un impianto domestico regge 16 Ampere. Il fulmine medio porta 30.000 A:
   quasi 2.000 volte di più. Ciò che lo rende letale è la potenza istantanea,
   non la quantità totale di energia."
```

---

### S2: Come si Forma (2 pannelli verticali)

**P1 — L'effetto triboelettrico**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 02 · formazione"
  title: "Il condensatore <em>naturale</em>"
  body:
    All'interno del cumulonembo, le correnti d'aria violente fanno scontrare
    cristalli di ghiaccio e graupel (grandine tenera). L'attrito —
    <strong>effetto triboelettrico</strong> — strappa elettroni:
    i cristalli leggeri si caricano <strong>positivamente</strong> e salgono,
    il graupel pesante si carica <strong>negativamente</strong> e scende.
    La nuvola diventa un <strong>condensatore naturale</strong>.
    La terra sotto sviluppa una carica positiva per
    <strong>induzione elettrostatica</strong>. Quando la differenza di
    potenziale supera ~3×10⁶ V/m, l'aria si ionizza e scatta la scarica.
Destra: canvas id="formation-canvas"
  Nuvola con cristalli + (gold) che salgono, graupel − (blue) che scende,
  segni + al suolo per induzione. Label ZONA+, ZONA−, SUOLO+.
```

**P2 — Le 4 fasi della scarica**
```
Layout: panel-split
Sinistra (max-width 45%):
  eyebrow: "meccanismo di scarica"
  Horizontal Carousel (hc-tabs):
    [A. Stepped Leader] [B. Upward Streamer] [C. Connessione] [D. Return Stroke]
  hc-slide A — desc:
    "Dal cumulonembo parte il <strong>precursore</strong>: un canale quasi
     invisibile che avanza a scatti da 50 m, a 150–200 km/s, cercando
     il percorso di minore resistenza. La sua traiettoria è frattale."
  hc-slide B — desc:
    "A poche centinaia di metri dal suolo, il campo elettrico intensissimo
     ionizza l'aria vicino ai punti prominenti. Salgono verso l'alto
     filamenti di plasma blu: gli <strong>upward streamers</strong>."
  hc-slide C — desc:
    "A 30–50 m dal suolo, stepped leader e upward streamer si incontrano.
     Il circuito si chiude: si crea un 'cavo virtuale' di plasma
     tra nuvola e terra."
  hc-slide D — desc:
    "Con il circuito chiuso, la resistenza crolla. Un'immensa ondata di
     carica risale dal suolo a <strong>100.000 km/s</strong> (c/3).
     È questo il lampo che vediamo — risale dal basso, non scende."
  Bottone PLAY/PAUSA
Destra: canvas id="leader-canvas"
  Animazione fase corrente, auto-avanza ogni 120 frame se in play.
```

---

### S3: La Fisica Estrema (3 pannelli verticali)

**P1 — Termodinamica**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 03 · fisica estrema"
  title: "30.000 K in <em>un microsecondo</em>"
  body:
    Durante il return stroke, l'effetto Joule scalda il canale da temperatura
    ambiente a <strong>30.000 K</strong> in meno di 10⁻⁶ secondi.
    Secondo P = ρRT, la pressione interna schizza a
    <strong>10–100 atmosfere</strong>. Il canale esplode radialmente
    a velocità supersonica (Mach > 1 nei primi 1–2 metri),
    generando un'<strong>onda d'urto idrodinamica</strong> che
    poi decade in onda acustica: il tuono.
Destra: layout a 2 colonne di stat mini:
  Temperatura: 30.000 K
  Pressione picco: 10–100 atm
  Diametro canale: 2–3 cm
  Durata return stroke: 0,1 ms
  Espansione: supersonica (M>1)
  Confronto: 5× superficie del Sole
```

**P2 — Elettromagnetismo & Z-Pinch**
```
Layout: panel-center (2 colonne)
eyebrow: "elettromagnetismo"
title: "Il fulmine come <em>laboratorio</em>"
Colonna sinistra — Legge di Ampère:
  "Una corrente fino a 200.000 A genera un campo magnetico B circolare
   e intensissimo attorno al canale. Ogni elettrone in movimento
   è soggetto alla <strong>forza di Lorentz</strong>."
Colonna destra — Effetto Z-Pinch:
  "Il campo magnetico spinge le particelle cariche <em>verso l'interno</em>
   del canale: è lo <strong>Z-Pinch</strong> (strizione magnetica).
   Il plasma viene compresso e densificato per pochi microsecondi,
   in un equilibrio dinamico violentissimo tra compressione magnetica
   ed espansione termica."
Box bottom — Impulso EM:
  "La variazione rapidissima di corrente genera un potente impulso
   elettromagnetico rilevabile a centinaia di chilometri."
canvas id="em-canvas" (bottone AVVIA → bolt + cerchi concentrici colorati)
```

**P3 — Chimica & Fisica Nucleare**
```
Layout: panel-split (2 colonne uguali)
Colonna sinistra — Chimica ad Alta Energia:
  eyebrow: "chimica"
  title: "L'atmosfera <em>trasformata</em>"
  Lista:
    🌿 Ozono O₃ — l'energia del fulmine dissocia N₂ e O₂; gli atomi
       liberi si ricombinano formando ozono. L'odore "fresco" dopo
       il temporale è ozono.
    🌱 NOx — fissazione naturale dell'azoto atmosferico nel terreno:
       fertilizzante naturale per le piante.
    💎 Fulgurite — quando colpisce sabbia, la corrente fonde il quarzo
       (SiO₂) a 30.000 K creando una roccia vetrosa tubolare che
       replica la forma sotterranea della scarica.

Colonna destra — Fisica Nucleare:
  eyebrow: "fisica nucleare"
  title: "Acceleratore <em>naturale</em>"
  Lista:
    ☢️ TGF (Terrestrial Gamma-ray Flashes) — i campi elettrici dei
       temporali accelerano elettroni a velocità relativistiche.
       Frenando contro i nuclei dell'aria, emettono raggi X e gamma
       rilevabili dai satelliti.
    ⚛️ Reazioni fotonucleari — i fotoni gamma colpiscono nuclei di
       ¹⁴N, strappando un neutrone e generando ¹³N instabile.
       Brevissima reazione nucleare spontanea in atmosfera.
```

---

### S4: Il Tuono (3 pannelli verticali)

**P1 — Dal Plasma all'Onda**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 04 · il tuono"
  title: "Un'esplosione <em>supersonica</em>"
  body:
    Il tuono non è un evento separato: è la diretta conseguenza
    termodinamica del fulmine. Il canale di plasma a 30.000°C
    — quasi 5 volte la superficie del Sole — non può contenere
    la pressione di 10–100 atm.
    Si espande radialmente a regime <strong>supersonico</strong>
    (Mach > 1 nei primi metri): <strong>onda d'urto idrodinamica</strong>.
    Man mano che si allontana perde energia, rallenta sotto Mach 1:
    l'onda d'urto decade in normale <strong>onda acustica lineare</strong>.
    Questo è il tuono. Udibile fino a 20–25 km; oltre, la rifrazione
    acustica disperde le onde verso l'alto.
Destra: schema visuale canvas id="thunder-canvas"
  Fulmine sinistra, cerchi-arco che si espandono verso destra.
  Auto-loop.
```

**P2 — Calcolatore della Distanza**
```
Layout: panel-center
eyebrow: "calcola la distanza"
title: "Lampo → Tuono → <em>distanza</em>"
body:
  La luce viaggia a 300.000 km/s: il lampo è istantaneo.
  Il suono viaggia a 343 m/s: circa 1 km ogni 3 secondi.

Slider id="thunder-slider" min=1 max=30 valore default=10
Label: "SECONDI TRA LAMPO E TUONO"
Output grande id="thunder-out" → (valore/3).toFixed(1) km
Formula mostrata: "km = secondi ÷ 3"

Esempio pratico:
  "9 secondi → 3 km. Se l'intervallo diminuisce tra un lampo e l'altro,
   il temporale si avvicina."
```

**P3 — Tabella Dati Fisici**
```
Layout: panel-center
eyebrow: "dati fisici completi"
title: "La scheda <em>tecnica</em>"
data-table:
  Temperatura canale       | ~30.000 K
  Corrente media           | 20.000–30.000 A
  Tensione                 | 100 milioni – 1 miliardo di Volt
  Durata (con più scariche)| ~0,2 secondi
  Energia per fulmine      | 1–5 miliardi di Joule
  Energia utilizzabile     | ~1 kWh (il resto è calore)
  Velocità return stroke   | ~100.000 km/s (c/3)
  Diametro canale plasma   | 2–3 cm
  Pressione picco          | 10–100 atm

Nota sotto tabella:
  "Potrebbe far bollire 4 litri d'acqua. Ma la durata brevissima rende
   impossibile raccogliere e usare quell'energia in pratica."
```

---

### S5: Tipi di Fulmini (3 pannelli verticali)

**P1 — Fulmini CG (Horizontal Carousel)**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 05 · tipi di fulmini"
  title: "Fulmini <em>comuni</em>"
  hc-tabs: [CG⁻] [CG⁺] [IC] [CC]
  hc-slide CG⁻:
    badge: "NEGATIVO · ~90%"
    desc: "Dal base della nuvola verso terra. Corrente media 30.000 A.
           Le cariche negative si accumulano alla base del cumulonembo."
  hc-slide CG⁺:
    badge: "POSITIVO · ~10%"
    desc: "Dall'incudine (sommità) verso terra. Fino a 300.000 A —
           10× più potente. I tuoni più forti e sordi, udibili a
           decine di km. Statisticamente più pericolosi."
  hc-slide IC:
    badge: "INTRA-CLOUD · più frequente"
    desc: "Il tipo più frequente in natura. Avviene interamente dentro
           il cumulonembo tra base − e cima +. Un picco improvviso
           di IC segnala che il temporale si sta intensificando."
  hc-slide CC:
    badge: "CLOUD-TO-CLOUD"
    desc: "Scarica orizzontale tra due nuvole distinte con polarità
           opposta. Gli Anvil Crawlers percorrono centinaia di km
           con geometrie frattali nel cielo notturno."
Destra: canvas id="type-canvas" — animazione tipo corrente
```

**P2 — Fenomeni Rari (Horizontal Carousel)**
```
Layout: panel-split
Sinistra:
  eyebrow: "fenomeni rari"
  title: "Fulmini <em>misteriosi</em>"
  hc-tabs: [BALL] [SPRITE] [ELVES] [BLUE JET]
  hc-slide BALL:
    badge: "BALL LIGHTNING · mistero della fisica"
    desc: "Sfera luminosa (arancione→bianca), dimensione arancia→pallone.
           Dura secondi, si muove lentamente, può passare attraverso
           vetri, si dissolve silenziosamente o esplode. La spiegazione
           fisica è ancora oggetto di dibattito scientifico."
  hc-slide SPRITE:
    badge: "SPRITE · 50–90 km quota"
    desc: "Strutture luminose rosse a forma di medusa con tentacoli
           verso il basso. Compaiono dopo un CG positivo molto potente.
           Durano pochi millisecondi. Documentati in foto solo dal 1989."
  hc-slide ELVES:
    badge: "ELVES · ~400 km (ionosfera)"
    desc: "Emission of Light and VLF perturbations due to EMP Sources.
           Anelli luminosi piatti che si espandono alla velocità della
           luce. Diametro fino a 400 km. Causati dall'impulso EM
           del temporale sottostante."
  hc-slide BLUE JET:
    badge: "BLUE JET · 40–50 km"
    desc: "Getti conici di luce blu espulsi dalla cima della nuvola
           verso l'alto. Il colore blu = eccitazione degli atomi di
           N₂ molecolare negli strati atmosferici più densi."
Destra: canvas id="tle-canvas" — animazione tipo corrente
```

**P3 — Diagramma Quota Atmosferica**
```
Layout: panel-center
eyebrow: "dove avvengono"
title: "Dalla nuvola alla <em>ionosfera</em>"
Visualizzazione verticale stilizzata (SVG o canvas):
  [~400 km] IONOSFERA    ══ ELVES (anelli che si espandono)
  [~90  km] MESOSFERA    ── SPRITES (meduse rosse)
  [~50  km] STRATOSFERA  ── BLUE JETS (coni blu)
  [~12  km] CUMULONEMBO  ═══ FULMINE STANDARD (CG/IC/CC)
  [  0  km] SUOLO

Nota:
  "Fino agli anni '90 i piloti riportavano 'luci strane' sopra i
   temporali. Nessuno li credeva. Oggi le chiamiamo TLE e le
   studiamo con i satelliti."
```

---

### S6: Numeri & Record (3 pannelli verticali)

**P1 — Record Mondiali (grid)**
```
Layout: panel-full (records-grid 2×3)
eyebrow assoluto: "slide 06 · numeri e record"
6 record-cell:
  8 MLN      | fulmini al giorno nel mondo
             | sub: 50–100 scariche ogni secondo
  768 KM     | mega-flash Texas–Mississippi
             | sub: record distanza orizzontale, 2020
  17,1 SEC   | record durata — Uruguay–Argentina, 2019
             | sub: come Milano–Napoli percorsa di luce in 0,002 s
  297 NOTTI  | Lago Maracaibo — fulmini ogni anno
             | sub: 28 scariche al minuto, 9 ore consecutive
  ×1.000     | più potenti i fulmini di Giove
             | sub: Saturno: rilevati da Cassini; Urano: scoperta recente
  100.000 KM/S | velocità return stroke
             | sub: c/3 — giro della Terra in < 0,5 secondi
```

**P2 — Curiosità Fisiche Rare**
```
Layout: 3 card orizzontali (o panel-split)
eyebrow: "curiosità rare"
title: "Fulmini che <em>sorprendono</em>"
Card 1 — Figure di Lichtenberg:
  "Quando una persona sopravvive a un fulmine, sulla pelle compaiono
   segni rossastri a forma di felce: le <strong>figure di Lichtenberg</strong>.
   Causate dalla rottura dei capillari per il passaggio della corrente
   sulla superficie corporea. Ogni figura è unica come un'impronta."
Card 2 — Fulmini Vulcanici:
  "Un fulmine non ha bisogno di nuvole di pioggia. Durante eruzioni
   violente, l'attrito tra particelle di cenere e roccia genera
   separazione di cariche così intensa da provocare
   <strong>fulmini spettacolari nel pennacchio di fumo</strong>."
Card 3 — Fulmini nello Spazio:
  "I fulmini non sono un'esclusiva terrestre. Nell'atmosfera di
   <strong>Giove e Saturno</strong> avvengono tempeste elettriche
   migliaia di volte più potenti delle nostre, alimentate da
   idrogeno ed elio in stato plasmatico."
```

**P3 — Debunking**
```
Layout: panel-split
Sinistra:
  eyebrow: "miti da sfatare"
  title: "Il fulmine colpisce sempre <em>il punto più alto?</em>"
  body:
    <strong>No.</strong> Il fulmine segue esclusivamente il percorso
    di <strong>minore resistenza elettrica</strong>, non l'altezza
    geometrica.
    Lo stepped leader che scende è "cieco" fino agli ultimi 30–50 m:
    è guidato solo dall'umidità, dalla ionizzazione locale e dalla
    conducibilità dell'aria — non dall'altezza degli oggetti sottostanti.
    Se l'aria sopra un edificio alto è secca e stabile, mentre sopra
    un prato basso è umida e ionica, il fulmine colpirà il prato.
    L'altezza e le punte metalliche <em>aumentano la probabilità</em>
    di innescare un upward streamer, ma non garantiscono nulla.
Destra:
  Stat box grande:
    🏢 EMPIRE STATE BUILDING
    "Colpito in media <strong>23 volte all'anno</strong>."
    "Il mito del 'non colpisce due volte nello stesso posto'
     è fisicamente assurdo."
  myth-list con 2 miti:
    - "Il canale del fulmine è largo" → il canale è 2–3 cm
    - "Il fulmine produce solo luce e calore" → TGF, NOx, fulguriti, reazioni nucleari
```

---

### S7: Franklin & Storia (3 pannelli verticali)

**P1 — Contesto Storico**
```
Layout: panel-split
Sinistra:
  eyebrow: "slide 07 · franklin e storia"
  title: "L'elettricità era <em>uno spettacolo</em>"
  body:
    A metà del XVIII secolo, l'elettricità era poco più di un curioso
    gioco da salotto. I filosofi naturali accumulavano cariche nelle
    <strong>bottiglie di Leida</strong> e divertivano l'aristocrazia
    con scintille. L'idea che il fulmine e l'elettricità fossero la
    stessa cosa era considerata bizzarra, quasi blasfema.
    Franklin, autodidatta delle colonie americane, fu il primo a intuire
    che le scintille di laboratorio e i fulmini celesti erano
    lo stesso fenomeno su scala diversa.
Destra: timeline tl-list
  1746 — Pieter van Musschenbroek inventa la bottiglia di Leida,
          primo condensatore della storia
  1750 — Franklin scrive alla Royal Society proponendo di "catturare
          l'elettricità celeste". Viene preso per pazzo.
  1752 — Esperimento dell'aquilone a Filadelfia (giugno)
  1753 — Georg Wilhelm Richmann, San Pietroburgo: primo martire
          della scienza elettrica
  1800s — Il parafulmine si diffonde in tutto il mondo occidentale
  1891  — Tesla e la Bobina: scariche artificiali spettacolari
```

**P2 — L'Esperimento dell'Aquilone**
```
Layout: panel-split
Sinistra: SVG anatomia apparato sperimentale
  - Nuvola temporalesca in cima
  - Aquilone di seta con filo metallico appuntito
  - Cavo di canapa bagnata (conduttore)
  - Chiave di ferro al nodo canapa/seta
  - Nastro di seta asciutta (isolante) — Franklin sotto tettoia
  - Bottiglia di Leida in fondo
  Label per ogni elemento
Destra:
  eyebrow: "l'esperimento"
  title: "Induzione, non <em>colpo diretto</em>"
  body:
    Franklin non aspettava un fulmine diretto — sarebbe morto.
    Sfruttava l'<strong>induzione elettrostatica</strong>: la nuvola
    carica induceva cariche libere lungo la canapa bagnata.
    Avvicinando il nocchiolo del dito alla chiave carica, generava
    una <strong>scintilla controllata</strong>. La bottiglia di Leida
    raccoglieva la carica celeste: prova definitiva che elettricità
    di laboratorio e fulmini erano la stessa cosa.
  warn-pill:
    "⚠ Richmann tentò lo stesso esperimento a San Pietroburgo l'anno
     successivo. Fu colpito e ucciso il 6 agosto 1753 — prima vittima
     accertata nella storia degli esperimenti elettrici."
```

**P3 — Il Parafulmine**
```
Layout: panel-split
Sinistra:
  eyebrow: "la conseguenza pratica"
  title: "Da terrore divino a rischio <em>calcolabile</em>"
  body:
    Franklin comprese che un'asta metallica appuntita collegata a
    terra avrebbe prodotto due effetti fisici protettivi:
    <strong>A. Effetto Punta (prevenzione):</strong> Le cariche si
    concentrano sulle punte. Il campo elettrico intensissimo ionizza
    costantemente l'aria circostante, disperdendo silenziosamente
    le cariche della nuvola e riducendo localmente la differenza
    di potenziale.
    <strong>B. Canalizzazione sicura (protezione):</strong> Se il
    fulmine scatta comunque, il conduttore metallico (rame/acciaio)
    offre il percorso a minima resistenza. La corrente scorre nel
    cavo, si disperde nel terreno, senza toccare pietra o legno.
Destra: tl-list post-Franklin
  1753 — Richmann muore. Il parafulmine diventa ingegneria seria.
  1800s — Franklin rifiuta il brevetto: "È per il bene dell'umanità."
  1891  — Tesla crea la Bobina Tesla. Sogna la trasmissione wireless
           di energia elettrica gratuita per tutti.
  Oggi  — Reti di rilevamento in tempo reale, razzi con filo metallico
           per "provocare" fulmini controllati in laboratorio.
```

---

### S8: Mitologia & Scienza (3 pannelli verticali)

**P1 — I Dei del Fulmine**
```
Layout: panel-center
eyebrow: "slide 08 · mitologia"
title: "Prima della <em>fisica</em>"
4 deity-card in grid 2×2:
  ⚡ ZEUS (Grecia)
    "Re degli dei. Il fulmine era la sua arma assoluta, simbolo
     di potere e giustizia divina. I luoghi colpiti dai fulmini
     erano sacri — i greci li chiamavano <em>enelysion</em>."
  🔨 THOR (Norrena)
    "Dio del tuono. Il suo martello Mjolnir creava i fulmini
     durante i viaggi tra i regni. Protettore degli uomini
     contro il caos delle forze della natura."
  ⚡ GIOVE (Roma)
    "Equivalente romano di Zeus. I fulmini erano i suoi
     strali divini. I pontifex romani interpretavano la
     direzione e il tipo di fulmine come augurio o condanna."
  ✨ INDRA (Induismo)
    "Dio della guerra e dei temporali. Armato del vajra —
     il fulmine cosmico — sconfisse il demone-serpente Vritra
     liberando le acque del mondo."
```

**P2 — Dopo Franklin: La Scienza Moderna**
```
Layout: panel-split
Sinistra:
  eyebrow: "la storia della scienza"
  title: "Da Franklin <em>ai satelliti</em>"
  tl-list:
    1753 — Richmann: prima vittima. Il parafulmine diventa
            un oggetto serio di ingegneria.
    1891 — Nikola Tesla sviluppa la Bobina Tesla. Scariche
            artificiali spettacolari. Sogna la trasmissione
            wireless di energia gratuita.
    1960s — I razzi con filo metallico permettono di
             "provocare" fulmini artificiali in laboratorio
             per studiarli da vicino in sicurezza.
    1995  — NASA lancia il Lightning Imaging Sensor (LIS)
             sui satelliti. Prima mappa globale completa
             della distribuzione dei fulmini.
    1989  — Prima fotografia documentata di uno Sprite da
             un aereo dell'Università del Minnesota.
Destra:
  eyebrow: "rilevamento moderno"
  title: "La rete <em>invisibile</em>"
  body:
    Ogni fulmine emette un impulso radio sferico. Tre o più
    stazioni lo ricevono con differenze di tempo di frazioni
    di microsecondo. La <strong>triangolazione time-of-arrival</strong>
    localizza la scarica con precisione di <strong>pochi metri</strong>.
  detect-list:
    LINET  — Europa: 135+ sensori, copertura continentale
    SIRF   — Italia (ISPRA): 1,5 milioni di CG/anno, mappe stagionali
    ENTLN  — Globale: 900+ sensori worldwide
    ISUAL  — Satellite: rileva TGF, Sprite e Elves dallo spazio
```

**P3 — Curiosità finali**
```
Layout: panel-center (3 myth-card o note-card)
eyebrow: "lo sapevi che"
title: "Tre fatti che <em>sorprendono</em>"
3 myth-card (usare classe .myth-item o simile, senza claim/truth — solo testo):
  "Il fulmine può fare il giro della Terra in meno di
   mezzo secondo alla velocità del return stroke (100.000 km/s)."
  "L'energia di un fulmine medio potrebbe far bollire
   circa 4 litri d'acqua. Ma è impossibile raccoglierla:
   dura 0,2 secondi e la maggior parte è calore disperso."
  "Il tuono non si sente oltre 20–25 km. Le onde sonore
   vengono piegate verso l'alto dagli strati d'aria a
   diverse temperature (rifrazione acustica) e si
   disperdono prima di raggiungere il suolo."
```

---

### S9: Nel Mondo & Protezione (3 pannelli verticali)

**P1 — Mappa Mondiale**
```
Layout: panel-full (map-panel)
<div id="map"></div>  — Leaflet dark (CartoCDN dark_all)
Centro: [20, 10], zoom 3
4 hotspot circleMarker gold con popup:
  [9.75, -71.6]  Lago Maracaibo — 230–260 fulmini/km²/anno · 297 notti
  [-2.5, 27.5]   Congo/Kifuka   — 205 fulmini/km²/anno · foresta equatoriale
  [27.9, -82.5]  Tampa, Florida — 1,2M fulmini/anno · convergenza brezze marine
  [45.5, 9.2]    Pianura Padana — hotspot autunnale · convezione violenta
map-counter in alto: "N FULMINI NEGLI ULTIMI 5 MIN"
  → WebSocket wss://ws1.blitzortung.org:8082/ con 5s timeout fallback simulato
```

**P2 — L'Italia**
```
Layout: panel-split
Sinistra:
  eyebrow: "il caso italia"
  title: "1,5 milioni di fulmini <em>l'anno</em>"
  body:
    L'Italia è uno dei paesi europei più esposti. La complessa
    orografia e la posizione centrale nel Mediterraneo creano
    meccanismi diversi per area geografica.
  data-table:
    Arco Alpino/Prealpi | Estate (Giu–Ago) | Sollevamento orografico
    Pianura Padana       | Tarda est/Autunno | Convezione termica + fronte freddo
    Tirreno/Appennini    | Autunno (Set–Nov) | Instabilità marittima
    Costa Adriatica      | Autunno           | Bora + umidità adriatica
  body:
    "Il paradosso della Pianura Padana: d'estate si comporta come
     una conca subtropicale. Quando un fronte freddo scavalca le Alpi,
     l'impatto con l'aria surriscaldata genera
     <strong>temporali supercellulari</strong> tra i più violenti d'Europa."
Destra: mini-mappa SVG dell'Italia con le 4 zone evidenziate
```

**P3 — Come Proteggersi**
```
Layout: panel-split
Sinistra:
  eyebrow: "protezione"
  title: "Cosa fare <em>e non fare</em>"
  safety-grid 3×2:
    🌲 Sotto un albero   [DANGER]  → Mai. Bersaglio preferito. 50 m di distanza.
    🚗 In auto           [SAFE]    → Gabbia di Faraday. Non toccare la scocca.
    🏠 In edificio       [SAFE]    → Sicuro. Stacca apparecchi. No doccia.
    🏊 In acqua          [DANGER]  → Esci subito. La corrente si espande in superficie.
    ⛰️  In cima          [DANGER]  → Scendi. Accovacciati, piedi uniti, mani sulle orecchie.
    🌾 Campo aperto      [MEDIUM]  → Posizione "a rana" sui talloni. Non sdraiarti.
  Click su scenario → mostra consiglio completo
Destra:
  eyebrow: "mito smontato"
  title: "Non colpisce <em>due volte?</em>"
  body:
    "Falso. L'Empire State Building viene colpito
     <strong>23 volte all'anno in media</strong>.
     Un fulmine colpisce dove trova il percorso di minore
     resistenza — e se quel percorso è buono,
     lo userà di nuovo."
  detect-list (riepilgo rilevamento):
    LINET / SIRF / ENTLN / ISUAL
```

---

## 4. Sistema Verticale Carousel (VC) — JS

```js
function initVerticalCarousel(slideEl) {
  const track = slideEl.querySelector('.vc-track');
  const panels = [...slideEl.querySelectorAll('.vc-panel')];
  const dots = [...slideEl.querySelectorAll('.vc-dot')];
  if (!track || panels.length < 2) return null;
  let idx = 0, busy = false;
  function go(n) {
    if (n < 0 || n >= panels.length || busy) return;
    busy = true; idx = n;
    track.style.transform = `translateY(calc(${n} * -100vh))`;
    dots.forEach((d, i) => d.classList.toggle('active', i === n));
    setTimeout(() => { busy = false; }, 700);
  }
  dots.forEach((d, i) => d.addEventListener('click', () => go(i)));
  slideEl.addEventListener('wheel', e => {
    const atEnd = (e.deltaY > 0 && idx >= panels.length - 1) || (e.deltaY < 0 && idx <= 0);
    if (!atEnd) { e.preventDefault(); e.stopPropagation(); }
    if (busy) return;
    if (e.deltaY > 0 && idx < panels.length - 1) go(idx + 1);
    else if (e.deltaY < 0 && idx > 0) go(idx - 1);
  }, { passive: false });
  // touch
  let ty = 0;
  slideEl.addEventListener('touchstart', e => { ty = e.touches[0].clientY; }, { passive: true });
  slideEl.addEventListener('touchend', e => {
    const dy = e.changedTouches[0].clientY - ty;
    if (dy < -50 && idx < panels.length - 1) go(idx + 1);
    else if (dy > 50 && idx > 0) go(idx - 1);
  });
  return { atTop: () => idx === 0, atBottom: () => idx === panels.length - 1,
           next: () => go(idx + 1), prev: () => go(idx - 1), reset: () => go(0) };
}
```

## 5. Sistema Horizontal Carousel (HC) — JS

```js
function initHorizontalCarousel(wrapEl) {
  const track = wrapEl.querySelector('.hc-track');
  const slides = [...wrapEl.querySelectorAll('.hc-slide')];
  const tabs = [...wrapEl.querySelectorAll('.hc-tab')];
  if (!track || slides.length < 2) return;
  let idx = 0;
  function go(n) {
    idx = Math.max(0, Math.min(n, slides.length - 1));
    track.style.transform = `translateX(calc(${idx} * -100%))`;
    tabs.forEach((t, i) => t.classList.toggle('active', i === idx));
  }
  tabs.forEach((t, i) => t.addEventListener('click', () => go(i)));
  return { go };
}
// Tutti gli HC si inizializzano in onSlideEnter del rispettivo slide
```

## 6. Canvas — Algoritmi

| ID | Slide | Algoritmo |
|----|-------|-----------|
| `hero-canvas` | S0 | Fulmini procedurali ricorsivi con branching 40%, trigger ogni 120–240 frame (pre-computato), fade out |
| `charge-canvas` | S1-P1 | 50 particelle +/− che salgono/scendono, barra tensione 0→1, flash, click reset |
| `formation-canvas` | S2-P1 | Nuvola ellisse, cristalli + verso alto, graupel − verso basso, label zone |
| `leader-canvas` | S2-P2 | 4 fasi animate: stepped dashed, streamer blu, flash pulsante, return stroke |
| `type-canvas` | S5-P1 | Animazione tipo CG: leader → flash → return stroke per ogni tipo |
| `tle-canvas` | S5-P2 | Ball: orb radiale fluttuante; Sprite: medusa rossa; Elves: anello espandente; Blue Jet: cono blu |
| `em-canvas` | S3-P2 | Bolt → cerchi concentrici colorati (hsl shift), bottone AVVIA |
| `thunder-canvas` | S4-P1 | Bolt sinistra, archi-onda che si espandono verso destra in loop |

## 7. onSlideEnter Dispatcher

```js
// S0=hero, S1=cosae, S2=formazione, S3=fisica, S4=tuono,
// S5=tipi, S6=record, S7=franklin, S8=mitologia, S9=mondo
const inits = [
  initHeroCanvas,                                       // S0
  initCloudCharge,                                      // S1 — no HC
  () => { initFormationCanvas(); initSteppedLeader(); },// S2 — HC in initSteppedLeader
  initEMPulse,                                          // S3
  () => { initThunderCalc(); initThunderCanvas(); },    // S4
  () => { initTypeVisualizer(); initTLEVisualizer(); }, // S5 — HC dentro ogni init
  () => { },                                            // S6
  () => { },                                            // S7
  () => { },                                            // S8
  () => { initBlitzortung(); initSafetySimulator(); },  // S9
];
// Ogni initXxx che gestisce un HC chiama initHorizontalCarousel(wrapEl) internamente.
```

## 8. Fixed UI

- `#cursor` + `#cursor-ring` — RAF lerp 0.12
- `#progress-bar` — left:0, width:3px, height = i/(n-1)*100%
- `#nav-dots` — 10 ndot (right side)
- `#slide-counter` — "01 / 10"
- `#fullscreen-btn` — tasto F o click
- `#scroll-hint` — scompare al primo scroll

---

## 9. Note Implementative

- `scroll-snap-type: y mandatory` SOLO su `html`
- `body { overflow: visible }` — MAI hidden o scroll
- `.map-panel { display: block !important; padding: 0 !important; }` — Leaflet richiede block
- HC: `hc-track` usa `width: 100%` e `hc-slide` usa `min-width: 100%` (non 100vw)
- Canvas: sempre `canvas.width = canvas.offsetWidth` prima di disegnare
- Tutti gli HC vengono inizializzati via `initHorizontalCarousels(slideSelector)` nel rispettivo `onSlideEnter`
- Nessun HC dentro S0 (hero) — solo il VC
