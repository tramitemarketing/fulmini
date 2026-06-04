# Fulmini v2 — Task 3: HTML Slides S3, S4, S5

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Popolare S3 (fisica estrema, 3 pannelli VC), S4 (il tuono, 3 pannelli VC), S5 (tipi di fulmini, 3 pannelli VC con HC) con il markup HTML completo.

**Architecture:** Ogni slide usa `.vc-wrap > .vc-track > .vc-panel[]`. S5 contiene 2 caroselli orizzontali (HC) nei pannelli P1 e P2.

**Tech Stack:** HTML5, classi CSS definite nel Task 1

**Prerequisito:** Task 1 + Task 2 completati

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezioni S3, S4, S5

---

### Task 1: Popola S3 — La Fisica Estrema (3 pannelli VC)

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Sostituisci `#s-fisica`**

Trova `<section id="s-fisica"    class="slide"><!-- Task 3 --></section>` e sostituisci con:

```html
<section id="s-fisica" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Termodinamica -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 03 · fisica estrema</span>
          <h2 class="slide-title">30.000 K in <em>un microsecondo</em></h2>
          <p class="slide-body">
            Durante il return stroke, l'effetto Joule scalda il canale da temperatura
            ambiente a <strong>30.000 K</strong> in meno di 10⁻⁶ secondi.
            Secondo P = ρRT, la pressione interna schizza a
            <strong>10–100 atmosfere</strong>. Il canale esplode radialmente
            a velocità supersonica (Mach&nbsp;&gt;&nbsp;1 nei primi 1–2 metri),
            generando un'<strong>onda d'urto idrodinamica</strong> che
            poi decade in onda acustica: il tuono.
          </p>
        </div>
        <div class="right">
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:.75rem;">
            <div class="stat-box"><span class="stat-label">Temperatura</span><span class="stat-value" style="font-size:1.2rem;">30.000 K</span></div>
            <div class="stat-box"><span class="stat-label">Pressione picco</span><span class="stat-value" style="font-size:1.2rem;">10–100 atm</span></div>
            <div class="stat-box"><span class="stat-label">Diametro canale</span><span class="stat-value" style="font-size:1.2rem;">2–3 cm</span></div>
            <div class="stat-box"><span class="stat-label">Durata return stroke</span><span class="stat-value" style="font-size:1.2rem;">0,1 ms</span></div>
            <div class="stat-box"><span class="stat-label">Espansione</span><span class="stat-value" style="font-size:1.2rem;">Mach&gt;1</span></div>
            <div class="stat-box"><span class="stat-label">Confronto Sole</span><span class="stat-value" style="font-size:1.2rem;">5×</span></div>
          </div>
        </div>
      </div>

      <!-- P2: EM + Z-Pinch -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">elettromagnetismo</span>
        <h2 class="slide-title">Il fulmine come <em>laboratorio</em></h2>
        <div class="two-cols" style="width:100%;max-width:800px;text-align:left;">
          <div>
            <h3 style="font-family:'Bebas Neue',sans-serif;font-size:.9rem;letter-spacing:.15em;color:var(--gold);margin-bottom:.5rem;">LEGGE DI AMPÈRE</h3>
            <p class="slide-body">
              Una corrente fino a 200.000 A genera un campo magnetico B circolare
              e intensissimo attorno al canale. Ogni elettrone in movimento
              è soggetto alla <strong>forza di Lorentz</strong>.
            </p>
          </div>
          <div>
            <h3 style="font-family:'Bebas Neue',sans-serif;font-size:.9rem;letter-spacing:.15em;color:var(--gold);margin-bottom:.5rem;">EFFETTO Z-PINCH</h3>
            <p class="slide-body">
              Il campo magnetico spinge le particelle cariche <em>verso l'interno</em>
              del canale: è lo <strong>Z-Pinch</strong> (strizione magnetica).
              Il plasma viene compresso e densificato per pochi microsecondi,
              in equilibrio dinamico tra compressione magnetica ed espansione termica.
            </p>
          </div>
        </div>
        <div class="info-box" style="max-width:800px;width:100%;text-align:left;margin-top:1rem;">
          La variazione rapidissima di corrente genera un potente <strong>impulso
          elettromagnetico</strong> rilevabile a centinaia di chilometri.
        </div>
        <canvas id="em-canvas" width="500" height="180" style="max-width:100%;margin-top:1rem;"></canvas>
        <button class="canvas-btn" id="em-play-btn">AVVIA IMPULSO</button>
      </div>

      <!-- P3: Chimica + Fisica Nucleare -->
      <div class="vc-panel" style="padding:2rem 4rem;display:grid;grid-template-columns:1fr 1fr;gap:3rem;align-items:start;align-content:center;">
        <div>
          <span class="slide-eyebrow">chimica</span>
          <h2 class="slide-title" style="font-size:clamp(1.8rem,3vw,2.8rem);">L'atmosfera <em>trasformata</em></h2>
          <ul style="list-style:none;margin-top:1rem;display:flex;flex-direction:column;gap:.75rem;">
            <li class="slide-body">🌿 <strong>Ozono O₃</strong> — l'energia del fulmine dissocia N₂ e O₂; gli atomi liberi si ricombinano formando ozono. L'odore "fresco" dopo il temporale è ozono.</li>
            <li class="slide-body">🌱 <strong>NOx</strong> — fissazione naturale dell'azoto atmosferico nel terreno: fertilizzante naturale per le piante.</li>
            <li class="slide-body">💎 <strong>Fulgurite</strong> — quando colpisce sabbia, la corrente fonde il quarzo (SiO₂) a 30.000 K creando una roccia vetrosa tubolare che replica la forma sotterranea della scarica.</li>
          </ul>
        </div>
        <div>
          <span class="slide-eyebrow">fisica nucleare</span>
          <h2 class="slide-title" style="font-size:clamp(1.8rem,3vw,2.8rem);">Acceleratore <em>naturale</em></h2>
          <ul style="list-style:none;margin-top:1rem;display:flex;flex-direction:column;gap:.75rem;">
            <li class="slide-body">☢️ <strong>TGF (Terrestrial Gamma-ray Flashes)</strong> — i campi elettrici dei temporali accelerano elettroni a velocità relativistiche. Frenando contro i nuclei dell'aria, emettono raggi X e gamma rilevabili dai satelliti.</li>
            <li class="slide-body">⚛️ <strong>Reazioni fotonucleari</strong> — i fotoni gamma colpiscono nuclei di ¹⁴N, strappando un neutrone e generando ¹³N instabile. Brevissima reazione nucleare spontanea in atmosfera.</li>
          </ul>
        </div>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S3 fisica estrema HTML (3 VC panels)"
```

