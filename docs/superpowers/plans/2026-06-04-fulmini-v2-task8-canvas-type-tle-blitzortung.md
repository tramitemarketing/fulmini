# Fulmini v2 — Task 8: Canvas — Type, TLE, Blitzortung

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implementare `initTypeVisualizer` (S5-P1, sincronizzato con HC CG), `initTLEVisualizer` (S5-P2, sincronizzato con HC TLE) e `initBlitzortung` (S9-P1, Leaflet map + WebSocket Blitzortung con timeout fallback).

**Architecture:** Type/TLE visualizer sincronizzano il canvas con la tab HC attiva. Blitzortung: mappa Leaflet dark, 4 circleMarker hotspot, contatore WebSocket con fallback simulato dopo 5s timeout.

**Tech Stack:** Canvas API 2D, Leaflet.js (già importato), WebSocket API, vanilla JS

**Prerequisito:** Task 5-7 completati

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezione 6 e S9-P1

---

### Task 1: Implementa initTypeVisualizer

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Trova `function initTypeVisualizer() {}` e sostituisci con:**

```js
function initTypeVisualizer() {
  const canvas = document.getElementById('type-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 300;
  canvas.height = canvas.offsetHeight || 380;
  const W = canvas.width, H = canvas.height;

  const hcWrap = document.getElementById('cg-hc');
  const hcObj = initHorizontalCarousel(hcWrap);

  let currentType = 0;
  let frame = 0;

  // Types: 0=CG-, 1=CG+, 2=IC, 3=CC
  const typeColors = ['#f0c040', '#ff8040', '#3a8fff', '#a060ff'];
  const typeLabels = ['CG⁻', 'CG⁺', 'IC', 'CC'];

  function drawCGDown(color, label) {
    // Cloud top
    ctx.beginPath();
    ctx.ellipse(W / 2, 50, 70, 28, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.95)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,.3)'; ctx.lineWidth = 1; ctx.stroke();
    ctx.fillStyle = 'rgba(58,143,255,.5)';
    ctx.font = '8px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('−', W / 2, 55);

    // Bolt: top→bottom
    const progress = (Math.sin(frame * 0.03) + 1) / 2;
    if (progress > 0.5) {
      ctx.strokeStyle = color;
      ctx.lineWidth = 2 + (progress - 0.5) * 4;
      ctx.shadowColor = color;
      ctx.shadowBlur = 15 * (progress - 0.5) * 2;
      ctx.beginPath();
      ctx.moveTo(W / 2, 78);
      ctx.lineTo(W / 2 + 15, H * 0.45);
      ctx.lineTo(W / 2 - 8, H * 0.45);
      ctx.lineTo(W / 2 + 5, H - 30);
      ctx.stroke();
      ctx.shadowBlur = 0;
    }

    // Ground
    ctx.fillStyle = 'rgba(122,128,153,0.15)';
    ctx.fillRect(0, H - 28, W, 28);
    ctx.fillStyle = 'rgba(240,192,64,.4)';
    ctx.font = '9px sans-serif';
    for (let x = 12; x < W; x += 22) ctx.fillText('+', x, H - 10);

    // Label
    ctx.fillStyle = color;
    ctx.font = 'bold 14px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText(label, W / 2, H - 5);
  }

  function drawIC(color) {
    // Two clouds
    ctx.beginPath();
    ctx.ellipse(W / 2, 60, 65, 25, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.95)'; ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,.3)'; ctx.stroke();
    // Bolt inside cloud
    const alpha = (Math.sin(frame * 0.05) + 1) / 2;
    ctx.strokeStyle = `rgba(58,143,255,${0.4 + alpha * 0.6})`;
    ctx.lineWidth = 2;
    ctx.shadowColor = '#3a8fff';
    ctx.shadowBlur = 10 * alpha;
    ctx.beginPath();
    ctx.moveTo(W / 2 - 25, 50);
    ctx.lineTo(W / 2, 65);
    ctx.lineTo(W / 2 - 10, 65);
    ctx.lineTo(W / 2 + 20, 80);
    ctx.stroke();
    ctx.shadowBlur = 0;
    ctx.fillStyle = color;
    ctx.font = 'bold 14px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('IC', W / 2, H / 2 + 20);
    ctx.fillStyle = 'rgba(58,143,255,.4)';
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.fillText('INTRA-CLOUD · PIÙ FREQUENTE', W / 2, H / 2 + 38);
  }

  function drawCC(color) {
    // Two clouds side by side
    ctx.beginPath();
    ctx.ellipse(W * 0.28, H * 0.3, 50, 22, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.95)'; ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,.3)'; ctx.stroke();
    ctx.beginPath();
    ctx.ellipse(W * 0.72, H * 0.3, 50, 22, 0, 0, Math.PI * 2);
    ctx.fill(); ctx.stroke();
    // Horizontal bolt
    const alpha = (Math.sin(frame * 0.04) + 1) / 2;
    if (alpha > 0.3) {
      ctx.strokeStyle = `rgba(160,96,255,${alpha})`;
      ctx.lineWidth = 2;
      ctx.shadowColor = '#a060ff';
      ctx.shadowBlur = 12 * alpha;
      ctx.beginPath();
      ctx.moveTo(W * 0.28 + 50, H * 0.3);
      ctx.lineTo(W / 2, H * 0.3 - 10);
      ctx.lineTo(W / 2 + 5, H * 0.3 + 8);
      ctx.lineTo(W * 0.72 - 50, H * 0.3);
      ctx.stroke();
      ctx.shadowBlur = 0;
    }
    ctx.fillStyle = color;
    ctx.font = 'bold 14px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('CC', W / 2, H / 2 + 20);
  }

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.35)';
    ctx.fillRect(0, 0, W, H);
    frame++;

    // Sync with HC
    if (hcWrap) {
      const tabs = [...hcWrap.querySelectorAll('.hc-tab')];
      const active = tabs.findIndex(t => t.classList.contains('active'));
      if (active >= 0) currentType = active;
    }

    if (currentType === 0) drawCGDown(typeColors[0], 'CG⁻ NEGATIVO');
    else if (currentType === 1) drawCGDown(typeColors[1], 'CG⁺ POSITIVO');
    else if (currentType === 2) drawIC(typeColors[2]);
    else drawCC(typeColors[3]);

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Vai a S5-P1. Clicca le tab CG⁻/CG⁺/IC/CC → il canvas deve cambiare animazione per ogni tipo.

---

### Task 2: Implementa initTLEVisualizer

- [ ] **Step 1: Trova `function initTLEVisualizer() {}` e sostituisci con:**

```js
function initTLEVisualizer() {
  const canvas = document.getElementById('tle-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 300;
  canvas.height = canvas.offsetHeight || 380;
  const W = canvas.width, H = canvas.height;

  const hcWrap = document.getElementById('tle-hc');
  const hcObj = initHorizontalCarousel(hcWrap);
  let currentTLE = 0;
  let frame = 0;

  function drawBall() {
    // Floating orb
    const y = H / 2 + Math.sin(frame * 0.02) * 20;
    const r = 22 + Math.sin(frame * 0.03) * 5;
    const grad = ctx.createRadialGradient(W / 2, y, 0, W / 2, y, r * 1.5);
    grad.addColorStop(0, 'rgba(255,200,100,0.95)');
    grad.addColorStop(0.5, 'rgba(255,160,60,0.6)');
    grad.addColorStop(1, 'rgba(255,100,20,0)');
    ctx.beginPath();
    ctx.arc(W / 2, y, r * 1.5, 0, Math.PI * 2);
    ctx.fillStyle = grad;
    ctx.fill();
    ctx.beginPath();
    ctx.arc(W / 2, y, r, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(255,220,120,0.85)';
    ctx.fill();
    ctx.fillStyle = 'rgba(255,200,80,.6)';
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('BALL LIGHTNING', W / 2, H - 20);
  }

  function drawSprite() {
    // Red jellyfish shape
    const alpha = 0.7 + 0.3 * Math.sin(frame * 0.08);
    ctx.strokeStyle = `rgba(255,80,100,${alpha})`;
    ctx.lineWidth = 1.5;
    ctx.shadowColor = '#ff5060';
    ctx.shadowBlur = 12;
    // Body (ellipse)
    ctx.beginPath();
    ctx.ellipse(W / 2, H * 0.35, 35, 20, 0, 0, Math.PI * 2);
    ctx.stroke();
    // Tentacles
    for (let t = 0; t < 6; t++) {
      const tx = W / 2 - 30 + t * 12;
      ctx.beginPath();
      ctx.moveTo(tx, H * 0.35 + 20);
      ctx.bezierCurveTo(tx + (Math.random() - 0.5) * 15, H * 0.55, tx + (Math.random() - 0.5) * 15, H * 0.65, tx + (Math.random() - 0.5) * 10, H * 0.75);
      ctx.stroke();
    }
    ctx.shadowBlur = 0;
    ctx.fillStyle = 'rgba(255,80,100,.5)';
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('SPRITE · 50–90 KM', W / 2, H - 20);
  }

  function drawElves() {
    // Expanding ring
    const progress = (frame % 80) / 80;
    const r = 10 + progress * 90;
    const alpha = 1 - progress;
    ctx.beginPath();
    ctx.arc(W / 2, H / 2, r, 0, Math.PI * 2);
    ctx.strokeStyle = `rgba(180,230,255,${alpha * 0.8})`;
    ctx.lineWidth = 2;
    ctx.shadowColor = '#b4e6ff';
    ctx.shadowBlur = 10;
    ctx.stroke();
    ctx.shadowBlur = 0;
    ctx.fillStyle = 'rgba(180,230,255,.4)';
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('ELVES · ~400 KM', W / 2, H - 20);
  }

  function drawBlueJet() {
    // Blue cone going up
    const progress = (frame % 60) / 60;
    const topY = H * 0.7 - progress * H * 0.55;
    const alpha = 0.5 + 0.5 * Math.sin(frame * 0.1);
    ctx.beginPath();
    ctx.moveTo(W / 2, H * 0.75);
    ctx.lineTo(W / 2 - 30 * (1 - progress), topY);
    ctx.lineTo(W / 2 + 30 * (1 - progress), topY);
    ctx.closePath();
    ctx.fillStyle = `rgba(60,160,255,${alpha * 0.4})`;
    ctx.fill();
    ctx.strokeStyle = `rgba(60,160,255,${alpha * 0.8})`;
    ctx.lineWidth = 1.5;
    ctx.shadowColor = '#3ca0ff';
    ctx.shadowBlur = 10;
    ctx.stroke();
    ctx.shadowBlur = 0;
    ctx.fillStyle = 'rgba(60,160,255,.5)';
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('BLUE JET · 40–50 KM', W / 2, H - 20);
  }

  const fns = [drawBall, drawSprite, drawElves, drawBlueJet];

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.4)';
    ctx.fillRect(0, 0, W, H);
    frame++;
    if (hcWrap) {
      const tabs = [...hcWrap.querySelectorAll('.hc-tab')];
      const active = tabs.findIndex(t => t.classList.contains('active'));
      if (active >= 0) currentTLE = active;
    }
    fns[currentTLE]?.();
    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Vai a S5-P2. Clicca BALL/SPRITE/ELVES/BLUE JET → ogni tab deve mostrare un'animazione diversa nel canvas.

---

### Task 3: Implementa initBlitzortung

- [ ] **Step 1: Trova `function initBlitzortung() {}` e sostituisci con:**

```js
function initBlitzortung() {
  const mapDiv = document.getElementById('map');
  if (!mapDiv || mapDiv._leaflet_id) return;

  const map = L.map('map', { zoomControl: true, attributionControl: false })
    .setView([20, 10], 3);

  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
    maxZoom: 19
  }).addTo(map);

  const hotspots = [
    { latlng: [9.75, -71.6], name: 'Lago Maracaibo', desc: '230–260 fulmini/km²/anno · 297 notti' },
    { latlng: [-2.5, 27.5],  name: 'Congo / Kifuka',  desc: '205 fulmini/km²/anno · foresta equatoriale' },
    { latlng: [27.9, -82.5], name: 'Tampa, Florida',  desc: '1,2M fulmini/anno · convergenza brezze marine' },
    { latlng: [45.5, 9.2],   name: 'Pianura Padana',  desc: 'Hotspot autunnale · convezione violenta' },
  ];

  hotspots.forEach(h => {
    L.circleMarker(h.latlng, {
      radius: 10,
      color: '#f0c040',
      fillColor: 'rgba(240,192,64,0.2)',
      fillOpacity: 1,
      weight: 1.5
    }).bindPopup(`<strong style="color:#f0c040;">${h.name}</strong><br><span style="font-size:.75rem;">${h.desc}</span>`)
      .addTo(map);
  });

  // WebSocket Blitzortung
  const counter = document.getElementById('map-counter');
  let count = 0;
  let ws = null;
  let wsConnected = false;

  function updateCounter() {
    if (counter) counter.textContent = count + ' FULMINI NEGLI ULTIMI 5 MIN';
  }

  // Try WebSocket connection with 5s timeout fallback
  try {
    ws = new WebSocket('wss://ws1.blitzortung.org:8082/');
    const wsTimeout = setTimeout(() => {
      if (!wsConnected) {
        ws?.close();
        startSimulation();
      }
    }, 5000);

    ws.onopen = () => {
      wsConnected = true;
      clearTimeout(wsTimeout);
      ws.send(JSON.stringify({ west: -180, east: 180, south: -90, north: 90 }));
    };
    ws.onmessage = () => {
      count++;
      updateCounter();
    };
    ws.onerror = () => {
      clearTimeout(wsTimeout);
      startSimulation();
    };
  } catch (e) {
    startSimulation();
  }

  function startSimulation() {
    if (counter) counter.textContent = '≈ SIMULAZIONE DATI STORICI';
    setInterval(() => {
      count += Math.floor(Math.random() * 3) + 1;
      updateCounter();
    }, 800);
  }

  updateCounter();
}
```

- [ ] **Step 2: Verifica**

Vai a S9-P1. La mappa Leaflet dark deve caricarsi con 4 marker dorati (Maracaibo, Congo, Florida, Padana). Clicca i marker → popup con dati. Il contatore in alto deve aggiornarsi (WebSocket reale o simulato).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: implement canvas type, TLE, Blitzortung map"
```
