# Fulmini v2 — Task 4: HTML Slides S6, S7, S8, S9

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Popolare S6 (numeri e record), S7 (Franklin e storia), S8 (mitologia), S9 (nel mondo e protezione) con il markup HTML completo.

**Architecture:** Ogni slide usa `.vc-wrap > .vc-track > .vc-panel[]`. S9-P1 usa `#map` (Leaflet). Tutti i pannelli usano classi CSS definite nel Task 1.

**Tech Stack:** HTML5, classi CSS Task 1, Leaflet CDN (già importato)

**Prerequisito:** Task 1 + Task 2 + Task 3 completati

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezioni S6, S7, S8, S9

---

### Task 1: Popola S6 — Numeri & Record (3 pannelli VC)

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Sostituisci `#s-record`**

Trova `<section id="s-record"    class="slide"><!-- Task 4 --></section>` e sostituisci con:

```html
<section id="s-record" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Records grid -->
      <div class="vc-panel panel-full">
        <div style="display:flex;flex-direction:column;align-items:flex-start;padding:2rem 4rem .5rem;">
          <span class="slide-eyebrow">slide 06 · numeri e record</span>
          <h2 class="slide-title">I <em>numeri</em> del fulmine</h2>
        </div>
        <div class="records-grid" style="height:auto;padding:0 4rem 2rem;">
          <div class="record-cell"><span class="rec-value">8 MLN</span><span class="rec-label">fulmini al giorno nel mondo</span><span class="rec-sub">50–100 scariche ogni secondo</span></div>
          <div class="record-cell"><span class="rec-value">768 KM</span><span class="rec-label">mega-flash Texas–Mississippi</span><span class="rec-sub">record distanza orizzontale, 2020</span></div>
          <div class="record-cell"><span class="rec-value">17,1 SEC</span><span class="rec-label">record durata</span><span class="rec-sub">Uruguay–Argentina, 2019</span></div>
          <div class="record-cell"><span class="rec-value">297 NOTTI</span><span class="rec-label">Lago Maracaibo — fulmini ogni anno</span><span class="rec-sub">28 scariche al minuto, 9 ore consecutive</span></div>
          <div class="record-cell"><span class="rec-value">×1.000</span><span class="rec-label">più potenti i fulmini di Giove</span><span class="rec-sub">Saturno: rilevati da Cassini; Urano: scoperta recente</span></div>
          <div class="record-cell"><span class="rec-value">100.000 KM/S</span><span class="rec-label">velocità return stroke</span><span class="rec-sub">c/3 — giro della Terra in &lt; 0,5 secondi</span></div>
        </div>
      </div>

      <!-- P2: Curiosità rare -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">curiosità rare</span>
        <h2 class="slide-title">Fulmini che <em>sorprendono</em></h2>
        <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;max-width:900px;width:100%;margin-top:1.25rem;">
          <div class="myth-item">
            <strong style="color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;font-size:.85rem;display:block;margin-bottom:.4rem;">FIGURE DI LICHTENBERG</strong>
            Quando una persona sopravvive a un fulmine, sulla pelle compaiono segni rossastri a forma di felce.
            Causate dalla rottura dei capillari per il passaggio della corrente sulla superficie corporea. Ogni figura è unica come un'impronta.
          </div>
          <div class="myth-item">
            <strong style="color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;font-size:.85rem;display:block;margin-bottom:.4rem;">FULMINI VULCANICI</strong>
            Un fulmine non ha bisogno di nuvole di pioggia. Durante eruzioni violente, l'attrito tra particelle di cenere genera separazione di cariche così intensa da provocare fulmini spettacolari nel pennacchio di fumo.
          </div>
          <div class="myth-item">
            <strong style="color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;font-size:.85rem;display:block;margin-bottom:.4rem;">FULMINI NELLO SPAZIO</strong>
            I fulmini non sono un'esclusiva terrestre. Nell'atmosfera di <strong>Giove e Saturno</strong> avvengono tempeste elettriche migliaia di volte più potenti delle nostre, alimentate da idrogeno ed elio plasmatico.
          </div>
        </div>
      </div>

      <!-- P3: Debunking -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">miti da sfatare</span>
          <h2 class="slide-title">Il fulmine colpisce sempre <em>il punto più alto?</em></h2>
          <p class="slide-body">
            <strong>No.</strong> Il fulmine segue esclusivamente il percorso
            di <strong>minore resistenza elettrica</strong>, non l'altezza geometrica.<br><br>
            Lo stepped leader che scende è "cieco" fino agli ultimi 30–50 m:
            è guidato solo dall'umidità, dalla ionizzazione locale e dalla
            conducibilità dell'aria — non dall'altezza degli oggetti sottostanti.<br><br>
            L'altezza e le punte metalliche <em>aumentano la probabilità</em>
            di innescare un upward streamer, ma non garantiscono nulla.
          </p>
        </div>
        <div class="right" style="flex-direction:column;gap:1rem;align-items:flex-start;">
          <div class="stat-box" style="width:100%;">
            <span class="stat-icon">🏢</span>
            <span class="stat-label">EMPIRE STATE BUILDING</span>
            <span class="stat-value" style="font-size:1.5rem;">23×/anno</span>
            <p style="font-size:.72rem;color:var(--muted);margin-top:.4rem;">Il mito del "non colpisce due volte nello stesso posto" è fisicamente assurdo.</p>
          </div>
          <div class="myth-item" style="width:100%;">
            <em style="color:var(--muted);font-size:.78rem;">"Il canale del fulmine è largo"</em><br>
            <strong style="color:var(--gold);font-size:.78rem;">→ Il canale è appena 2–3 cm</strong>
          </div>
          <div class="myth-item" style="width:100%;">
            <em style="color:var(--muted);font-size:.78rem;">"Produce solo luce e calore"</em><br>
            <strong style="color:var(--gold);font-size:.78rem;">→ TGF, NOx, fulguriti, reazioni nucleari</strong>
          </div>
        </div>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S6 numeri e record HTML (3 VC panels)"
```

