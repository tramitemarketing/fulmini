# Fulmini v2 — Task 5: JS Core (Scroll, VC, HC, UI)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Aggiungere tutto il JS infrastrutturale: sistema scroll con IntersectionObserver, `initVerticalCarousel`, `initHorizontalCarousel`, dispatcher `onSlideEnter`, UI fissa (cursore, progress bar, nav dots, counter, fullscreen, scroll hint), e il slider del tuono.

**Architecture:** Tutto il JS va nel `<script>` già presente in `index.html`, prima della chiusura `</body>`. Le funzioni canvas stub (`initHeroCanvas`, `initCloudCharge`, ecc.) vengono dichiarate come `function initXxx(){}` vuote per non dare errori — saranno implementate nei task 6-8.

**Tech Stack:** Vanilla JS ES6+

**Prerequisito:** Task 1-4 completati (HTML completo in `index.html`)

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezioni 4, 5, 7, 8

---

### Task 1: Sostituisci il blocco `<script>` con il JS completo

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Sostituisci `// JS aggiunto nei task 5-8` nel blocco script con il codice seguente**

Trova `// JS aggiunto nei task 5-8` e sostituisci con:

```js
/* ===== CURSOR ===== */
const cursor = document.getElementById('cursor');
const ring = document.getElementById('cursor-ring');
let mx = 0, my = 0, rx = 0, ry = 0;
document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
(function raf() {
  rx += (mx - rx) * 0.12;
  ry += (my - ry) * 0.12;
  cursor.style.left = mx + 'px'; cursor.style.top = my + 'px';
  ring.style.left = rx + 'px'; ring.style.top = ry + 'px';
  requestAnimationFrame(raf);
})();

/* ===== SLIDES SETUP ===== */
const slides = [...document.querySelectorAll('.slide')];
const N = slides.length;

/* ===== NAV DOTS ===== */
const navDots = document.getElementById('nav-dots');
slides.forEach((_, i) => {
  const d = document.createElement('div');
  d.className = 'ndot' + (i === 0 ? ' active' : '');
  d.addEventListener('click', () => slides[i].scrollIntoView({ behavior: 'smooth' }));
  navDots.appendChild(d);
});
function updateNav(i) {
  document.querySelectorAll('.ndot').forEach((d, j) => d.classList.toggle('active', j === i));
  document.getElementById('slide-counter').textContent =
    String(i + 1).padStart(2, '0') + ' / ' + String(N).padStart(2, '0');
  document.getElementById('progress-bar').style.height = (i / (N - 1) * 100) + '%';
}

/* ===== SCROLL HINT ===== */
const scrollHint = document.getElementById('scroll-hint');
let hintGone = false;
window.addEventListener('scroll', () => {
  if (!hintGone) { scrollHint.style.opacity = '0'; hintGone = true; }
}, { once: true });

/* ===== FULLSCREEN ===== */
document.getElementById('fullscreen-btn').addEventListener('click', () => {
  if (!document.fullscreenElement) document.documentElement.requestFullscreen();
  else document.exitFullscreen();
});
document.addEventListener('keydown', e => {
  if (e.key === 'f' || e.key === 'F') document.getElementById('fullscreen-btn').click();
});

/* ===== VERTICAL CAROUSEL ===== */
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
  let ty = 0;
  slideEl.addEventListener('touchstart', e => { ty = e.touches[0].clientY; }, { passive: true });
  slideEl.addEventListener('touchend', e => {
    const dy = e.changedTouches[0].clientY - ty;
    if (dy < -50 && idx < panels.length - 1) go(idx + 1);
    else if (dy > 50 && idx > 0) go(idx - 1);
  });
  return {
    atTop: () => idx === 0,
    atBottom: () => idx === panels.length - 1,
    next: () => go(idx + 1),
    prev: () => go(idx - 1),
    reset: () => go(0)
  };
}

/* ===== HORIZONTAL CAROUSEL ===== */
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
  return { go, getIdx: () => idx };
}

/* ===== THUNDER SLIDER ===== */
const tSlider = document.getElementById('thunder-slider');
const tOut = document.getElementById('thunder-out');
if (tSlider && tOut) {
  tSlider.addEventListener('input', () => {
    tOut.textContent = (tSlider.value / 3).toFixed(1) + ' km';
  });
}

/* ===== SAFETY SIMULATOR ===== */
function initSafetySimulator() {
  const items = document.querySelectorAll('.safety-item');
  const tip = document.getElementById('safety-tip');
  if (!tip) return;
  items.forEach(item => {
    item.addEventListener('click', () => {
      items.forEach(i => i.style.outline = 'none');
      item.style.outline = '1px solid var(--gold)';
      tip.textContent = item.dataset.tip || '';
    });
  });
}

/* ===== CANVAS STUBS (implementati nei task 6-8) ===== */
function initHeroCanvas() {}
function initCloudCharge() {}
function initFormationCanvas() {}
function initSteppedLeader() {}
function initEMPulse() {}
function initThunderCalc() {}
function initThunderCanvas() {}
function initTypeVisualizer() {}
function initTLEVisualizer() {}
function initBlitzortung() {}

/* ===== onSlideEnter DISPATCHER ===== */
const triggered = new Set();
const vcControls = [];
const inits = [
  initHeroCanvas,
  initCloudCharge,
  () => { initFormationCanvas(); initSteppedLeader(); },
  initEMPulse,
  () => { initThunderCalc(); initThunderCanvas(); },
  () => { initTypeVisualizer(); initTLEVisualizer(); },
  () => {},
  () => {},
  () => {},
  () => { initBlitzortung(); initSafetySimulator(); },
];

function onSlideEnter(i) {
  updateNav(i);
  if (!triggered.has(i)) {
    triggered.add(i);
    inits[i]?.();
  }
}

/* ===== INIT ALL VC ===== */
slides.forEach((s, i) => {
  const vc = initVerticalCarousel(s);
  vcControls[i] = vc;
});

/* ===== INTERSECTION OBSERVER ===== */
const io = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting && e.intersectionRatio >= 0.5) {
      const i = slides.indexOf(e.target);
      if (i >= 0) onSlideEnter(i);
    }
  });
}, { threshold: 0.5 });
slides.forEach(s => io.observe(s));

/* ===== KEYBOARD NAV ===== */
document.addEventListener('keydown', e => {
  const curr = [...document.querySelectorAll('.ndot')].findIndex(d => d.classList.contains('active'));
  if (e.key === 'ArrowDown' && curr < N - 1) slides[curr + 1].scrollIntoView({ behavior: 'smooth' });
  if (e.key === 'ArrowUp' && curr > 0) slides[curr - 1].scrollIntoView({ behavior: 'smooth' });
});

/* ===== INIT S0 IMMEDIATELY ===== */
onSlideEnter(0);
```

- [ ] **Step 2: Verifica nel browser**

Apri `index.html`. Verifica:
- Cursore oro personalizzato visibile
- Scroll snap funzionante (ogni scroll = slide successiva)
- Nav dots a destra si aggiornano ad ogni slide
- Counter "01 / 10" aggiornato
- Progress bar sinistra si riempie
- Bottone fullscreen funzionante
- Scroll hint scompare al primo scroll
- Nessun errore in console

- [ ] **Step 3: Verifica HC**

Vai a S2 (formazione). Clicca le tab [A. STEPPED LEADER] [B. STREAMER] ecc. → i pannelli HC devono scorrere. Stesso test su S5 (CG⁻/CG⁺/IC/CC) e (BALL/SPRITE/ELVES/BLUE JET).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add core JS (VC, HC, scroll, fixed UI, dispatcher)"
```
