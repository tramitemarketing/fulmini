# Fulmini v2 — Task 2: HTML Slides S0, S1, S2

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Sostituire i placeholder di S0 (hero), S1 (cos'è un fulmine, 3 pannelli VC), S2 (come si forma, 2 pannelli VC) con il markup HTML completo.

**Architecture:** Ogni slide usa `.vc-wrap > .vc-track > .vc-panel[]` per il carosello verticale. Le sezioni `<!-- Task 2 -->` vengono sostituite con il markup completo. Nessun JS in questo task — solo HTML.

**Tech Stack:** HTML5, classi CSS definite nel Task 1

**Prerequisito:** Task 1 completato (`index.html` esiste con tutto il CSS e i placeholder)

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezioni S0, S1, S2

---

### Task 1: Popola S0 — Hero

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Sostituisci il contenuto di `#s-hero`**

Trova `<section id="s-hero"      class="slide"><!-- Task 2 --></section>` e sostituisci con:

```html
<section id="s-hero" class="slide" style="overflow:hidden;">
  <canvas id="hero-canvas" style="position:absolute;inset:0;width:100%;height:100%;z-index:1;pointer-events:none;"></canvas>
  <div style="position:relative;z-index:2;text-align:center;">
    <span class="hero-eyebrow">la scienza del lampo</span>
    <h1 class="hero-title">FUL<em>MI</em>NI</h1>
    <div class="hero-line"></div>
    <p class="hero-sub">Scariche elettriche atmosferiche — fisica, storia, misteri</p>
  </div>
</section>
```

- [ ] **Step 2: Commit parziale**

```bash
git add index.html
git commit -m "feat: add S0 hero HTML"
```

---

### Task 2: Popola S1 — Cos'è un Fulmine (3 pannelli VC)

- [ ] **Step 1: Sostituisci il contenuto di `#s-cosae`**

Trova `<section id="s-cosae"     class="slide"><!-- Task 2 --></section>` e sostituisci con:

```html
<section id="s-cosae" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Definizione + canvas -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 01 · cos'è un fulmine</span>
          <h2 class="slide-title">Una scarica <em>gigantesca</em></h2>
          <p class="slide-body">
            Un fulmine è una scarica elettrica transitoria ad altissima intensità che
            si verifica nell'atmosfera per riequilibrare un forte dislivello di potenziale
            elettrico tra nuvole e suolo (o tra diverse nuvole).<br><br>
            La scarica dura pochissimi millisecondi ma raggiunge temperature di circa
            <strong>30.000 K</strong> — 5 volte più calda della superficie del sole —
            e una corrente che può superare i <strong>30.000 ampere</strong>.
          </p>
          <p style="font-size:.72rem;color:var(--muted);margin-top:.75rem;font-style:italic;">
            Clicca il canvas per resettare l'animazione
          </p>
        </div>
        <div class="right">
          <canvas id="charge-canvas" width="340" height="300" style="max-width:100%;"></canvas>
        </div>
      </div>

      <!-- P2: Plasma -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">la sostanza del fulmine</span>
        <h2 class="slide-title">Non fuoco. <em>Plasma.</em></h2>
        <p class="slide-body">
          Il fulmine non è fatto di fuoco, né è semplice "elettricità". La sua sostanza
          fisica è il <strong>plasma</strong> — il quarto stato della materia.
          Quando il campo elettrico supera la rigidità dielettrica dell'aria
          (<strong>3×10⁶ V/m</strong>), gli elettroni vengono strappati dai nuclei
          atomici di N₂ e O₂. Il gas isolante si trasforma in una miscela di ioni
          positivi ed elettroni liberi: plasma conduttore a resistività bassissima,
          super-riscaldato, intensamente luminoso.
        </p>
        <div class="plasma-states">
          <div class="state">SOLIDO</div>
          <div class="state">LIQUIDO</div>
          <div class="state">GAS</div>
          <div class="state active">PLASMA</div>
        </div>
        <div class="info-box">
          Il canale plasma ha un diametro reale di appena <strong>2–3 centimetri</strong>.
          L'apparente larghezza del lampo è causata dallo scattering di Mie e Rayleigh:
          la luce del plasma rimbalza tra le gocce di pioggia illuminando km² di cielo.
        </div>
      </div>

      <!-- P3: Numeri -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">parametri fisici</span>
        <h2 class="slide-title">Numeri <em>estremi</em></h2>
        <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;width:100%;max-width:700px;margin:1.5rem 0;">
          <div class="stat-box">
            <span class="stat-icon">⚡</span>
            <span class="stat-label">Tensione</span>
            <span class="stat-value">100M–1B<br><span style="font-size:1rem;">Volt</span></span>
          </div>
          <div class="stat-box">
            <span class="stat-icon">⚡</span>
            <span class="stat-label">Corrente media</span>
            <span class="stat-value">30.000<br><span style="font-size:1rem;">Ampere</span></span>
          </div>
          <div class="stat-box">
            <span class="stat-icon">⚡</span>
            <span class="stat-label">Energia</span>
            <span class="stat-value">1–5 Mld<br><span style="font-size:1rem;">Joule</span></span>
          </div>
        </div>
        <p class="slide-body" style="max-width:55ch;text-align:center;">
          Un impianto domestico regge 16 Ampere. Il fulmine medio porta 30.000 A:
          quasi <strong>2.000 volte di più</strong>. Ciò che lo rende letale è la potenza
          istantanea, non la quantità totale di energia.
        </p>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S1 cos'è un fulmine HTML (3 VC panels)"
```

---

### Task 3: Popola S2 — Come si Forma (2 pannelli VC)

- [ ] **Step 1: Sostituisci il contenuto di `#s-formazione`**

Trova `<section id="s-formazione"class="slide"><!-- Task 2 --></section>` e sostituisci con:

```html
<section id="s-formazione" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Effetto triboelettrico + canvas -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 02 · formazione</span>
          <h2 class="slide-title">Il condensatore <em>naturale</em></h2>
          <p class="slide-body">
            All'interno del cumulonembo, le correnti d'aria violente fanno scontrare
            cristalli di ghiaccio e graupel (grandine tenera). L'attrito —
            <strong>effetto triboelettrico</strong> — strappa elettroni:
            i cristalli leggeri si caricano <strong>positivamente</strong> e salgono,
            il graupel pesante si carica <strong>negativamente</strong> e scende.
            La nuvola diventa un <strong>condensatore naturale</strong>.<br><br>
            La terra sotto sviluppa una carica positiva per
            <strong>induzione elettrostatica</strong>. Quando la differenza di
            potenziale supera ~3×10⁶ V/m, l'aria si ionizza e scatta la scarica.
          </p>
        </div>
        <div class="right">
          <canvas id="formation-canvas" width="320" height="320" style="max-width:100%;"></canvas>
        </div>
      </div>

      <!-- P2: 4 fasi (HC) + leader canvas -->
      <div class="vc-panel panel-split">
        <div class="left" style="max-width:45%;">
          <span class="slide-eyebrow">meccanismo di scarica</span>
          <div class="hc-wrap" id="leader-hc">
            <div class="hc-tabs">
              <button class="hc-tab active">A. STEPPED LEADER</button>
              <button class="hc-tab">B. STREAMER</button>
              <button class="hc-tab">C. CONNESSIONE</button>
              <button class="hc-tab">D. RETURN STROKE</button>
            </div>
            <div class="hc-track">
              <div class="hc-slide">
                <span class="phase-badge">FASE A</span>
                <p class="slide-body">
                  Dal cumulonembo parte il <strong>precursore</strong>: un canale quasi
                  invisibile che avanza a scatti da 50 m, a 150–200 km/s, cercando
                  il percorso di minore resistenza. La sua traiettoria è frattale.
                </p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">FASE B</span>
                <p class="slide-body">
                  A poche centinaia di metri dal suolo, il campo elettrico intensissimo
                  ionizza l'aria vicino ai punti prominenti. Salgono verso l'alto
                  filamenti di plasma blu: gli <strong>upward streamers</strong>.
                </p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">FASE C</span>
                <p class="slide-body">
                  A 30–50 m dal suolo, stepped leader e upward streamer si incontrano.
                  Il circuito si chiude: si crea un 'cavo virtuale' di plasma
                  tra nuvola e terra.
                </p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">FASE D</span>
                <p class="slide-body">
                  Con il circuito chiuso, la resistenza crolla. Un'immensa ondata di
                  carica risale dal suolo a <strong>100.000 km/s</strong> (c/3).
                  È questo il lampo che vediamo — risale dal basso, non scende.
                </p>
              </div>
            </div>
          </div>
          <button class="canvas-btn" id="leader-play-btn">▶ PLAY</button>
        </div>
        <div class="right">
          <canvas id="leader-canvas" width="300" height="400" style="max-width:100%;"></canvas>
        </div>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S2 formazione HTML (2 VC panels + HC phases)"
```