---

### Task 2: Popola S4 — Il Tuono (3 pannelli VC)

- [ ] **Step 1: Sostituisci `#s-tuono`**

Trova `<section id="s-tuono"     class="slide"><!-- Task 3 --></section>` e sostituisci con:

```html
<section id="s-tuono" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Dal plasma all'onda -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 04 · il tuono</span>
          <h2 class="slide-title">Un'esplosione <em>supersonica</em></h2>
          <p class="slide-body">
            Il tuono non è un evento separato: è la diretta conseguenza
            termodinamica del fulmine. Il canale di plasma a 30.000°C
            — quasi 5 volte la superficie del Sole — non può contenere
            la pressione di 10–100 atm.<br><br>
            Si espande radialmente a regime <strong>supersonico</strong>
            (Mach&nbsp;&gt;&nbsp;1 nei primi metri): <strong>onda d'urto idrodinamica</strong>.
            Man mano che si allontana perde energia, rallenta sotto Mach 1:
            l'onda d'urto decade in normale <strong>onda acustica lineare</strong>.<br><br>
            Udibile fino a 20–25 km; oltre, la rifrazione
            acustica disperde le onde verso l'alto.
          </p>
        </div>
        <div class="right">
          <canvas id="thunder-canvas" width="340" height="260" style="max-width:100%;"></canvas>
        </div>
      </div>

      <!-- P2: Calcolatore distanza -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">calcola la distanza</span>
        <h2 class="slide-title">Lampo → Tuono → <em>distanza</em></h2>
        <p class="slide-body" style="margin-bottom:1.5rem;">
          La luce viaggia a 300.000 km/s: il lampo è istantaneo.
          Il suono viaggia a 343 m/s: circa 1 km ogni 3 secondi.
        </p>
        <div class="thunder-slider-wrap">
          <span class="slider-label">SECONDI TRA LAMPO E TUONO</span>
          <input type="range" id="thunder-slider" min="1" max="30" value="10">
          <span class="thunder-out" id="thunder-out">3.3 km</span>
          <span class="thunder-formula">km = secondi ÷ 3</span>
        </div>
        <p class="slide-body" style="margin-top:1.5rem;max-width:45ch;text-align:center;">
          9 secondi → 3 km. Se l'intervallo <em>diminuisce</em> tra un lampo e
          l'altro, il temporale si sta avvicinando.
        </p>
      </div>

      <!-- P3: Tabella dati fisici -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">dati fisici completi</span>
        <h2 class="slide-title">La scheda <em>tecnica</em></h2>
        <table class="data-table" style="max-width:620px;width:100%;margin-top:1.25rem;">
          <thead><tr><th>Parametro</th><th>Valore</th></tr></thead>
          <tbody>
            <tr><td>Temperatura canale</td><td>~30.000 K</td></tr>
            <tr><td>Corrente media</td><td>20.000–30.000 A</td></tr>
            <tr><td>Tensione</td><td>100 milioni – 1 miliardo di Volt</td></tr>
            <tr><td>Durata (con più scariche)</td><td>~0,2 secondi</td></tr>
            <tr><td>Energia per fulmine</td><td>1–5 miliardi di Joule</td></tr>
            <tr><td>Energia utilizzabile</td><td>~1 kWh (il resto è calore)</td></tr>
            <tr><td>Velocità return stroke</td><td>~100.000 km/s (c/3)</td></tr>
            <tr><td>Diametro canale plasma</td><td>2–3 cm</td></tr>
            <tr><td>Pressione picco</td><td>10–100 atm</td></tr>
          </tbody>
        </table>
        <p class="slide-body" style="margin-top:1.25rem;max-width:50ch;text-align:center;">
          Potrebbe far bollire 4 litri d'acqua. Ma la durata brevissima rende
          impossibile raccogliere e usare quell'energia in pratica.
        </p>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S4 tuono HTML (3 VC panels)"
```

