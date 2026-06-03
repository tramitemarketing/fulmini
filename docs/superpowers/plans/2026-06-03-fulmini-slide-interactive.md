# Fulmini — Slide System + Interactive Tools Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Trasformare `fulmini.html` in una presentazione full-page con 8 slide, UI fissa identica a terremoti, e uno strumento interattivo canvas/Leaflet per ogni slide.

**Architecture:** Single HTML file, tutto inline (CSS + JS). Modifiche incrementali: prima la shell slide engine, poi UI fissa, poi ogni tool uno alla volta. Nessun build step, nessuna dipendenza esterna oltre Leaflet (CDN).

**Tech Stack:** Vanilla JS, Canvas API, CSS scroll-snap, Leaflet.js (CDN), Blitzortung WebSocket pubblico.

---

## File

- Modify: `fulmini.html` (unico file target)
- Backup già creato: `fulmini_backup_pre-slide.html`

---

### Task 1: Slide engine CSS + HTML shell

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere Leaflet CSS in `<head>`**

Dopo `<title>` e prima del `<link>` Google Fonts, aggiungere:
```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
```

- [ ] **Step 2: Aggiungere CSS slide engine all'inizio di `<style>`**

Inserire subito dopo `:root { ... }` e prima di `*, *::before`:

```css
/* ── SLIDE ENGINE ── */
html {
  scroll-snap-type: y mandatory;
  overflow-y: scroll;
  scrollbar-width: none;
  cursor: none;
  height: 100%;
}
html::-webkit-scrollbar { display: none; }
body { height: 100%; }

.slide {
  height: 100vh;
  min-height: 100vh;
  max-height: 100vh;
  scroll-snap-align: start;
  scroll-snap-stop: always;
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* ── CUSTOM CURSOR ── */
#cursor {
  position: fixed; width: 10px; height: 10px;
  background: var(--gold);
  border-radius: 50%;
  pointer-events: none; z-index: 9999;
  transform: translate(-50%,-50%);
}
#cursor-ring {
  position: fixed; width: 32px; height: 32px;
  border: 1px solid rgba(240,192,64,0.4);
  border-radius: 50%;
  pointer-events: none; z-index: 9998;
  transform: translate(-50%,-50%);
  transition: width .3s, height .3s;
}

/* ── FIXED UI ── */
#progress-bar {
  position: fixed; left: 0; top: 0;
  width: 3px; height: 0%;
  background: var(--gold); z-index: 200;
  transition: height .6s cubic-bezier(.4,0,.2,1);
}
#nav-dots {
  position: fixed; right: 1.8rem; top: 50%;
  transform: translateY(-50%);
  z-index: 200;
  display: flex; flex-direction: column; gap: .75rem;
}
.ndot {
  width: 5px; height: 5px;
  background: rgba(240,192,64,0.25);
  border-radius: 50%; cursor: pointer;
  transition: all .35s ease;
}
.ndot.active { background: var(--gold); transform: scale(1.8); }

#slide-counter {
  position: fixed; left: 1.8rem; bottom: .5rem;
  font-family: monospace; font-size: .6rem;
  letter-spacing: .2em; color: rgba(240,192,64,0.3);
  z-index: 200;
}
#fullscreen-btn {
  position: fixed; top: 1.4rem; right: 1.4rem;
  width: 34px; height: 34px;
  border: 1px solid rgba(240,192,64,0.2);
  background: transparent; cursor: pointer;
  z-index: 200; display: flex; align-items: center; justify-content: center;
  transition: border-color .3s;
}
#fullscreen-btn:hover { border-color: var(--gold); }
#fullscreen-btn svg { width: 13px; height: 13px; fill: none; stroke: rgba(240,192,64,0.5); stroke-width: 1.5; }

#scroll-hint {
  position: fixed; bottom: 1.8rem; left: 50%;
  transform: translateX(-50%);
  display: flex; flex-direction: column; align-items: center; gap: .4rem;
  z-index: 200; pointer-events: none; transition: opacity .6s;
}
#scroll-hint.hidden { opacity: 0; }
.scroll-mouse {
  width: 18px; height: 28px;
  border: 1px solid rgba(240,192,64,0.2); border-radius: 9px; position: relative;
}
.scroll-mouse::after {
  content: ''; position: absolute; top: 5px; left: 50%;
  transform: translateX(-50%); width: 2px; height: 7px;
  background: rgba(240,192,64,0.35); border-radius: 1px;
  animation: scrollBob 1.6s infinite;
}
@keyframes scrollBob { 0%,100% { top: 5px; opacity: 1; } 80% { top: 13px; opacity: 0; } }
.scroll-label {
  font-family: 'Bebas Neue', sans-serif; font-size: .5rem;
  letter-spacing: .25em; color: rgba(240,192,64,0.25); text-transform: uppercase;
}

/* ── CAROUSEL INFRASTRUCTURE (disabled) ── */
.c-track {
  display: flex; width: 100%;
  transition: transform .6s cubic-bezier(.4,0,.2,1);
  will-change: transform;
}
.c-slide { min-width: 100vw; height: 100vh; overflow: hidden; }
.c-nav {
  display: none; /* RIMUOVI per attivare */
  position: absolute; bottom: 2rem; left: 50%; transform: translateX(-50%);
  align-items: center; gap: 1rem; z-index: 10;
}
.c-dot { width: 5px; height: 5px; background: rgba(240,192,64,0.25); border-radius: 50%; cursor: pointer; }
.c-dot.active { background: var(--gold); }
.c-prev, .c-next {
  background: none; border: 1px solid rgba(240,192,64,0.3);
  color: var(--gold); font-size: 1rem; cursor: pointer; padding: .3rem .7rem;
}
```

- [ ] **Step 3: Aggiungere Fixed UI HTML subito dopo `<body>`**

```html
<!-- FIXED UI -->
<div id="cursor"></div>
<div id="cursor-ring"></div>
<div id="progress-bar"></div>
<nav id="nav-dots">
  <div class="ndot active" data-i="0"></div>
  <div class="ndot" data-i="1"></div>
  <div class="ndot" data-i="2"></div>
  <div class="ndot" data-i="3"></div>
  <div class="ndot" data-i="4"></div>
  <div class="ndot" data-i="5"></div>
  <div class="ndot" data-i="6"></div>
  <div class="ndot" data-i="7"></div>
</nav>
<div id="slide-counter">01 / 08</div>
<button id="fullscreen-btn" title="Schermo intero (F)">
  <svg viewBox="0 0 24 24"><path d="M3 3h6M3 3v6M21 3h-6M21 3v6M3 21h6M3 21v-6M21 21h-6M21 21v-6"/></svg>
</button>
<div id="scroll-hint">
  <div class="scroll-mouse"></div>
  <span class="scroll-label">Scorri</span>
</div>
```

- [ ] **Step 4: Aggiungere classe `slide` e id a ogni sezione**

Cambiare le sezioni esistenti così:

```html
<!-- Hero -->
<section class="hero slide" id="s-hero">
<!-- Cos'è -->
<section id="s-cosae" class="slide">
<!-- Come si forma (sezione dentro #cosae → diventa slide separata) -->
<section id="s-formazione" class="slide">
<!-- Tipi -->
<section id="s-tipi" class="slide">
<!-- Fisica -->
<section id="s-fisica" class="slide">
<!-- Franklin -->
<section id="s-franklin" class="slide">
<!-- Nel Mondo -->
<section id="s-mondo" class="slide">
<!-- Protezione -->
<section id="s-protezione" class="slide">
```