---

### Task 2: Popola S7 — Franklin & Storia (3 pannelli VC)

- [ ] **Step 1: Sostituisci `#s-franklin`**

Trova `<section id="s-franklin"  class="slide"><!-- Task 4 --></section>` e sostituisci con:

```html
<section id="s-franklin" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Contesto storico -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">slide 07 · franklin e storia</span>
          <h2 class="slide-title">L'elettricità era <em>uno spettacolo</em></h2>
          <p class="slide-body">
            A metà del XVIII secolo, l'elettricità era poco più di un curioso
            gioco da salotto. I filosofi naturali accumulavano cariche nelle
            <strong>bottiglie di Leida</strong> e divertivano l'aristocrazia
            con scintille. L'idea che il fulmine e l'elettricità fossero la
            stessa cosa era considerata bizzarra, quasi blasfema.<br><br>
            Franklin, autodidatta delle colonie americane, fu il primo a intuire
            che le scintille di laboratorio e i fulmini celesti erano
            lo stesso fenomeno su scala diversa.
          </p>
        </div>
        <div class="right" style="flex-direction:column;align-items:flex-start;gap:.5rem;">
          <div class="tl-item"><span class="tl-year">1746</span><span class="tl-text">Pieter van Musschenbroek inventa la bottiglia di Leida, primo condensatore della storia</span></div>
          <div class="tl-item"><span class="tl-year">1750</span><span class="tl-text">Franklin scrive alla Royal Society proponendo di "catturare l'elettricità celeste". Viene preso per pazzo.</span></div>
          <div class="tl-item"><span class="tl-year">1752</span><span class="tl-text">Esperimento dell'aquilone a Filadelfia (giugno)</span></div>
          <div class="tl-item"><span class="tl-year">1753</span><span class="tl-text">Georg Wilhelm Richmann, San Pietroburgo: primo martire della scienza elettrica</span></div>
          <div class="tl-item"><span class="tl-year">1800s</span><span class="tl-text">Il parafulmine si diffonde in tutto il mondo occidentale</span></div>
          <div class="tl-item"><span class="tl-year">1891</span><span class="tl-text">Tesla e la Bobina: scariche artificiali spettacolari</span></div>
        </div>
      </div>

      <!-- P2: Esperimento aquilone -->
      <div class="vc-panel panel-split">
        <div class="left" style="align-items:center;justify-content:center;display:flex;">
          <svg viewBox="0 0 220 380" width="180" style="max-width:100%;">
            <!-- Cloud -->
            <ellipse cx="110" cy="30" rx="65" ry="25" fill="#1a2040" stroke="rgba(58,143,255,.4)" stroke-width="1.5"/>
            <text x="110" y="35" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="10" fill="#3a8fff">NUVOLA TEMPORALESCA</text>
            <!-- Kite string -->
            <line x1="110" y1="55" x2="110" y2="130" stroke="rgba(240,192,64,.5)" stroke-width="1" stroke-dasharray="4,3"/>
            <!-- Kite shape -->
            <polygon points="110,65 90,100 110,130 130,100" fill="none" stroke="var(--gold)" stroke-width="1.5"/>
            <line x1="90" y1="100" x2="130" y2="100" stroke="rgba(240,192,64,.4)" stroke-width="1"/>
            <line x1="110" y1="65" x2="110" y2="130" stroke="rgba(240,192,64,.4)" stroke-width="1"/>
            <text x="155" y="100" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--muted)">AQUILONE DI SETA</text>
            <!-- Hemp rope -->
            <line x1="110" y1="130" x2="110" y2="220" stroke="rgba(122,128,153,.6)" stroke-width="2"/>
            <text x="118" y="180" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--muted)">CANAPA</text>
            <!-- Key -->
            <circle cx="110" cy="225" r="6" fill="none" stroke="var(--gold)" stroke-width="1.5"/>
            <line x1="110" y1="231" x2="110" y2="245" stroke="var(--gold)" stroke-width="1.5"/>
            <text x="120" y="228" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--gold)">CHIAVE</text>
            <!-- Silk insulator -->
            <line x1="110" y1="245" x2="110" y2="270" stroke="#e8eaf0" stroke-width="1.5"/>
            <text x="118" y="258" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--white)">SETA (ISO.)</text>
            <!-- Leyden jar -->
            <rect x="94" y="270" width="32" height="40" rx="4" fill="none" stroke="rgba(58,143,255,.5)" stroke-width="1.5"/>
            <text x="110" y="296" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="#3a8fff">BOTTIGLIA</text>
            <text x="110" y="308" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="#3a8fff">DI LEIDA</text>
            <!-- Ground line -->
            <line x1="50" y1="325" x2="170" y2="325" stroke="rgba(122,128,153,.3)" stroke-width="1"/>
          </svg>
        </div>
        <div class="right" style="flex-direction:column;align-items:flex-start;">
          <span class="slide-eyebrow">l'esperimento</span>
          <h2 class="slide-title">Induzione, non <em>colpo diretto</em></h2>
          <p class="slide-body">
            Franklin non aspettava un fulmine diretto — sarebbe morto.
            Sfruttava l'<strong>induzione elettrostatica</strong>: la nuvola
            carica induceva cariche libere lungo la canapa bagnata.<br><br>
            Avvicinando il nocchiolo del dito alla chiave carica, generava
            una <strong>scintilla controllata</strong>. La bottiglia di Leida
            raccoglieva la carica celeste: prova definitiva che elettricità
            di laboratorio e fulmini erano la stessa cosa.
          </p>
          <div class="warn-pill">
            ⚠ Richmann tentò lo stesso esperimento a San Pietroburgo l'anno
            successivo. Fu colpito e ucciso il <strong>6 agosto 1753</strong> —
            prima vittima accertata nella storia degli esperimenti elettrici.
          </div>
        </div>
      </div>

      <!-- P3: Il parafulmine -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">la conseguenza pratica</span>
          <h2 class="slide-title">Da terrore divino a rischio <em>calcolabile</em></h2>
          <p class="slide-body">
            Franklin comprese che un'asta metallica appuntita collegata a terra
            avrebbe prodotto due effetti fisici protettivi:<br><br>
            <strong>A. Effetto Punta (prevenzione):</strong> Le cariche si concentrano
            sulle punte. Il campo elettrico intensissimo ionizza costantemente l'aria
            circostante, disperdendo silenziosamente le cariche della nuvola e
            riducendo localmente la differenza di potenziale.<br><br>
            <strong>B. Canalizzazione sicura (protezione):</strong> Se il fulmine scatta
            comunque, il conduttore metallico offre il percorso a minima resistenza.
            La corrente scorre nel cavo, si disperde nel terreno, senza toccare pietra o legno.
          </p>
        </div>
        <div class="right" style="flex-direction:column;align-items:flex-start;gap:.5rem;">
          <div class="tl-item"><span class="tl-year">1753</span><span class="tl-text">Richmann muore. Il parafulmine diventa ingegneria seria.</span></div>
          <div class="tl-item"><span class="tl-year">1800s</span><span class="tl-text">Franklin rifiuta il brevetto: "È per il bene dell'umanità."</span></div>
          <div class="tl-item"><span class="tl-year">1891</span><span class="tl-text">Tesla crea la Bobina Tesla. Sogna la trasmissione wireless di energia elettrica gratuita per tutti.</span></div>
          <div class="tl-item"><span class="tl-year">Oggi</span><span class="tl-text">Reti di rilevamento in tempo reale, razzi con filo metallico per "provocare" fulmini controllati in laboratorio.</span></div>
        </div>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S7 franklin e storia HTML (3 VC panels)"
```