---

### Task 3: Popola S5 — Tipi di Fulmini (3 pannelli VC con HC)

- [ ] **Step 1: Sostituisci `#s-tipi`**

Trova `<section id="s-tipi"      class="slide"><!-- Task 3 --></section>` e sostituisci con:

```html
<section id="s-tipi" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Fulmini CG (HC) -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 05 · tipi di fulmini</span>
          <h2 class="slide-title">Fulmini <em>comuni</em></h2>
          <div class="hc-wrap" id="cg-hc">
            <div class="hc-tabs">
              <button class="hc-tab active">CG⁻</button>
              <button class="hc-tab">CG⁺</button>
              <button class="hc-tab">IC</button>
              <button class="hc-tab">CC</button>
            </div>
            <div class="hc-track">
              <div class="hc-slide">
                <span class="phase-badge">NEGATIVO · ~90%</span>
                <p class="slide-body">Dal base della nuvola verso terra. Corrente media 30.000 A. Le cariche negative si accumulano alla base del cumulonembo.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">POSITIVO · ~10%</span>
                <p class="slide-body">Dall'incudine (sommità) verso terra. Fino a 300.000 A — 10× più potente. I tuoni più forti e sordi, udibili a decine di km. Statisticamente più pericolosi.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">INTRA-CLOUD · più frequente</span>
                <p class="slide-body">Il tipo più frequente in natura. Avviene interamente dentro il cumulonembo tra base − e cima +. Un picco improvviso di IC segnala che il temporale si sta intensificando.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">CLOUD-TO-CLOUD</span>
                <p class="slide-body">Scarica orizzontale tra due nuvole distinte con polarità opposta. Gli Anvil Crawlers percorrono centinaia di km con geometrie frattali nel cielo notturno.</p>
              </div>
            </div>
          </div>
        </div>
        <div class="right">
          <canvas id="type-canvas" width="300" height="380" style="max-width:100%;"></canvas>
        </div>
      </div>

      <!-- P2: Fenomeni rari TLE (HC) -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">fenomeni rari</span>
          <h2 class="slide-title">Fulmini <em>misteriosi</em></h2>
          <div class="hc-wrap" id="tle-hc">
            <div class="hc-tabs">
              <button class="hc-tab active">BALL</button>
              <button class="hc-tab">SPRITE</button>
              <button class="hc-tab">ELVES</button>
              <button class="hc-tab">BLUE JET</button>
            </div>
            <div class="hc-track">
              <div class="hc-slide">
                <span class="phase-badge">BALL LIGHTNING · mistero della fisica</span>
                <p class="slide-body">Sfera luminosa (arancione→bianca), dimensione arancia→pallone. Dura secondi, si muove lentamente, può passare attraverso vetri, si dissolve silenziosamente o esplode. La spiegazione fisica è ancora oggetto di dibattito scientifico.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">SPRITE · 50–90 km quota</span>
                <p class="slide-body">Strutture luminose rosse a forma di medusa con tentacoli verso il basso. Compaiono dopo un CG positivo molto potente. Durano pochi millisecondi. Documentati in foto solo dal 1989.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">ELVES · ~400 km (ionosfera)</span>
                <p class="slide-body">Emission of Light and VLF perturbations due to EMP Sources. Anelli luminosi piatti che si espandono alla velocità della luce. Diametro fino a 400 km. Causati dall'impulso EM del temporale sottostante.</p>
              </div>
              <div class="hc-slide">
                <span class="phase-badge">BLUE JET · 40–50 km</span>
                <p class="slide-body">Getti conici di luce blu espulsi dalla cima della nuvola verso l'alto. Il colore blu = eccitazione degli atomi di N₂ molecolare negli strati atmosferici più densi.</p>
              </div>
            </div>
          </div>
        </div>
        <div class="right">
          <canvas id="tle-canvas" width="300" height="380" style="max-width:100%;"></canvas>
        </div>
      </div>

      <!-- P3: Diagramma quota atmosferica -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">dove avvengono</span>
        <h2 class="slide-title">Dalla nuvola alla <em>ionosfera</em></h2>
        <div class="atmo-diagram" style="margin-top:1.5rem;">
          <div class="atmo-layer tle-elves">
            <span class="alt">~400 km</span>
            <span class="name">IONOSFERA</span>
            <span class="tle-name">⚪ ELVES — anelli che si espandono alla velocità della luce</span>
          </div>
          <div class="atmo-layer tle-sprite">
            <span class="alt">~90 km</span>
            <span class="name">MESOSFERA</span>
            <span class="tle-name">🔴 SPRITES — strutture rosse a medusa</span>
          </div>
          <div class="atmo-layer tle-jet">
            <span class="alt">~50 km</span>
            <span class="name">STRATOSFERA</span>
            <span class="tle-name">🔵 BLUE JETS — coni di luce blu verso l'alto</span>
          </div>
          <div class="atmo-layer tle-bolt">
            <span class="alt">~12 km</span>
            <span class="name">CUMULONEMBO</span>
            <span class="tle-name">⚡ FULMINI CG / IC / CC</span>
          </div>
          <div class="atmo-layer">
            <span class="alt">0 km</span>
            <span class="name">SUOLO</span>
            <span class="tle-name" style="color:var(--muted);">—</span>
          </div>
        </div>
        <p class="slide-body" style="margin-top:1.5rem;max-width:50ch;text-align:center;font-style:italic;">
          "Fino agli anni '90 i piloti riportavano 'luci strane' sopra i temporali.
          Nessuno li credeva. Oggi le chiamiamo TLE e le studiamo con i satelliti."
        </p>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S5 tipi di fulmini HTML (3 VC panels + 2 HC)"
```