Nota: la sezione "Come si forma" (steps) va estratta dalla sezione `#cosae` e diventa una slide autonoma `#s-formazione`. Il contenuto dei steps rimane invariato ma viene spostato in `<section id="s-formazione" class="slide">`.

- [ ] **Step 5: Nascondere la `<nav>` sticky esistente**

La `<nav>` con i link ancorali era per lo scroll normale. Aggiungerle `style="display:none"` oppure aggiungere nel CSS:

```css
body > nav { display: none; }
```

- [ ] **Step 6: Verificare in browser**

Aprire `fulmini.html`. Deve essere possibile scrollare tra le slide con snap. I dot devono essere visibili a destra. Il cursore custom deve funzionare.

---

### Task 2: JS core — slide tracking, cursor, keyboard, UI

**Files:** Modify `fulmini.html` — sostituire l'intero `<script>` esistente

- [ ] **Step 1: Sostituire lo `<script>` in fondo al `<body>` con il seguente**

```js
/* ═══════════════════════════════════════════
   FULMINI — CORE ENGINE
═══════════════════════════════════════════ */

// ── Cursor ──
const cursor = document.getElementById('cursor');
const cursorRing = document.getElementById('cursor-ring');
let mx = 0, my = 0, rx = 0, ry = 0;
document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
(function animCursor() {
  cursor.style.left = mx + 'px'; cursor.style.top = my + 'px';
  rx += (mx - rx) * 0.12; ry += (my - ry) * 0.12;
  cursorRing.style.left = rx + 'px'; cursorRing.style.top = ry + 'px';
  requestAnimationFrame(animCursor);
})();
document.querySelectorAll('a, button, .type-card, .ndot').forEach(el => {
  el.addEventListener('mouseenter', () => { cursorRing.style.width = '50px'; cursorRing.style.height = '50px'; });
  el.addEventListener('mouseleave', () => { cursorRing.style.width = '32px'; cursorRing.style.height = '32px'; });
});

// ── Slide tracking ──
const slides     = document.querySelectorAll('.slide');
const ndots      = document.querySelectorAll('.ndot');
const progressBar = document.getElementById('progress-bar');
const counter    = document.getElementById('slide-counter');
const scrollHint = document.getElementById('scroll-hint');
let currentIdx   = 0;
let scrolledOnce = false;
const triggered  = new Set();

const slideObserver = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting && e.intersectionRatio >= 0.5) {
      const i = [...slides].indexOf(e.target);
      if (i !== currentIdx) {
        currentIdx = i;
        updateUI(i);
        if (!scrolledOnce) { scrolledOnce = true; scrollHint.classList.add('hidden'); }
        onSlideEnter(i);
      }
    }
  });
}, { threshold: 0.5 });
slides.forEach(s => slideObserver.observe(s));

function updateUI(i) {
  ndots.forEach((d, j) => d.classList.toggle('active', j === i));
  progressBar.style.height = (i / (slides.length - 1) * 100) + '%';
  counter.textContent = String(i + 1).padStart(2, '0') + ' / ' + String(slides.length).padStart(2, '0');
}

ndots.forEach(d => d.addEventListener('click', () =>
  slides[+d.dataset.i].scrollIntoView({ behavior: 'smooth' })));

// ── Fullscreen + keyboard ──
document.getElementById('fullscreen-btn').addEventListener('click', toggleFS);
function toggleFS() {
  if (!document.fullscreenElement) document.documentElement.requestFullscreen();
  else document.exitFullscreen();
}
document.addEventListener('keydown', e => {
  if (e.key === 'ArrowDown' || e.key === 'PageDown') {
    e.preventDefault();
    slides[Math.min(currentIdx + 1, slides.length - 1)].scrollIntoView({ behavior: 'smooth' });
  } else if (e.key === 'ArrowUp' || e.key === 'PageUp') {
    e.preventDefault();
    slides[Math.max(currentIdx - 1, 0)].scrollIntoView({ behavior: 'smooth' });
  } else if (e.key === 'f' || e.key === 'F') toggleFS();
  else if (e.key === 'r' || e.key === 'R') {
    sessionStorage.setItem('slideIdx', currentIdx);
    location.reload();
  } else if (e.key === 'Escape' && document.fullscreenElement) document.exitFullscreen();
});

// Ripristino posizione dopo reload
(function() {
  const saved = sessionStorage.getItem('slideIdx');
  if (saved !== null) {
    sessionStorage.removeItem('slideIdx');
    const idx = parseInt(saved, 10);
    if (idx > 0 && idx < slides.length)
      slides[idx].scrollIntoView({ behavior: 'instant' });
  }
})();

// ── Scroll reveal (preservato) ──
const reveals = document.querySelectorAll('.reveal');
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach((e, i) => {
    if (e.isIntersecting) {
      setTimeout(() => e.target.classList.add('visible'), i * 80);
      revealObserver.unobserve(e.target);
    }
  });
}, { threshold: 0.1 });
reveals.forEach(r => revealObserver.observe(r));

// ══════════════════════════════════════════
// CAROUSEL INFRASTRUCTURE
// Per attivare: chiamare initCarousel('section-id', N) dentro onSlideEnter
// + cambiare .c-nav { display: none } → display: flex nel CSS
// ══════════════════════════════════════════
function initCarousel(sectionId, total) {
  const section = document.getElementById(sectionId);
  if (!section) return;
  const track   = section.querySelector('.c-track');
  const dots    = section.querySelectorAll('.c-dot');
  const prevBtn = section.querySelector('.c-prev');
  const nextBtn = section.querySelector('.c-next');
  if (!track) return;
  let idx = 0, busy = false;

  function go(n) {
    if (n < 0 || n >= total || busy) return;
    busy = true;
    idx = n;
    track.style.transform = `translateX(calc(${idx} * -100vw))`;
    dots.forEach((d, i) => d.classList.toggle('active', i === idx));
    if (prevBtn) prevBtn.disabled = idx === 0;
    if (nextBtn) nextBtn.disabled = idx === total - 1;
    setTimeout(() => { busy = false; }, 700);
  }

  if (prevBtn) prevBtn.addEventListener('click', () => go(idx - 1));
  if (nextBtn) nextBtn.addEventListener('click', () => go(idx + 1));
  dots.forEach((d, i) => d.addEventListener('click', () => go(i)));

  section.addEventListener('wheel', e => {
    const rect = section.getBoundingClientRect();
    if (Math.abs(rect.top) > 50) return;
    if (e.deltaY > 0 && idx === total - 1) return;
    if (e.deltaY < 0 && idx === 0) return;
    e.preventDefault(); e.stopPropagation();
    e.deltaY > 0 ? go(idx + 1) : go(idx - 1);
  }, { passive: false });

  let tx = 0;
  section.addEventListener('touchstart', e => { tx = e.touches[0].clientX; }, { passive: true });
  section.addEventListener('touchend', e => {
    const dx = e.changedTouches[0].clientX - tx;
    if (dx < -50) go(idx + 1);
    else if (dx > 50) go(idx - 1);
  });

  document.addEventListener('keydown', e => {
    const rect = section.getBoundingClientRect();
    if (Math.abs(rect.top) > 50) return;
    if (e.key === 'ArrowRight') { e.preventDefault(); go(idx + 1); }
    else if (e.key === 'ArrowLeft') { e.preventDefault(); go(idx - 1); }
  });
}

// ══════════════════════════════════════════
// onSlideEnter — dispatcher tool per slide
// ══════════════════════════════════════════
function onSlideEnter(i) {
  if (triggered.has(i)) return;
  triggered.add(i);
  if (i === 0) initHeroCanvas();
  if (i === 1) initCloudCharge();
  if (i === 2) initSteppedLeader();
  if (i === 3) initTypeVisualizer();
  if (i === 4) { initPhysicsSliders(); initLichtenberg(); }
  if (i === 5) initEMPulse();
  if (i === 6) initBlitzortung();
  if (i === 7) { initSafetySimulator(); initThunderCalc(); }
}

// ══════════════════════════════════════════
// TOOL STUBS — sostituiti nei task successivi
// ══════════════════════════════════════════
function initHeroCanvas() {}
function initCloudCharge() {}
function initSteppedLeader() {}
function initTypeVisualizer() {}
function initPhysicsSliders() {}
function initLichtenberg() {}
function initEMPulse() {}
function initBlitzortung() {}
function initSafetySimulator() {}
function initThunderCalc() {}
```