---

### Task 3: Popola S8 — Mitologia & Scienza (3 pannelli VC)

- [ ] **Step 1: Sostituisci `#s-mitologia`**

Trova `<section id="s-mitologia" class="slide"><!-- Task 4 --></section>` e sostituisci con:

```html
<section id="s-mitologia" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Dei del fulmine -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">slide 08 · mitologia</span>
        <h2 class="slide-title">Prima della <em>fisica</em></h2>
        <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:1rem;max-width:700px;width:100%;margin-top:1.25rem;">
          <div class="deity-card">
            <span class="deity-icon">⚡</span>
            <span class="deity-name">ZEUS</span>
            <span class="deity-culture">GRECIA</span>
            <p>Re degli dei. Il fulmine era la sua arma assoluta, simbolo di potere e giustizia divina. I luoghi colpiti dai fulmini erano sacri — i greci li chiamavano <em>enelysion</em>.</p>
          </div>
          <div class="deity-card">
            <span class="deity-icon">🔨</span>
            <span class="deity-name">THOR</span>
            <span class="deity-culture">NORRENA</span>
            <p>Dio del tuono. Il suo martello Mjolnir creava i fulmini durante i viaggi tra i regni. Protettore degli uomini contro il caos delle forze della natura.</p>
          </div>
          <div class="deity-card">
            <span class="deity-icon">⚡</span>
            <span class="deity-name">GIOVE</span>
            <span class="deity-culture">ROMA</span>
            <p>Equivalente romano di Zeus. I fulmini erano i suoi strali divini. I pontifex romani interpretavano la direzione e il tipo di fulmine come augurio o condanna.</p>
          </div>
          <div class="deity-card">
            <span class="deity-icon">✨</span>
            <span class="deity-name">INDRA</span>
            <span class="deity-culture">INDUISMO</span>
            <p>Dio della guerra e dei temporali. Armato del vajra — il fulmine cosmico — sconfisse il demone-serpente Vritra liberando le acque del mondo.</p>
          </div>
        </div>
      </div>

      <!-- P2: Dopo Franklin: scienza moderna -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">la storia della scienza</span>
          <h2 class="slide-title">Da Franklin <em>ai satelliti</em></h2>
          <div style="margin-top:.75rem;">
            <div class="tl-item"><span class="tl-year">1753</span><span class="tl-text">Richmann muore. Il parafulmine diventa un oggetto serio di ingegneria.</span></div>
            <div class="tl-item"><span class="tl-year">1891</span><span class="tl-text">Nikola Tesla sviluppa la Bobina Tesla. Sogna la trasmissione wireless di energia gratuita.</span></div>
            <div class="tl-item"><span class="tl-year">1960s</span><span class="tl-text">I razzi con filo metallico permettono di "provocare" fulmini artificiali in laboratorio.</span></div>
            <div class="tl-item"><span class="tl-year">1989</span><span class="tl-text">Prima fotografia documentata di uno Sprite da un aereo dell'Università del Minnesota.</span></div>
            <div class="tl-item"><span class="tl-year">1995</span><span class="tl-text">NASA lancia il Lightning Imaging Sensor (LIS). Prima mappa globale completa della distribuzione dei fulmini.</span></div>
          </div>
        </div>
        <div class="right" style="flex-direction:column;align-items:flex-start;">
          <span class="slide-eyebrow">rilevamento moderno</span>
          <h2 class="slide-title" style="font-size:clamp(1.8rem,3vw,2.8rem);">La rete <em>invisibile</em></h2>
          <p class="slide-body">
            Ogni fulmine emette un impulso radio sferico. Tre o più stazioni lo ricevono
            con differenze di tempo di frazioni di microsecondo. La
            <strong>triangolazione time-of-arrival</strong> localizza la scarica
            con precisione di <strong>pochi metri</strong>.
          </p>
          <ul class="detect-list" style="margin-top:.75rem;">
            <li><span>LINET</span><span>Europa: 135+ sensori, copertura continentale</span></li>
            <li><span>SIRF</span><span>Italia (ISPRA): 1,5 milioni CG/anno, mappe stagionali</span></li>
            <li><span>ENTLN</span><span>Globale: 900+ sensori worldwide</span></li>
            <li><span>ISUAL</span><span>Satellite: rileva TGF, Sprite e Elves dallo spazio</span></li>
          </ul>
        </div>
      </div>

      <!-- P3: Curiosità finali -->
      <div class="vc-panel panel-center">
        <span class="slide-eyebrow">lo sapevi che</span>
        <h2 class="slide-title">Tre fatti che <em>sorprendono</em></h2>
        <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;max-width:900px;width:100%;margin-top:1.5rem;">
          <div class="myth-item">
            Il fulmine può fare il giro della Terra in meno di mezzo secondo alla velocità del return stroke (<strong>100.000 km/s</strong>).
          </div>
          <div class="myth-item">
            L'energia di un fulmine medio potrebbe far bollire circa <strong>4 litri d'acqua</strong>. Ma è impossibile raccoglierla: dura 0,2 secondi e la maggior parte è calore disperso.
          </div>
          <div class="myth-item">
            Il tuono non si sente oltre <strong>20–25 km</strong>. Le onde sonore vengono piegate verso l'alto dagli strati d'aria a diverse temperature (rifrazione acustica) e si disperdono prima di raggiungere il suolo.
          </div>
        </div>
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add S8 mitologia HTML (3 VC panels)"
```