- [ ] **Step 2: Verificare in browser**

Scrollare tra le slide. La barra sinistra deve avanzare. I dot devono aggiornare. Il counter deve mostrare `01/08`, `02/08` ecc. Frecce ↑↓ devono funzionare. `F` deve aprire fullscreen.

---

### Task 3: S0 — Hero canvas procedurale (fulmini frattali in loop)

**Files:** Modify `fulmini.html` — aggiungere canvas nell'hero + sostituire `initHeroCanvas()`

- [ ] **Step 1: Aggiungere canvas nell'hero section**

All'interno di `<section class="hero slide" id="s-hero">`, come primo figlio:
```html
<canvas id="hero-canvas" style="position:absolute;inset:0;width:100%;height:100%;pointer-events:none;z-index:1;"></canvas>
```

Aggiungere al CSS (il `.hero-bg` e tutto il contenuto dell'hero deve avere `z-index` >= 2 per stare sopra il canvas).

- [ ] **Step 2: Sostituire `function initHeroCanvas() {}`**

```js
function initHeroCanvas() {
  const canvas = document.getElementById('hero-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  let W, H;
  function resize() { W = canvas.width = canvas.offsetWidth; H = canvas.height = canvas.offsetHeight; }
  resize();
  window.addEventListener('resize', resize);

  function branch(ctx, x, y, angle, len, depth) {
    if (depth === 0 || len < 3) return;
    const ex = x + Math.cos(angle) * len;
    const ey = y + Math.sin(angle) * len;
    ctx.beginPath();
    ctx.moveTo(x, y);
    ctx.lineTo(ex, ey);
    ctx.globalAlpha = 0.5 + depth * 0.08;
    ctx.strokeStyle = depth > 4 ? '#fff' : '#f0c040';
    ctx.lineWidth = depth * 0.6;
    ctx.stroke();
    if (Math.random() < 0.35) branch(ctx, ex, ey, angle + (Math.random() - 0.5) * 0.9, len * 0.55, depth - 1);
    branch(ctx, ex, ey, angle + (Math.random() - 0.3) * 0.4, len * 0.7, depth - 1);
  }

  function spawnBolt() {
    const x = W * (0.1 + Math.random() * 0.8);
    ctx.save();
    ctx.shadowBlur = 18;
    ctx.shadowColor = '#3a8fff';
    branch(ctx, x, 0, Math.PI / 2, H * 0.28, 6);
    ctx.restore();
    setTimeout(() => {
      ctx.clearRect(0, 0, W, H);
    }, 180 + Math.random() * 120);
  }

  setInterval(() => { spawnBolt(); }, 2200 + Math.random() * 2000);
  spawnBolt();
}
```

- [ ] **Step 3: Verificare**

L'hero deve mostrare fulmini procedurali gold/bianchi che appaiono e svaniscono sopra il gradiente. I bolt SVG CSS animati esistenti restano (si sovrappongono, effetto layered).

---

### Task 4: S1 — Cloud charge buildup canvas

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere canvas HTML nella slide `#s-cosae`**

Dentro `<section id="s-cosae" class="slide">`, aggiungere prima del `.container`:
```html
<canvas id="cloud-canvas" style="position:absolute;inset:0;width:100%;height:100%;pointer-events:auto;z-index:1;opacity:0.7;"></canvas>
```

- [ ] **Step 2: Sostituire `function initCloudCharge() {}`**

```js
function initCloudCharge() {
  const canvas = document.getElementById('cloud-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  let W, H;
  function resize() { W = canvas.width = canvas.offsetWidth; H = canvas.height = canvas.offsetHeight; }
  resize();

  const PARTICLES = 60;
  let particles = [];
  let tension = 0;
  let discharged = false;

  function resetParticles() {
    particles = Array.from({ length: PARTICLES }, () => ({
      x: W * 0.15 + Math.random() * W * 0.7,
      y: H * 0.05 + Math.random() * H * 0.45,
      vx: (Math.random() - 0.5) * 0.4,
      vy: (Math.random() - 0.5) * 0.4,
      charge: Math.random() < 0.5 ? 1 : -1,
      r: 3 + Math.random() * 2,
    }));
    tension = 0;
    discharged = false;
  }
  resetParticles();

  canvas.addEventListener('click', () => {
    if (!discharged) { tension = 1; }
    else resetParticles();
  });

  let boltY = 0, boltActive = false;

  function discharge() {
    discharged = true;
    boltActive = true;
    boltY = H * 0.5;
    setTimeout(() => { boltActive = false; resetParticles(); }, 600);
  }

  function drawCloud(x, y, w, h) {
    ctx.beginPath();
    ctx.ellipse(x, y, w, h, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.7)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(240,192,64,0.15)';
    ctx.stroke();
  }

  function loop() {
    ctx.clearRect(0, 0, W, H);
    drawCloud(W * 0.5, H * 0.22, W * 0.38, H * 0.15);

    particles.forEach(p => {
      p.x += p.vx; p.y += p.vy;
      if (p.charge === 1) { p.vy -= 0.008; } else { p.vy += 0.008; }
      p.x = Math.max(W * 0.1, Math.min(W * 0.9, p.x));
      p.y = Math.max(H * 0.03, Math.min(H * 0.55, p.y));
      ctx.beginPath();
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
      ctx.fillStyle = p.charge === 1 ? '#f0c040' : '#3a8fff';
      ctx.globalAlpha = 0.8;
      ctx.fill();
      ctx.globalAlpha = 1;
    });

    tension = Math.min(1, tension + 0.0015);
    const pct = tension;
    ctx.fillStyle = `rgba(240,192,64,${0.2 + pct * 0.5})`;
    ctx.font = `${10 + pct * 8}px 'Bebas Neue'`;
    ctx.fillText(`TENSIONE ${Math.round(pct * 100)}%`, W * 0.05, H * 0.92);

    if (tension >= 1 && !discharged) discharge();

    if (boltActive) {
      ctx.beginPath();
      ctx.moveTo(W * 0.5, H * 0.37);
      let cx = W * 0.5;
      for (let y = H * 0.37; y < H * 0.95; y += 18) {
        cx += (Math.random() - 0.5) * 40;
        ctx.lineTo(cx, y);
      }
      ctx.strokeStyle = '#fff';
      ctx.lineWidth = 2.5;
      ctx.shadowBlur = 20;
      ctx.shadowColor = '#3a8fff';
      ctx.stroke();
      ctx.shadowBlur = 0;
    }

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 3: Verificare**

Slide S1: particelle + (gold) e − (blu) che si separano lentamente dentro il cumulonembo. Barra tensione che sale. Quando arriva al 100% parte il fulmine. Click per triggherare o resettare.

---

### Task 5: S2 — Stepped leader canvas (4 fasi animate)

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere HTML canvas + controlli nella slide `#s-formazione`**

Dentro `<section id="s-formazione" class="slide">`, sostituire il contenuto (i `.steps`) con:

```html
<div class="container" style="display:grid;grid-template-columns:1fr 1fr;height:100%;align-items:center;gap:2rem;">
  <div>
    <div class="section-label">02 — Formazione</div>
    <h2>Come nasce<br>il fulmine</h2>
    <ul id="leader-phases" style="list-style:none;padding:0;margin-top:1.5rem;">
      <li class="lphase active" data-phase="0" style="padding:.6rem 0;border-bottom:1px solid var(--border);font-size:.95rem;color:#c0c4d4;cursor:pointer;">01 — Stepped Leader: discende a gradini</li>
      <li class="lphase" data-phase="1" style="padding:.6rem 0;border-bottom:1px solid var(--border);font-size:.95rem;color:#c0c4d4;cursor:pointer;">02 — Streamer: sale dal suolo</li>
      <li class="lphase" data-phase="2" style="padding:.6rem 0;border-bottom:1px solid var(--border);font-size:.95rem;color:#c0c4d4;cursor:pointer;">03 — Connessione</li>
      <li class="lphase" data-phase="3" style="padding:.6rem 0;border-bottom:1px solid var(--border);font-size:.95rem;color:#c0c4d4;cursor:pointer;">04 — Return Stroke</li>
    </ul>
    <button id="leader-btn" style="margin-top:1.5rem;background:none;border:1px solid var(--gold);color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;padding:.5rem 1.2rem;cursor:pointer;font-size:1rem;">▶ Avvia</button>
  </div>
  <div>
    <canvas id="leader-canvas" style="width:100%;height:60vh;display:block;"></canvas>
  </div>
</div>
```

- [ ] **Step 2: Sostituire `function initSteppedLeader() {}`**

```js
function initSteppedLeader() {
  const canvas = document.getElementById('leader-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth;
  canvas.height = canvas.offsetHeight;
  const W = canvas.width, H = canvas.height;
  const phaseEls = document.querySelectorAll('.lphase');
  const btn = document.getElementById('leader-btn');
  let phase = 0, animId = null, running = false;

  // State per ogni fase
  let leaderY = 0;
  let streamerY = H;
  let flashAlpha = 0;
  let returnY = H;

  function setPhase(p) {
    phase = p;
    phaseEls.forEach((el, i) => el.classList.toggle('active', i === p));
    // evidenzia fase attiva
    phaseEls.forEach((el, i) => el.style.color = i === p ? 'var(--gold)' : '#c0c4d4');
  }

  phaseEls.forEach((el, i) => el.addEventListener('click', () => { stop(); setPhase(i); draw(); }));

  function draw() {
    ctx.clearRect(0, 0, W, H);
    // nuvola
    ctx.fillStyle = 'rgba(10,14,26,0.95)';
    ctx.fillRect(0, 0, W, H);
    ctx.fillStyle = '#1a2a4a';
    ctx.beginPath(); ctx.ellipse(W/2, 50, W*0.42, 38, 0, 0, Math.PI*2); ctx.fill();
    // terreno
    ctx.fillStyle = '#0a0e1a';
    ctx.fillRect(0, H-30, W, 30);
    ctx.strokeStyle = 'rgba(240,192,64,0.2)';
    ctx.strokeRect(0, H-30, W, 30);

    if (phase === 0) {
      // stepped leader
      ctx.strokeStyle = 'rgba(180,180,255,0.6)';
      ctx.lineWidth = 1.5;
      ctx.setLineDash([8, 6]);
      ctx.beginPath();
      let lx = W/2;
      for (let y = 88; y <= leaderY; y += 14) {
        ctx.lineTo(lx + (Math.sin(y*0.3)*12), y);
        if (Math.random() < 0.3) { // branch
          ctx.moveTo(lx + (Math.sin(y*0.3)*12), y);
          ctx.lineTo(lx + (Math.sin(y*0.3)*12) + 20, y + 20);
          ctx.moveTo(lx + (Math.sin(y*0.3)*12), y);
        }
      }
      ctx.stroke();
      ctx.setLineDash([]);
    }
    if (phase === 1) {
      // leader completo
      ctx.strokeStyle = 'rgba(180,180,255,0.5)';
      ctx.lineWidth = 1.5;
      ctx.setLineDash([8, 6]);
      ctx.beginPath();
      let lx = W/2;
      for (let y = 88; y <= H - 30; y += 14)
        ctx.lineTo(lx + Math.sin(y*0.3)*12, y);
      ctx.stroke();
      ctx.setLineDash([]);
      // streamer
      ctx.strokeStyle = 'rgba(240,192,64,0.8)';
      ctx.lineWidth = 2;
      ctx.beginPath();
      let sx = W/2 + 15;
      for (let y = H-30; y >= streamerY; y -= 10)
        ctx.lineTo(sx + Math.sin(y*0.4)*8, y);
      ctx.stroke();
    }
    if (phase === 2) {
      // flash connessione
      ctx.fillStyle = `rgba(255,255,255,${flashAlpha})`;
      ctx.fillRect(0, 0, W, H);
    }
    if (phase === 3) {
      // return stroke
      ctx.shadowBlur = 30; ctx.shadowColor = '#3a8fff';
      ctx.strokeStyle = '#ffffff';
      ctx.lineWidth = 3;
      ctx.beginPath();
      let rx = W/2;
      for (let y = returnY; y >= 88; y -= 12)
        ctx.lineTo(rx + Math.sin(y*0.3)*10, y);
      ctx.stroke();
      ctx.shadowBlur = 0;
    }
  }

  function stop() { if (animId) cancelAnimationFrame(animId); running = false; btn.textContent = '▶ Avvia'; }

  function runAnim() {
    running = true;
    btn.textContent = '⏸ Pausa';
    leaderY = 88; streamerY = H - 30; flashAlpha = 0; returnY = H - 30;
    setPhase(0);

    function step() {
      if (!running) return;
      if (phase === 0) {
        leaderY += 3;
        if (leaderY >= H - 30) { setPhase(1); streamerY = H - 30; }
      } else if (phase === 1) {
        streamerY -= 3;
        if (streamerY <= H * 0.55) { setPhase(2); flashAlpha = 0; }
      } else if (phase === 2) {
        flashAlpha += 0.08;
        if (flashAlpha >= 1) { setTimeout(() => { setPhase(3); returnY = H - 30; }, 100); return; }
      } else if (phase === 3) {
        returnY -= 6;
        if (returnY <= 88) { stop(); return; }
      }
      draw();
      animId = requestAnimationFrame(step);
    }
    step();
  }

  btn.addEventListener('click', () => running ? stop() : runAnim());
  draw();
}
```

- [ ] **Step 3: Aggiungere CSS fase attiva**

Nel `<style>`:
```css
.lphase.active { color: var(--gold) !important; }
```

- [ ] **Step 4: Verificare**

Slide S2: pannello sinistro con 4 fasi cliccabili, canvas destra. Avvia mostra animazione sequenziale 4 fasi. Click su fase mostra quella specifica.

---

### Task 6: S3 — Lightning type visualizer

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere canvas nella slide `#s-tipi`**

Dentro `<section id="s-tipi" class="slide">`, dopo `<h2>Tipi di Fulmini</h2>` e prima di `.types-grid`:
```html
<canvas id="type-canvas" style="width:100%;height:200px;display:block;margin-bottom:1.5rem;"></canvas>
```

- [ ] **Step 2: Aggiungere `data-type` alle card esistenti**

Aggiungere attributo a ogni `.type-card`:
```html
<div class="type-card" data-type="cg">...</div>   <!-- Nube-Suolo -->
<div class="type-card" data-type="ic">...</div>   <!-- Intra-nube -->
<div class="type-card" data-type="cc">...</div>   <!-- Nube-Nube -->
<div class="type-card" data-type="ball">...</div> <!-- Palla -->
<div class="type-card" data-type="sprite">...</div>
<div class="type-card" data-type="jet">...</div>
```

- [ ] **Step 3: Sostituire `function initTypeVisualizer() {}`**

```js
function initTypeVisualizer() {
  const canvas = document.getElementById('type-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth;
  canvas.height = canvas.offsetHeight;
  const W = canvas.width, H = canvas.height;
  let animId = null;

  const drawers = {
    cg(t) {
      ctx.clearRect(0,0,W,H);
      const x = W/2, y0 = 10, y1 = H-10;
      const prog = (Math.sin(t*2)+1)/2;
      ctx.strokeStyle = '#f0c040'; ctx.lineWidth = 2; ctx.shadowBlur = 15; ctx.shadowColor = '#3a8fff';
      ctx.beginPath();
      for (let y = y0; y <= y0 + (y1-y0)*prog; y += 8)
        ctx.lineTo(x + Math.sin(y*0.3)*15, y);
      ctx.stroke(); ctx.shadowBlur = 0;
    },
    ic(t) {
      ctx.clearRect(0,0,W,H);
      const prog = (Math.sin(t*1.5)+1)/2;
      ctx.strokeStyle = '#3a8fff'; ctx.lineWidth = 2; ctx.shadowBlur = 12; ctx.shadowColor = '#3a8fff';
      ctx.beginPath();
      for (let x = W*0.1; x <= W*0.1 + W*0.8*prog; x += 8)
        ctx.lineTo(x, H/2 + Math.sin(x*0.08)*20);
      ctx.stroke(); ctx.shadowBlur = 0;
    },
    cc(t) {
      ctx.clearRect(0,0,W,H);
      const prog = (Math.sin(t*1.5)+1)/2;
      ctx.strokeStyle = '#f0c040'; ctx.lineWidth = 2;
      ctx.beginPath();
      for (let x = W*0.05; x <= W*0.05 + W*0.9*prog; x += 8)
        ctx.lineTo(x, H*0.35 + Math.sin(x*0.05)*H*0.2);
      ctx.stroke();
      // due nuvole
      ctx.fillStyle = 'rgba(26,42,74,0.6)';
      ctx.beginPath(); ctx.ellipse(W*0.12, H*0.35, 40, 22, 0, 0, Math.PI*2); ctx.fill();
      ctx.beginPath(); ctx.ellipse(W*0.88, H*0.35, 40, 22, 0, 0, Math.PI*2); ctx.fill();
    },
    ball(t) {
      ctx.clearRect(0,0,W,H);
      const bx = W/2 + Math.sin(t)*60;
      const by = H/2 + Math.sin(t*0.7)*20;
      const r = 18 + Math.sin(t*3)*4;
      const grd = ctx.createRadialGradient(bx, by, 0, bx, by, r);
      grd.addColorStop(0, '#fff');
      grd.addColorStop(0.4, '#f0c040');
      grd.addColorStop(1, 'rgba(58,143,255,0)');
      ctx.beginPath(); ctx.arc(bx, by, r, 0, Math.PI*2);
      ctx.fillStyle = grd; ctx.fill();
    },
    sprite(t) {
      ctx.clearRect(0,0,W,H);
      const alpha = (Math.sin(t*2)+1)/2;
      ctx.globalAlpha = 0.3 + alpha*0.7;
      ctx.fillStyle = '#c040f0';
      // forma medusa
      for (let i = 0; i < 6; i++) {
        const x = W/2 + (i-2.5)*20;
        ctx.beginPath();
        ctx.moveTo(x, H*0.5);
        ctx.lineTo(x - 8, H*0.15);
        ctx.lineTo(x + 8, H*0.15);
        ctx.closePath();
        ctx.fill();
        ctx.beginPath();
        ctx.moveTo(x, H*0.5);
        ctx.lineTo(x + (Math.random()-0.5)*10, H*0.85);
        ctx.strokeStyle = 'rgba(192,64,240,0.4)'; ctx.lineWidth = 1;
        ctx.stroke();
      }
      ctx.globalAlpha = 1;
    },
    jet(t) {
      ctx.clearRect(0,0,W,H);
      const prog = (Math.sin(t*1.8)+1)/2;
      ctx.fillStyle = '#1a2a4a';
      ctx.beginPath(); ctx.ellipse(W/2, H*0.85, W*0.3, 20, 0, 0, Math.PI*2); ctx.fill();
      ctx.strokeStyle = '#4080ff'; ctx.lineWidth = 3; ctx.shadowBlur = 20; ctx.shadowColor = '#4080ff';
      ctx.beginPath();
      ctx.moveTo(W/2, H*0.85);
      ctx.lineTo(W/2 - 15, H*0.85 - (H*0.75)*prog);
      ctx.lineTo(W/2 + 15, H*0.85 - (H*0.75)*prog);
      ctx.stroke(); ctx.shadowBlur = 0;
    }
  };

  let activeType = 'cg';
  let t = 0;

  document.querySelectorAll('.type-card[data-type]').forEach(card => {
    card.style.cursor = 'pointer';
    card.addEventListener('click', () => {
      document.querySelectorAll('.type-card').forEach(c => c.style.borderColor = '');
      card.style.borderColor = 'var(--gold)';
      activeType = card.dataset.type;
    });
  });

  function loop() {
    t += 0.04;
    if (drawers[activeType]) drawers[activeType](t);
    animId = requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 4: Verificare**

Slide S3: canvas sopra le card. Click su ogni tipo → animazione specifica nel canvas.

---

### Task 7: S4 — Physics sliders + Lichtenberg

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere HTML nella slide `#s-fisica`**

Dentro `<section id="s-fisica" class="slide">`, prima del `.container` esistente aggiungere:
```html
<div id="fisica-tools" style="display:grid;grid-template-columns:1fr 1fr;gap:2rem;padding:2rem 4rem;align-items:start;height:100%;overflow-y:auto;">
  <div id="sliders-wrap">
    <p style="font-family:'Bebas Neue',sans-serif;letter-spacing:.15em;color:var(--gold);margin-bottom:1rem;">PARAMETRI FISICI</p>
    <label style="font-size:.85rem;color:var(--muted);">Tensione (V)</label>
    <input id="volt-slider" type="range" min="100" max="1000" value="300" style="width:100%;margin:.5rem 0;">
    <p id="volt-val" style="font-family:'Bebas Neue',sans-serif;font-size:2rem;color:var(--gold);">300 MV</p>
    <div style="margin-top:1rem;">
      <p style="font-size:.8rem;color:var(--muted);">Corrente: <span id="curr-val" style="color:var(--white);">—</span></p>
      <p style="font-size:.8rem;color:var(--muted);">Temperatura: <span id="temp-val" style="color:var(--white);">—</span></p>
      <p style="font-size:.8rem;color:var(--muted);">Energia: <span id="energy-val" style="color:var(--white);">—</span></p>
    </div>
    <div id="temp-bar-wrap" style="margin-top:1rem;height:12px;background:rgba(255,255,255,0.05);border-radius:2px;overflow:hidden;">
      <div id="temp-bar" style="height:100%;width:30%;background:linear-gradient(to right,#3a8fff,#f0c040,#fff);transition:width .3s;"></div>
    </div>
    <p style="font-size:.7rem;color:var(--muted);margin-top:.4rem;">Temperatura canale (0 = 1000K · 100% = 30.000K)</p>
  </div>
  <div>
    <p style="font-family:'Bebas Neue',sans-serif;letter-spacing:.15em;color:var(--gold);margin-bottom:.5rem;">FIGURA DI LICHTENBERG</p>
    <p style="font-size:.8rem;color:var(--muted);margin-bottom:.5rem;">Click sul canvas per generare un fulmine frattale unico</p>
    <canvas id="lichtenberg-canvas" style="width:100%;height:300px;display:block;cursor:crosshair;background:#05070f;border:1px solid rgba(240,192,64,0.1);"></canvas>
    <div style="margin-top:.5rem;display:flex;gap:.5rem;">
      <button id="lich-clear" style="background:none;border:1px solid rgba(240,192,64,0.3);color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;padding:.3rem .8rem;cursor:pointer;font-size:.85rem;">RESET</button>
    </div>
  </div>
</div>
```

- [ ] **Step 2: Nascondere `.container` esistente nella slide fisica (o spostarlo dentro `#fisica-tools`)**

Aggiungere `style="display:none"` al `.container` della slide `#s-fisica`. I tool wrap hanno tutto il necessario.

- [ ] **Step 3: Sostituire `function initPhysicsSliders() {}` e `function initLichtenberg() {}`**

```js
function initPhysicsSliders() {
  const slider = document.getElementById('volt-slider');
  if (!slider) return;
  function update() {
    const v = +slider.value;
    document.getElementById('volt-val').textContent = v + ' MV';
    const curr = Math.round(v * 0.1); // kA proporzionale
    const temp = Math.round(1000 + v * 29);
    const energy = Math.round(v * v * 0.003);
    document.getElementById('curr-val').textContent = curr + ' kA';
    document.getElementById('temp-val').textContent = temp.toLocaleString('it') + ' K';
    document.getElementById('energy-val').textContent = energy.toLocaleString('it') + ' MJ';
    document.getElementById('temp-bar').style.width = (v / 1000 * 100) + '%';
  }
  slider.addEventListener('input', update);
  update();
}

function initLichtenberg() {
  const canvas = document.getElementById('lichtenberg-canvas');
  if (!canvas) return;
  canvas.width = canvas.offsetWidth;
  canvas.height = canvas.offsetHeight;
  const ctx = canvas.getContext('2d');
  const W = canvas.width, H = canvas.height;

  function drawBolt(x, y, angle, len, depth) {
    if (depth === 0 || len < 2) return;
    const ex = x + Math.cos(angle) * len;
    const ey = y + Math.sin(angle) * len;
    ctx.beginPath();
    ctx.moveTo(x, y);
    ctx.lineTo(ex, ey);
    ctx.globalAlpha = 0.15 + depth * 0.12;
    ctx.strokeStyle = depth > 3 ? '#f0c040' : '#3a8fff';
    ctx.lineWidth = depth * 0.5;
    ctx.shadowBlur = 8;
    ctx.shadowColor = '#3a8fff';
    ctx.stroke();
    ctx.shadowBlur = 0;
    const spread = 0.5 + Math.random() * 0.5;
    if (Math.random() < 0.5)
      drawBolt(ex, ey, angle + spread, len * (0.6 + Math.random() * 0.2), depth - 1);
    drawBolt(ex, ey, angle - spread * 0.4 + (Math.random()-0.5)*0.6, len * (0.65 + Math.random() * 0.2), depth - 1);
  }

  canvas.addEventListener('click', e => {
    const rect = canvas.getBoundingClientRect();
    const cx = (e.clientX - rect.left) * (W / rect.width);
    const cy = (e.clientY - rect.top) * (H / rect.height);
    ctx.globalAlpha = 1;
    for (let i = 0; i < 8; i++) {
      drawBolt(cx, cy, (Math.PI * 2 / 8) * i, 60 + Math.random() * 40, 6);
    }
    ctx.globalAlpha = 1;
  });

  document.getElementById('lich-clear').addEventListener('click', () => ctx.clearRect(0,0,W,H));
}
```

- [ ] **Step 4: Verificare**

Slide S4: slider tensione → valori corrente/temperatura/energia si aggiornano. Click sul canvas Lichtenberg → frattale unico. Reset pulisce.

---

### Task 8: S5 — EM Pulse visualizer

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere canvas sotto il SVG Franklin nella slide `#s-franklin`**

Dentro `<section id="s-franklin" class="slide">`, dopo `.franklin-wrap`, aggiungere:
```html
<div style="text-align:center;margin-top:1rem;">
  <canvas id="em-canvas" style="width:320px;height:120px;display:inline-block;background:#05070f;border:1px solid rgba(240,192,64,0.1);"></canvas>
  <br>
  <button id="em-btn" style="margin-top:.5rem;background:none;border:1px solid var(--gold);color:var(--gold);font-family:'Bebas Neue',sans-serif;letter-spacing:.1em;padding:.4rem 1rem;cursor:pointer;">▶ Impulso EM</button>
  <p style="font-size:.7rem;color:var(--muted);margin-top:.3rem;">Questo impulso radio viene captato dai sensori Blitzortung a centinaia di km</p>
</div>
```

- [ ] **Step 2: Sostituire `function initEMPulse() {}`**

```js
function initEMPulse() {
  const canvas = document.getElementById('em-canvas');
  if (!canvas) return;
  canvas.width = canvas.offsetWidth;
  canvas.height = canvas.offsetHeight;
  const ctx = canvas.getContext('2d');
  const W = canvas.width, H = canvas.height;
  const btn = document.getElementById('em-btn');
  let waves = [];

  function draw() {
    ctx.clearRect(0,0,W,H);
    // fulmine al centro
    ctx.strokeStyle = '#f0c040'; ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(W/2, 5); ctx.lineTo(W/2 - 6, H/2); ctx.lineTo(W/2 + 3, H/2); ctx.lineTo(W/2 - 4, H-5);
    ctx.stroke();
    // onde EM
    waves = waves.filter(w => w.r < W);
    waves.forEach(w => {
      ctx.beginPath();
      ctx.arc(W/2, H/2, w.r, 0, Math.PI*2);
      ctx.strokeStyle = `rgba(58,143,255,${Math.max(0, 0.7 - w.r/W)})`;
      ctx.lineWidth = 1.5;
      ctx.stroke();
      w.r += 2.5;
    });
    if (waves.length > 0) requestAnimationFrame(draw);
  }

  btn.addEventListener('click', () => {
    waves = [{ r: 5 }, { r: 20 }, { r: 40 }];
    draw();
  });

  draw();
}
```

- [ ] **Step 3: Verificare**

Slide S5: il SVG Franklin rimane. Sotto, canvas con fulmine stilizzato + pulsante. Click → cerchi EM che si espandono.

---

### Task 9: S6 — Blitzortung live map

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Aggiungere Leaflet JS in fondo al `<head>`**

```html
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

- [ ] **Step 2: Ristrutturare slide `#s-mondo`**

Sostituire il contenuto di `<section id="s-mondo" class="slide">` con:
```html
<div style="width:100%;height:100%;display:grid;grid-template-columns:1fr 1.6fr;">
  <div class="container" style="padding:3rem 2rem;overflow-y:auto;border-right:1px solid var(--border);">
    <div class="section-label">06 — Geografia</div>
    <h2>Fulmini<br>nel Mondo</h2>
    <div id="strike-counter" style="font-family:'Bebas Neue',sans-serif;font-size:2.5rem;color:var(--gold);margin:1rem 0;">—</div>
    <p style="font-size:.8rem;color:var(--muted);">fulmini rilevati in tempo reale</p>
    <div id="ws-status" style="font-size:.75rem;color:var(--muted);margin-top:.5rem;">Connessione in corso…</div>
    <h3 style="margin-top:2rem;">Hotspot mondiali</h3>
    <table class="data-table">
      <thead><tr><th>Luogo</th><th>Curiosità</th></tr></thead>
      <tbody>
        <tr><td>Lago Maracaibo</td><td>280 notti temporalesche/anno</td></tr>
        <tr><td>Congo</td><td>Massima densità terrestre</td></tr>
        <tr><td>Florida</td><td>Lightning Capital USA</td></tr>
        <tr><td>Alpi / Pianura Padana</td><td>Zone più colpite in Italia</td></tr>
      </tbody>
    </table>
    <h3 style="margin-top:2rem;">Mitologia</h3>
    <div class="types-grid" style="grid-template-columns:1fr 1fr;">
      <div class="type-card"><div class="type-name">Zeus</div><p class="type-desc">Re degli dèi greci, scagliava fulmini forgiati da Efesto.</p></div>
      <div class="type-card"><div class="type-name">Thor</div><p class="type-desc">Dio norreno del tuono, il martello Mjolnir creava i fulmini.</p></div>
      <div class="type-card"><div class="type-name">Giove</div><p class="type-desc">Equivalente romano di Zeus. I luoghi colpiti erano sacri.</p></div>
      <div class="type-card"><div class="type-name">Indra</div><p class="type-desc">Dio induista armato di vajra, il fulmine.</p></div>
    </div>
  </div>
  <div id="blitz-map" style="height:100%;"></div>
</div>
```

- [ ] **Step 3: Sostituire `function initBlitzortung() {}`**

```js
function initBlitzortung() {
  const mapEl = document.getElementById('blitz-map');
  if (!mapEl) return;

  const map = L.map('blitz-map', { zoomControl: false, attributionControl: false }).setView([20, 10], 2);
  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
    maxZoom: 10
  }).addTo(map);

  let strikeCount = 0;
  const counterEl = document.getElementById('strike-counter');
  const statusEl  = document.getElementById('ws-status');

  // hotspot markers
  const hotspots = [
    { lat: 9.75, lon: -71.6, label: 'Lago Maracaibo' },
    { lat: 0.5,  lon: 24,    label: 'Congo' },
    { lat: 27.8, lon: -81.5, label: 'Florida' },
    { lat: 45.5, lon: 11.0,  label: 'Pianura Padana' },
  ];
  hotspots.forEach(h => {
    L.circleMarker([h.lat, h.lon], {
      radius: 8, color: '#f0c040', fillColor: '#f0c040',
      fillOpacity: 0.3, weight: 1.5
    }).bindTooltip(h.label, { className: 'blitz-tooltip' }).addTo(map);
  });

  function addStrike(lat, lon) {
    strikeCount++;
    counterEl.textContent = strikeCount.toLocaleString('it');
    const circle = L.circleMarker([lat, lon], {
      radius: 6, color: '#f0c040', fillColor: '#fff',
      fillOpacity: 1, weight: 1.5
    }).addTo(map);
    let r = 6;
    const expand = setInterval(() => {
      r += 2;
      circle.setRadius(r);
      circle.setStyle({ fillOpacity: Math.max(0, 1 - r / 30), opacity: Math.max(0, 1 - r / 30) });
      if (r > 30) { clearInterval(expand); map.removeLayer(circle); }
    }, 40);
  }

  // Blitzortung WebSocket
  let ws;
  function connect() {
    try {
      ws = new WebSocket('wss://ws1.blitzortung.org:8082/');
      ws.onopen = () => {
        statusEl.textContent = '● Live — Blitzortung.org';
        statusEl.style.color = '#4caf50';
        ws.send(JSON.stringify({ west: -180, east: 180, north: 85, south: -85 }));
      };
      ws.onmessage = e => {
        try {
          const d = JSON.parse(e.data);
          if (d.lat && d.lon) addStrike(d.lat, d.lon);
        } catch {}
      };
      ws.onerror = () => { statusEl.textContent = '○ Offline — dati simulati'; statusEl.style.color = 'var(--muted)'; startFallback(); };
      ws.onclose = () => { setTimeout(connect, 5000); };
    } catch { startFallback(); }
  }

  function startFallback() {
    // Simula strikes nelle zone ad alta attività
    const zones = [
      [5, 20], [0, 24], [5, 25], [9, -71], [10, -72], [28, -81], [45, 11], [46, 12]
    ];
    setInterval(() => {
      const z = zones[Math.floor(Math.random() * zones.length)];
      addStrike(z[0] + (Math.random()-0.5)*5, z[1] + (Math.random()-0.5)*5);
    }, 800);
  }

  connect();
}
```

- [ ] **Step 4: Aggiungere stile tooltip**

Nel `<style>`:
```css
.blitz-tooltip { background: #05070f; border: 1px solid rgba(240,192,64,0.3); color: #f0c040; font-family: 'Bebas Neue',sans-serif; letter-spacing: .1em; }
```

- [ ] **Step 5: Verificare**

Slide S6: mappa dark a destra, pannello sinistro con counter + hotspot + mitologia. I cerchi gold appaiono e si espandono sulla mappa. Se WS offline → fallback simulato.

---

### Task 10: S7 — Safety simulator + Thunder calculator

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Ristrutturare slide `#s-protezione`**

Sostituire il contenuto di `<section id="s-protezione" class="slide">` con:
```html
<div class="container" style="height:100%;overflow-y:auto;padding:3rem 2rem;">
  <div class="section-label">07 — Sicurezza</div>
  <h2>Come Proteggersi</h2>

  <p style="font-size:.9rem;color:var(--muted);margin-bottom:1.5rem;">Clicca su uno scenario per valutare il rischio.</p>
  <div id="scenarios" style="display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;margin-bottom:2rem;">
    <div class="scenario-card" data-risk="high" data-tip="L'albero attira fulmini. Distanziati almeno 30 metri da alberi isolati.">
      <div class="scenario-icon">🌳</div><div class="scenario-label">Albero isolato</div>
    </div>
    <div class="scenario-card" data-risk="low" data-tip="L'auto è una gabbia di Faraday. Resta dentro con i finestrini chiusi.">
      <div class="scenario-icon">🚗</div><div class="scenario-label">Automobile</div>
    </div>
    <div class="scenario-card" data-risk="low" data-tip="Gli edifici con parafulmini sono sicuri. Allontanati da finestre.">
      <div class="scenario-icon">🏠</div><div class="scenario-label">Edificio</div>
    </div>
    <div class="scenario-card" data-risk="high" data-tip="L'acqua conduce l'elettricità. Esci immediatamente da laghi e piscine.">
      <div class="scenario-icon">🏊</div><div class="scenario-label">Acqua</div>
    </div>
    <div class="scenario-card" data-risk="med" data-tip="Le colline espongono. Accovacciati con i piedi uniti, non sdraiarti.">
      <div class="scenario-icon">⛰️</div><div class="scenario-label">Collina</div>
    </div>
    <div class="scenario-card" data-risk="med" data-tip="Campo aperto: sei il punto più alto. Accovacciati lontano da altri.">
      <div class="scenario-icon">🌾</div><div class="scenario-label">Campo aperto</div>
    </div>
  </div>
  <div id="risk-info" style="min-height:60px;padding:1rem;background:rgba(240,192,64,0.05);border:1px solid var(--border);font-size:.9rem;color:#c0c4d4;">Clicca su uno scenario per vedere i consigli.</div>

  <h3 style="margin-top:2rem;">Calcolatore Distanza Tuono</h3>
  <p style="font-size:.85rem;color:var(--muted);">Quanti secondi tra il lampo e il tuono?</p>
  <input id="thunder-slider" type="range" min="1" max="30" value="5" style="width:100%;margin:.75rem 0;">
  <p id="thunder-result" style="font-family:'Bebas Neue',sans-serif;font-size:2rem;color:var(--gold);">1.7 km</p>
  <p id="thunder-note" style="font-size:.8rem;color:var(--muted);">Il tuono viaggia a ~340 m/s</p>
</div>
```

- [ ] **Step 2: Aggiungere CSS scenari**

Nel `<style>`:
```css
.scenario-card {
  background: var(--surface); border: 2px solid var(--border);
  border-radius: 2px; padding: 1.2rem; text-align: center; cursor: pointer;
  transition: border-color .2s, transform .2s;
}
.scenario-card:hover { transform: translateY(-2px); }
.scenario-card.risk-high { border-color: #e53935; }
.scenario-card.risk-med  { border-color: #f0c040; }
.scenario-card.risk-low  { border-color: #43a047; }
.scenario-icon { font-size: 2rem; display: block; margin-bottom: .4rem; }
.scenario-label { font-family: 'Bebas Neue',sans-serif; letter-spacing: .08em; font-size: .9rem; color: var(--white); }
```

- [ ] **Step 3: Sostituire `function initSafetySimulator() {}` e `function initThunderCalc() {}`**

```js
function initSafetySimulator() {
  const cards = document.querySelectorAll('.scenario-card');
  const info  = document.getElementById('risk-info');
  if (!cards.length) return;
  const colors = { high: '#e53935', med: '#f0c040', low: '#43a047' };
  const labels = { high: '🔴 RISCHIO ALTO', med: '🟡 RISCHIO MEDIO', low: '🟢 RISCHIO BASSO' };
  cards.forEach(card => {
    card.addEventListener('click', () => {
      cards.forEach(c => c.classList.remove('risk-high','risk-med','risk-low'));
      const risk = card.dataset.risk;
      card.classList.add('risk-' + risk);
      info.innerHTML = `<strong style="color:${colors[risk]}">${labels[risk]}</strong><br><br>${card.dataset.tip}`;
    });
  });
}

function initThunderCalc() {
  const slider = document.getElementById('thunder-slider');
  const result = document.getElementById('thunder-result');
  if (!slider) return;
  function update() {
    const sec = +slider.value;
    const km = (sec * 340 / 1000).toFixed(1);
    result.textContent = km + ' km';
    result.style.color = sec <= 5 ? '#e53935' : sec <= 15 ? '#f0c040' : 'var(--gold)';
  }
  slider.addEventListener('input', update);
  update();
}
```

- [ ] **Step 4: Verificare**

Slide S7: 6 scenari cliccabili → bordo colorato + testo consiglio. Slider tuono → km in tempo reale con colore che cambia (rosso = vicino, verde = lontano).

---

### Task 11: Fix visivo — adattare sezioni esistenti come slide

**Files:** Modify `fulmini.html`

- [ ] **Step 1: Fix overflow nelle slide con contenuto lungo**

Le slide `#s-fisica`, `#s-franklin`, `#s-protezione` potrebbero avere overflow. Verificare che il `.container` all'interno abbia `overflow-y: auto` se il contenuto supera 100vh.

Aggiungere nel CSS:
```css
.slide > .container { max-height: 100vh; overflow-y: auto; }
```

- [ ] **Step 2: Fix hero z-index**

Il contenuto dell'hero deve stare sopra il canvas procedurale. Aggiungere nel CSS:
```css
#s-hero .hero-bg,
#s-hero .bolt-bg,
#s-hero .hero-eyebrow,
#s-hero .hero-title,
#s-hero .hero-sub,
#s-hero .hero-scroll { position: relative; z-index: 2; }
```

- [ ] **Step 3: Verificare navigazione completa**

Scorrere dall'hero alla protezione e viceversa. Verificare che tutte le slide siano accessibili, che i tool si inizializzino al primo ingresso, e che non ci siano scroll anomali.

- [ ] **Step 4: Test responsive (resize finestra)**

Ridimensionare la finestra: verificare che le slide rimangano full-height e che i canvas si adattino correttamente.

---

## Self-Review

- ✅ Tutte le 8 slide hanno un task corrispondente
- ✅ Carousel infrastruttura presente in Task 2 — funzione `initCarousel()` scritta, non chiamata
- ✅ Blitzortung ha fallback esplicito con `startFallback()`
- ✅ Nessun TBD o placeholder
- ✅ Tutte le funzioni definite in Task 2 (stub) vengono implementate nei task successivi
- ✅ I nomi di funzione sono consistenti: `initHeroCanvas`, `initCloudCharge`, `initSteppedLeader`, `initTypeVisualizer`, `initPhysicsSliders`, `initLichtenberg`, `initEMPulse`, `initBlitzortung`, `initSafetySimulator`, `initThunderCalc`
- ✅ Leaflet CSS aggiunto in Task 1, Leaflet JS in Task 9 — ordine corretto