---

### Task 4: Popola S9 — Nel Mondo & Protezione (3 pannelli VC)

- [ ] **Step 1: Sostituisci `#s-mondo`**

Trova `<section id="s-mondo"     class="slide"><!-- Task 4 --></section>` e sostituisci con:

```html
<section id="s-mondo" class="slide">
  <div class="vc-wrap">
    <div class="vc-dots">
      <div class="vc-dot active"></div>
      <div class="vc-dot"></div>
      <div class="vc-dot"></div>
    </div>
    <div class="vc-track">

      <!-- P1: Mappa mondiale Leaflet -->
      <div class="vc-panel panel-full map-panel" style="position:relative;">
        <div id="map-counter" class="map-counter">— FULMINI NEGLI ULTIMI 5 MIN</div>
        <div id="map" style="width:100%;height:100%;"></div>
      </div>

      <!-- P2: Italia -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">il caso italia</span>
          <h2 class="slide-title">1,5 milioni di fulmini <em>l'anno</em></h2>
          <p class="slide-body">
            L'Italia è uno dei paesi europei più esposti. La complessa
            orografia e la posizione centrale nel Mediterraneo creano
            meccanismi diversi per area geografica.
          </p>
          <table class="data-table" style="margin-top:1rem;">
            <thead><tr><th>Area</th><th>Stagione</th><th>Meccanismo</th></tr></thead>
            <tbody>
              <tr><td>Arco Alpino/Prealpi</td><td>Estate (Giu–Ago)</td><td>Sollevamento orografico</td></tr>
              <tr><td>Pianura Padana</td><td>Tarda est./Autunno</td><td>Convezione termica + fronte freddo</td></tr>
              <tr><td>Tirreno/Appennini</td><td>Autunno (Set–Nov)</td><td>Instabilità marittima</td></tr>
              <tr><td>Costa Adriatica</td><td>Autunno</td><td>Bora + umidità adriatica</td></tr>
            </tbody>
          </table>
          <p class="slide-body" style="margin-top:.75rem;font-style:italic;">
            Il paradosso della Pianura Padana: d'estate si comporta come una conca
            subtropicale. Quando un fronte freddo scavalca le Alpi, l'impatto con
            l'aria surriscaldata genera <strong>temporali supercellulari</strong>
            tra i più violenti d'Europa.
          </p>
        </div>
        <div class="right">
          <!-- Mini mappa SVG Italia con zone colorate -->
          <svg viewBox="0 0 200 280" width="180" style="max-width:100%;">
            <text x="100" y="20" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="9" fill="var(--muted)" letter-spacing="2">ITALIA · ZONE TEMPORALESCHE</text>
            <!-- Nord (Alpi/Pianura) -->
            <rect x="40" y="30" width="120" height="50" rx="4" fill="rgba(58,143,255,.15)" stroke="rgba(58,143,255,.4)" stroke-width="1"/>
            <text x="100" y="50" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="#3a8fff">ALPI / PREALPI</text>
            <text x="100" y="65" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="7" fill="var(--muted)">Estate · Orografico</text>
            <!-- Pianura Padana -->
            <rect x="45" y="85" width="110" height="35" rx="4" fill="rgba(240,192,64,.12)" stroke="rgba(240,192,64,.4)" stroke-width="1"/>
            <text x="100" y="100" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--gold)">PIANURA PADANA</text>
            <text x="100" y="113" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="7" fill="var(--muted)">Autunno · Supercellulari</text>
            <!-- Centro -->
            <rect x="55" y="125" width="90" height="60" rx="4" fill="rgba(80,200,120,.1)" stroke="rgba(80,200,120,.3)" stroke-width="1"/>
            <text x="100" y="150" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="#50c878">APPENNINI / TIRRENO</text>
            <text x="100" y="163" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="7" fill="var(--muted)">Autunno · Marittimo</text>
            <!-- Sud -->
            <rect x="60" y="190" width="80" height="50" rx="4" fill="rgba(255,64,64,.1)" stroke="rgba(255,64,64,.3)" stroke-width="1"/>
            <text x="100" y="213" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="8" fill="var(--red)">SUD / ADRIATICO</text>
            <text x="100" y="226" text-anchor="middle" font-family="Bebas Neue,sans-serif" font-size="7" fill="var(--muted)">Autunno · Bora</text>
          </svg>
        </div>
      </div>

      <!-- P3: Come proteggersi -->
      <div class="vc-panel panel-split">
        <div class="left">
          <span class="slide-eyebrow">protezione</span>
          <h2 class="slide-title">Cosa fare <em>e non fare</em></h2>
          <div id="safety-grid" style="display:grid;grid-template-columns:1fr 1fr;gap:.4rem;margin-top:.75rem;">
            <div class="safety-item danger" data-tip="Mai sotto un albero. È un conduttore naturale alto e isolato. Mantieni almeno 50 m di distanza.">
              <span class="s-icon">🌲</span>
              <span class="s-label">SOTTO UN ALBERO</span>
              <span class="s-badge">PERICOLO</span>
            </div>
            <div class="safety-item safe" data-tip="L'auto è una gabbia di Faraday: la carrozzeria conduce la corrente intorno a te. Non toccare la scocca metallica durante il temporale.">
              <span class="s-icon">🚗</span>
              <span class="s-label">IN AUTO</span>
              <span class="s-badge">SICURO</span>
            </div>
            <div class="safety-item safe" data-tip="Edificio solido = sicuro. Stacca gli apparecchi elettrici e i cavi internet. Evita la doccia (acqua + tubature = conduttori).">
              <span class="s-icon">🏠</span>
              <span class="s-label">IN EDIFICIO</span>
              <span class="s-badge">SICURO</span>
            </div>
            <div class="safety-item danger" data-tip="Esci subito dall'acqua. La corrente si espande in superficie come cerchi concentrici. Anche a decine di metri dal punto di impatto sei in pericolo.">
              <span class="s-icon">🏊</span>
              <span class="s-label">IN ACQUA</span>
              <span class="s-badge">PERICOLO</span>
            </div>
            <div class="safety-item danger" data-tip="Scendi dalla cima. Accovacciati sui talloni (non in ginocchio), metti le mani sulle orecchie, resta distante dagli altri (la corrente può saltare da persona a persona).">
              <span class="s-icon">⛰️</span>
              <span class="s-label">IN CIMA</span>
              <span class="s-badge">PERICOLO</span>
            </div>
            <div class="safety-item medium" data-tip="Posizione 'a rana' sui talloni: riduci la superficie di contatto col suolo. Non sdraiarti (tensione di passo). Non correre.">
              <span class="s-icon">🌾</span>
              <span class="s-label">CAMPO APERTO</span>
              <span class="s-badge">ATTENZIONE</span>
            </div>
          </div>
          <div class="safety-tip" id="safety-tip">Clicca uno scenario per il consiglio completo.</div>
        </div>
        <div class="right" style="flex-direction:column;align-items:flex-start;">
          <span class="slide-eyebrow">mito smontato</span>
          <h2 class="slide-title" style="font-size:clamp(1.8rem,3vw,2.8rem);">Non colpisce <em>due volte?</em></h2>
          <p class="slide-body">
            Falso. L'Empire State Building viene colpito
            <strong>23 volte all'anno in media</strong>.
            Un fulmine colpisce dove trova il percorso di minore
            resistenza — e se quel percorso è buono, lo userà di nuovo.
          </p>
          <ul class="detect-list" style="margin-top:1rem;">
            <li><span>LINET</span><span>135+ sensori, Europa</span></li>
            <li><span>SIRF</span><span>Italia · ISPRA</span></li>
            <li><span>ENTLN</span><span>900+ sensori, globale</span></li>
            <li><span>ISUAL</span><span>Satellite · TGF/Sprite</span></li>
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
git commit -m "feat: add S9 mondo e protezione HTML (3 VC panels + map)"
```
